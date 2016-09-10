---
layout: post
title: "Java 并发编程笔记：如何让一个线程停下来"
date: 2016-09-09 21:51
comments: true
categories: 
---

# 前言

这几天趁辞职和下一份工作的间隙，考虑把 Java 的基础再好好补一下。提到 Java 的进阶使用，并发编程自然是无法绕过的一个主题。在上一份工作中初次接触了 Java，但在实际工作中使用到并发编程的次数屈指可数，所以尽管日常的使用或许没有问题，但对实现细节了解不深，还不能做到“知其所以然”。这一系列文章——尽管在写下这句话时还只有这第一篇文章，但我希望自己能够坚持写成一个系列——是我在探索“所以然”过程中的思考和记录，希望对后来人能够有帮助吧。

因为这篇文章的初衷是学习笔记而非教程，所以不会从创建10个 Thread 打印10句 "Hello, world!" 开始，而是在每篇文章中选取一个较小的主题，结合自己阅读 JDK 源代码的心得和网上搜索到的资料写成。由于 Java 是个拥有20年历史的古老项目，而且特别注重向后兼容性，因而有新旧 API 共存、某些设计反直觉、设计思想不统一等不利于后来人理解的因素，我也希望能够尽可能从历史的角度来解释“所以然”。

当然因为止于 Java 我还是初学者，而文章里涉及历史时也包含推测的部分，如果在阅读中发现有错误和缺陷的话，还请大家留言指正。

以上差不多是写下这篇文章的初衷，我们开始吧。

# 如何让一个线程停下来

## 问题

并发和锁，就像孪生子一样是离不开的两个概念。很少有程序可以在没有锁控制的情况下完成一些有意义的工作，最常见的使用多线程的情况就是：

1. 父线程把任务分割成 n 部分。
2. 分别把 n 个部分分配给 n 个线程。
3. 等待 n 个线程处理完成。
4. 验收子线程的结果，进行下一步处理。

其中的第3步——“等待”，就离不开锁。或者反过来说，锁的意义就在于“等待”，是为了控制线程之间的执行次序。

于是问题来了，**Java 里面有多少种让线程停下来的方法呢？**

<!--more-->

## 答案

答案是我也不知道 :) 因为在 JDK 提供的 `wait()`、`park()`、`join()`、`lock()` 的基础上可以自由地构造阻塞线程的函数，所以可以说有无数种办法让线程停下来，但是在这里我把 JDK 最常用的几种方法以及它们的特性整理成了一张表。这篇文章会重点关注这些 Java 锁机制的基础函数，并且稍候会详细解释一下它们的实现方法。

| 函数                 | 版本 | 消耗 CPU | 能否被 Interrupt | 核心方法       | 线程状态      | 备注                                         
|----------------------|------|----------|------------------|----------------|---------------|----------------------------------------------
| spinlock             | 1.0  | 是       | 否               | native         | RUNNABLE      |                                              
| wait()               | 1.0  | 否       | 是               | native         | WAITING       |                                              
| LockSupport.park()   | 1.5  | 否       | 是               | native         | WAITING       |                                              
| sleep()              | 1.0  | 否       | 是               | native         | TIMED_WAITING |                                              
| join()               | 1.0  | 否       | 是               | wait()         | WAITING       |                                              
| suspend()            | 1.0  | 否       | 否               | native         | WAITING       | 已弃用                                       
| ReentrantLock.lock() | 1.5  | 部分是   | 否               | park()         | WAITING       | 有可 Interrupt 版本 lockInterruptibly()      
| Condition.await()    | 1.5  | 否       | 是               | park()         | WAITING       | 有不可 Interrupt 版本 awaitUninterruptibly() 
| FutureTask.get()     | 1.5  | 否       | 是               | park()         | WAITING       |                                              


以上函数在没有特别注明 Class 名时，均指 Thread 对象的方法。

首先介绍最核心的三种阻塞方法：`Spinlock`、`wait()`、`LockSupport.park()`，其他阻塞线程的方法大多是通过调用这三个方法来实现的。

### Spinlock（自旋锁）

自旋锁是最简单的一种阻塞线程的办法，就是在一个死循环里不断检查等待的条件是否满足，直至满足之后才跳出循环。很显然，**自旋锁的最大缺点是白白消耗计算资源**，并且把调度的责任完全交给了线程调度器。但是**自旋锁最大的优点就是减少线程切换的次数**。因为线程切换需要CPU从用户态进入核心态，是非常昂贵的操作。特别是在等待时间较短时，频繁地切换线程的运行状态可能得不偿失。

下面是一段最简单的自旋锁实现

```java
for (;;) {
    if (condition == true)
        break;
}

// do stuff
```

### wait()

`wait()` 是从 Java 1.0 开始就存在的老牌“等待”函数，在 Java 1.5 以前是最主要的一类用于线程间进行同步的方法。

`wait()` 的使用方法相对比较“怪异”。首先调用 `wait()` 的线程需要获得一个用于线程间共享的对象的“锁”（在 Java 术语中称为“监视器”），然后调用 `wait()` 会首先释放这把锁，并将当前线程暂停，只有在其他进程通过调用共享对象的 `notify()` 或者 `notifyAll()` 时才会醒来。但是醒来之后也不是说立即就会得到执行，只是线程会重新加入对锁对象的竞争，只有竞争胜出之后才会获得运行权。

典型的使用 `wait()` 函数的代码是这样的

