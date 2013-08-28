---
layout: post
title: "欧洲人在日本"
date: 2013-08-28 17:38
comments: true
categories: 
---

看到以后笑了很久…… 译自 *HN* 上的[某个帖子](https://news.ycombinator.com/item?id=6141330)。[作者](https://twitter.com/patio11)似乎是一位欧洲友人。

某家日本公司有一天决定他们需要在某个系统里面加入一些“虚拟”的员工，以支持诸如在一个职位还未招到人的情况下能够把他的工作安排进组织图表里（以及其他很多应用场景）。所以他们想到了一个很聪明的主意，“啊哈，我们可以这么干，在‘日语姓名’一栏里填入状态信息，例如 XX_JOB_REQUEST 或者 XX_INCOMINT_TRANSFER 来代表这些虚拟的员工。”

然后有一个该系统的程序员，觉得每添加一个新状态信息就要改动代码太麻烦了，于是他写了如下的代码：

``` java
if (InternalStringUtils.isAllLatinCharacters(employee.getJapaneseName()) {
    /* 虚拟员工，将其从工资单中移除 */
    ...
}
```

为什么我会知道这段如此耐人寻味的代码，我想你们都能猜到了吧。
