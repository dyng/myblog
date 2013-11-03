---
layout: post
title: "一次格式化字符串攻击（上）"
date: 2013-11-03 10:22
comments: true
categories: 技术
---
最近大部分时间都花在做[这组题](http://ksnctf.sweetduet.info)上了（当然还有[GTA5](http://en.wikipedia.org/wiki/Grand_Theft_Auto_V)，不愧是2.6亿打造出来的游戏啊），题目的类型很丰富，涉及密码破解、反汇编、sql注入、hash碰撞等等，而且大部分都包含提示，搜索一下关键词就能找到破解的线索，或者真不济……还有公司里同事所写的[解题报告](http://ichirin2501.hatenablog.com/entry/2012/06/11/001540) :)

今天在这里主要记载一下[第四题](http://ksnctf.sweetduet.info/problem/4)的解题过程，因为在这题上花费了最多的时间，而且又是我比较喜欢的汇编级别的漏洞利用，当然最重要的是，借由这题了解了一种过去不知道的攻击方式——格式化字符串攻击（format string attack）。

注：本文需要一定程度的汇编知识，虽然尽可能地做到详细解释，不过对汇编和c语言的汇编表示不太熟悉的朋友强烈推荐看一下[CSAPP](http://book.douban.com/subject/5407246/)的第三章。

<!-- more -->

## 问题

这一题的背景是这样的：

以q4用户登录服务器之后，可以发现home目录下有两个文件，其中flag.txt包含本次破解的密码，但是owner是q4a用户并且权限是owner只读，另一个是可执行文件q4，owner是q4a但是所有用户均可执行，这就是需要破解的对象了。

程序的运行结果是这样的：

```text
vagrant@lucid32:/vagrant/q4-crack$ ./q4
What's your name?
foo
Hi, foo

Do you want the flag?
yes
Do you want the flag?
yes
Do you want the flag?
I neeeeeeeed it!
Do you want the flag?
no
I see. Good bye.
vagrant@lucid32:/vagrant/q4-crack$
```

对q4反汇编之后得到如下的汇编代码：

```text
80485b4 <main>:
80485b4:       55                      push   %ebp
80485b5:       89 e5                   mov    %esp,%ebp
80485b7:       83 e4 f0                and    $0xfffffff0,%esp
80485ba:       81 ec 20 04 00 00       sub    $0x420,%esp
80485c0:       c7 04 24 a4 87 04 08    movl   $0x80487a4,(%esp)
80485c7:       e8 f8 fe ff ff          call   80484c4 <puts@plt>
80485cc:       a1 04 9a 04 08          mov    0x8049a04,%eax
80485d1:       89 44 24 08             mov    %eax,0x8(%esp)
80485d5:       c7 44 24 04 00 04 00    movl   $0x400,0x4(%esp)
80485dc:       00
80485dd:       8d 44 24 18             lea    0x18(%esp),%eax
80485e1:       89 04 24                mov    %eax,(%esp)
80485e4:       e8 9b fe ff ff          call   8048484 <fgets@plt>
80485e9:       c7 04 24 b6 87 04 08    movl   $0x80487b6,(%esp)
80485f0:       e8 bf fe ff ff          call   80484b4 <printf@plt>
80485f5:       8d 44 24 18             lea    0x18(%esp),%eax
80485f9:       89 04 24                mov    %eax,(%esp)
80485fc:       e8 b3 fe ff ff          call   80484b4 <printf@plt>
8048601:       c7 04 24 0a 00 00 00    movl   $0xa,(%esp)
8048608:       e8 67 fe ff ff          call   8048474 <putchar@plt>
804860d:       c7 84 24 18 04 00 00    movl   $0x1,0x418(%esp)
8048614:       01 00 00 00
8048618:       eb 67                   jmp    8048681 <main+0xcd>
804861a:       c7 04 24 bb 87 04 08    movl   $0x80487bb,(%esp)
8048621:       e8 9e fe ff ff          call   80484c4 <puts@plt>
8048626:       a1 04 9a 04 08          mov    0x8049a04,%eax
804862b:       89 44 24 08             mov    %eax,0x8(%esp)
804862f:       c7 44 24 04 00 04 00    movl   $0x400,0x4(%esp)
8048636:       00
8048637:       8d 44 24 18             lea    0x18(%esp),%eax
804863b:       89 04 24                mov    %eax,(%esp)
804863e:       e8 41 fe ff ff          call   8048484 <fgets@plt>
8048643:       85 c0                   test   %eax,%eax
8048645:       0f 94 c0                sete   %al
8048648:       84 c0                   test   %al,%al
804864a:       74 0a                   je     8048656 <main+0xa2>
804864c:       b8 00 00 00 00          mov    $0x0,%eax
8048651:       e9 86 00 00 00          jmp    80486dc <main+0x128>
8048656:       c7 44 24 04 d1 87 04    movl   $0x80487d1,0x4(%esp)
804865d:       08
804865e:       8d 44 24 18             lea    0x18(%esp),%eax
8048662:       89 04 24                mov    %eax,(%esp)
8048665:       e8 7a fe ff ff          call   80484e4 <strcmp@plt>
804866a:       85 c0                   test   %eax,%eax
804866c:       75 13                   jne    8048681 <main+0xcd>
804866e:       c7 04 24 d5 87 04 08    movl   $0x80487d5,(%esp)
8048675:       e8 4a fe ff ff          call   80484c4 <puts@plt>
804867a:       b8 00 00 00 00          mov    $0x0,%eax
804867f:       eb 5b                   jmp    80486dc <main+0x128>
8048681:       8b 84 24 18 04 00 00    mov    0x418(%esp),%eax
8048688:       85 c0                   test   %eax,%eax
804868a:       0f 95 c0                setne  %al
804868d:       84 c0                   test   %al,%al
804868f:       75 89                   jne    804861a <main+0x66>
8048691:       c7 44 24 04 e6 87 04    movl   $0x80487e6,0x4(%esp)
8048698:       08
8048699:       c7 04 24 e8 87 04 08    movl   $0x80487e8,(%esp)
80486a0:       e8 ff fd ff ff          call   80484a4 <fopen@plt>
80486a5:       89 84 24 1c 04 00 00    mov    %eax,0x41c(%esp)
80486ac:       8b 84 24 1c 04 00 00    mov    0x41c(%esp),%eax
80486b3:       89 44 24 08             mov    %eax,0x8(%esp)
80486b7:       c7 44 24 04 00 04 00    movl   $0x400,0x4(%esp)
80486be:       00
80486bf:       8d 44 24 18             lea    0x18(%esp),%eax
80486c3:       89 04 24                mov    %eax,(%esp)
80486c6:       e8 b9 fd ff ff          call   8048484 <fgets@plt>
80486cb:       8d 44 24 18             lea    0x18(%esp),%eax
80486cf:       89 04 24                mov    %eax,(%esp)
80486d2:       e8 dd fd ff ff          call   80484b4 <printf@plt>
80486d7:       b8 00 00 00 00          mov    $0x0,%eax
80486dc:       c9                      leave
80486dd:       c3                      ret
80486de:       90                      nop
80486df:       90                      nop
```

这是一段比较简单易懂的汇编代码（而且最重要的是还很短！），仔细阅读之后，基本可以还原出源代码：

```c
#include <stdio.h>
#include <string.h>

int main(int argc, char *argv[])
{
    int flag;
    char buf[1024];
    FILE* f;

    puts("What's your name?");

    fgets(buf, 1024, stdin);
    printf("Hi, ");
    printf(buf);
    putchar('\n');

    flag = 1;
    while (flag == 1){
        puts("Do you want the flag?");

        if (fgets(buf, 1024, stdin) == NULL)
            return 0;

        if (!strcmp(buf, "no\n")){
            puts("I see. Good bye.");
            return 0;
        }
    }

    f = fopen("/home/q4a/flag.txt", "r");
    fgets(buf, 1024, f);
    printf(buf);

    return 0;
}
```

可以发现控制程序流程的是关键变量flag，但在程序内部却没有任何能够改变flag值的办法，因此程序会进入死循环，永远不会走到打开flag.txt文件的第29行。同时大家应该也会注意到第13行至第15行写得很怪异，最自然的写法应该是：
```c
printf("Hi, %s\n", buf);
```
但原文却没有选择这样写，而是分为了三段，而这正是破解的关键。注意到第14行printf(buf) ，这是一句将用户输入直接打印出来的语句，相信做惯了互联网开发的同学会直觉性地察觉到这就像把用户输入直接插入sql查询语句中一样散发着异味。对于普通的字符串，printf(buf)和printf("%s", buf)确实是相同的，但如果字符串中包含 %d 和 %x 这样的特殊字符呢？
``` text
vagrant@lucid32:/vagrant/q4-crack$ ./q4
What's your name?
%d
Hi, 1024
```
printf打印出来的不再是我们的输入，而是一个看起来似乎有意义的数字1024。为什么呢？这就涉及格式化字符串攻击的原理了。

## 原理

printf有三个特性，最终使得格式化字符串攻击成为可能。其中第一条就是：

> 当printf的format string是一个用户可控的字符串时，如果其中包含有%d这样特殊意义的字符时，printf就会根据format string的指示，把堆栈中接下来的地址作为余下的参数解释，从而做出程序作者没有预期的行为。

上例中，format string是一个单独的 %d，因为printf的第一个参数也就是format string的地址保存在 %esp，则printf会取接下来 %esp+0x4 上的四个字节作为整数输出，那么 %esp+0x4 保存的值又是什么呢？将目光往上移动两行就会发现，这是fgets的第二个参数——1024，因为fgets之后没有语句改动过这个地址，所以依然残留在堆栈里。

在这里程序已经表现出了一定程度的脆弱性，而且事实上我们还可以完全控制传递给printf的所有参数，不仅仅是显示fgets的第二个参数。
```text
80485dd:       8d 44 24 18             lea    0x18(%esp),%eax
80485e1:       89 04 24                mov    %eax,(%esp)
80485e4:       e8 9b fe ff ff          call   8048484 <fgets@plt>
```
透过这三行汇编代码可以看出 char* buf 被分配在 %esp+0x18 这个地址上，printf 的参数解析是从 %esp 开始的，因此只要在format string的最前面加上5个 %x（算上format string本身，正好是6个参数，也就是0x18个字节），接下来的参数就会在我们可以控制的buf中取用。例如：
```
vagrant@lucid32:/vagrant/q4-crack$ ./q4
What's your name?
[%08x][%08x][%08x][%08x][%08x]%08x
Hi, [00000400][0026b440][0014139c][0000332c][00000000]3830255b
```
最后的`3830255b`，用 [Little Endian](http://www.cs.umd.edu/class/sum2003/cmsc311/Notes/Data/endian.html) 表示就是`5b 25 30 38`，对应的ASCII字符就是`[%08`，正是输入字符串的前四个字符。

所以如果传给printf的字符串是`\x78\x56\x34\x12[%08x][%08x][%08x][%08x][%08x]%s`[^1]，那么printf就会很高兴地将`\x78\x56\x34\x12`作为需要输出的字符串地址，从而把内存地址为12345678的值作为字符串打印出来，直到遇到\0为止。（虽然这个地址很可能是不可读的，使得我们只能得到一个segfault。）

不过还能让人稍稍宽心一点的是，到目前为止，printf只能输出内存中的值，无法进行修改，因此可以做的事情非常有限。然而printf还有一个比较冷门的特性：

> printf有个特殊的格式符%n，作用是在对应参数所给出的地址写入目前为止输出的字符个数。

%n让printf有了修改内存的能力，而这正是格式化字符串攻击的核心。如果我们传给printf的字符串是`\x78\x56\x34\x12[%08x][%08x][%08x][%08x][%08x]%n`，那么printf就会在地址12345678处写入44，也就是之前输出的字符个数。换言之，我们有了**在任意地址写入任意值**的能力。

不过还有一些细节问题，因为%n写入的是之前输出的字符数，所以我们必须控制字符数成为我们希望的值，固然不断地增加%x可以做到（理论上），但现实是堆栈大小是有限的，很有可能会读出界从而segfault，或者构造出的攻击字符串太长超过了fgets的缓冲区大小根本无法读入。于是这里我们就需要利用到printf的第三个特性：

> 在format string中可以指定输出的位数，例如 %020x 就是用20位输出一个十六进制值，不足的位数用0补全。

例如：
```text
vagrant@lucid32:/vagrant/4-villager-a$ ./q4
What's your name?
%020x
Hi, 00000000000000000400
```
这就是压死printf的最后一根稻草了，现在我们真正拥有了**在任意地址写入任意值**的能力！

#### 下期预告

对于格式化字符串攻击还有一些容易陷进去的坑需要提一下，另外为了实际解开那一题，还需要利用到GOT覆写（GOT overwrite）的知识，这些都放到下篇吧~

[^1]: 注意这里的前四个字节是十六进制值。如何向标准输入中输入十六进制值？这就需要`echo -ne`或是`printf`了。当然，还有一个最近写的小工具……放到下次再介绍吧。