```java
// 等待者(Thread1)
synchronized (lock) {
    while (condition != true) {
        lock.wait()
    }

    // do stuff
}

// 唤醒者(Thread2)
synchronized (lock) {
    condition = true;
    lock.notify();
}
```

#### 为什么 `wait()` 需要配合 `synchronized` 使用？

在 stackoverflow 上有个[帖子](http://stackoverflow.com/questions/2779484/why-must-wait-always-be-in-synchronized-block)对这个问题进行了讨论。我认为最主要的原因是为了防止以下这种情况

```java
// 等待者(Thread1)
while (condition != true) { // step.1
    lock.wait() // step.4
}

// 唤醒者(Thread2)
condition = true; // step.2
lock.notify(); // step.3
```

在对之前的代码去掉 `synchronized` 块之后，如果在等待者**判断 `condition != true` 之后而调用 `wait()` 之前**，唤醒者**将 `condition` 修改成了 `true` 同时调用了 `notify()` **的话，那么等待者在调用了 `wait()` 之后就没有机会被唤醒了。

### LockSupport.park()

`LockSupport.park()`（以下简称 `park()` ）可能是 Java 1.5 加入 `java.util.concurrent` 包时最重要的函数了，因为很多 `java.util.concurrent` 中的功能类都是利用 `park()` 来实现它们各自的阻塞。在 `park()` 之前 Java 也有过类似功能的函数——`suspend()`，相应的唤醒函数是 `resume()`。不过 `suspend()` 有个严重的问题是父线程有可能在调用 `suspend()` 之前子线程已经调用了 `resume()`，那么这个 `resume()` 并不会解除在它之后的 `suspend()`，因此父线程就会陷入永久的等待中。相比于 `suspend()`，`park()` 可以在以下几种情况解除线程的等待状态：

1. 在 `park()` 前曾经调用过该线程的 `unpark()` 进而获得了**一次**“继续执行的权利”，此时调用 `park()` 会立即返回，并且消耗掉相应的“继续执行的权利”。
2. 在 `park()` 进入等待状态之后，有其他线程以该线程为目标调用 `unpark()`。
2. 在 `park()` 进入等待状态之后，有其他线程以该线程为目标调用 `interrupt()`。
2. 在 `park()` 进入等待状态之后，**有可能**会没有理由地解除等待状态。（这也是为什么推荐在循环体中调用 `park()`，并在返回之后再次检查条件是否满足。）

其中第一条就可以保证 `park()` 不会遇到和 `suspend()` 同样的问题。

最简单的使用 `park()` 是这样的

```java
// 等待者(Thread1)
while (condition != true) {
    LockSupport.park();
}

// 唤醒者(Thread2)
condition = true;
LockSupport.unpark(Thread1);
```

以上三个函数是 Java 1.5 之后最基本也是最核心的使得线程进入等待状态的方法，接下来介绍在这三个方法的基础上实现，但也很常用的一些阻塞线程的方法。

### join()

`join()` 也是和 `wait()` 一样的老牌等待函数，其内部使用了 `wait()` 实现等待功能。因为线程在运行结束时会调用 `this.notifyAll()`，所以相应地就会唤醒使用 `join()` 开始等待的线程。

已最开始提到的“父线程等待子线程完成任务”的问题为例，典型的使用 `join()` 的代码如下

```java
// spawn a child thread and start it
Thread child = new Thread(...);
child.start();

// parent thread continues waiting
child.join();
```

### ReentrantLock.lock()

这是随着 `java.util.concurrent` 包同时加入的泛用的锁对象，用于用户实现自己的线程同步策略，特别是那些很难用 `synchronized` 原语表达的情况（例如对一个双向链表进行并发地更新）。内部使用 `park()` 来实现等待。

`lock()` 使用方法比较直观，最简单的实例代码如下

```java
final Lock lock = new ReentrantLock();
try {
    lock.lock();

    // do stuff

} finally {
    lock.unlock();
}
```

### Condition.await()

`Condition` 类也是在 Java 1.5 之后新加入的并发控制类。如果说 `Lock` 是用来替代 `synchronized` 的话，那么 `Condition` 就是用来替代 `wait()`、`notify()` 和 `notifyAll()` 的，相应的函数名分别是 `await()`、`signal()` 和 `signalAll()`。注意到因为 `wait()` 是 `Object` 下的函数，所以 `Condition` 自然也有它的 `wait()`，为了不重名只好把新的函数命名成为 `await()`，`signal()` 还有 `signalAll()` 也是基于同样的理由。

内部同样使用 `park()` 实现等待。

### Future.get()

使用过 `ExecutorService` 或者 NIO 的话一定对 `Future` 不会陌生，而 `Future` 的 `get()` 是阻塞方法，内部也是使用 `park()` 来阻塞调用者的线程。

## 总结

Java 尽管语言的设计目标中包括了对多线程的原生支持，但初期的并发控制原语和函数 `synchronized`、`wait()`、`notify()` 的使用方法并不太友好，而且由于有时不够灵活的缘故，在设计粒度更细的并发控制机制时可能会捉襟见肘。自从 Java 1.5 引入了 `java.util.concurrent` 之后才大幅好转。以上介绍的较为底层的函数诸如 `park()`、`lock()` 可能大部分时候不需要和它们直接打交道，但是在使用在这些函数基础上构建出来的 `Semaphore`、`CountDownLatch`、`CyclicBarrier` 时，如果能够对底层细节“知其所以然”的话，相信能够更好地理解为何需要这么写，以及出现问题时也能够及时地理解问题的根源。
