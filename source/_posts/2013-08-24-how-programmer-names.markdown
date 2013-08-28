---
layout: post
title: "程序员的命名趣味"
date: 2013-08-24 21:09
comments: true
categories: 技术
---

> ……孔乙己显出极高兴的样子，将两个指头的长指甲敲着柜台，点头说，“对呀对呀！……exec有六样写法，你知道么？”我愈不耐烦了，努着嘴走远。孔乙己刚用指甲蘸了酒，想在柜上写字，见我毫不热心，便又叹一口气，显出极惋惜的样子。

这自然只是我的杜撰，不过 Unix 系统的 exec 确实有[六种写法][1]，想必当年也是害苦了不少系统程序员。其实今天我想说的，正是关于命名的事儿。相信以下已经成为程序员的共识：

**好的命名对提高可读性事半功倍，坏的命名则是代码晦涩的一大帮凶。**

但取一个好的名字又谈何容易，也无怪乎某朋友开玩笑说“（写代码）瓶颈是取变量名”了。不过凡事，博古大抵可以通今，所以这里就整理一下 *nix 系统中一些有趣的命令以及它们有趣的名字，总结一下所谓的“命名范式”，或许对大家今后的命名之道有用。

<!--more-->

在这之前，再补充一个 Unix 的故事，也是很有名的：

Unix 诞生十多年后，有人问起 Ken Thompson 如果重新设计 Unix 会有什么不同时，Ken Thompson 答道：

**我会给 creat 加一个 e 。**

这个典故源于 Unix 创建文件的系统调用 creat，关于为什么最后少了一个 e 这里也有一些[讨论](http://stackoverflow.com/questions/8390979/why-create-system-call-is-called-creat)。可见即便如 Thompson 这般的大牛，也对因为偷懒（节约几个字符）而牺牲可读性感到后悔啊。

好吧，回到正题，程序员的“命名范式”。首先自然是最为经典，最常使用，又最不费脑筋的：

1. 缩写式。

    Unix 下最常被使用，最基本的几个命令：

    - ls = list
    - cd = change directory
    - mv = move
    - rm = remove
    - cp = copy

    都是如此，另外还有 ln，man，pwd, fg, bg 等等，不一而足。只有 whoami 似乎不在此列，不过这是 2.9BSD（1983年）才加入的新命令，换做十年前或许就变成 wmi 了吧……

2. 反义式。

    这其实也是一个非常常见的范式，是以一个现有程序（往往就是针对它的改进）的反义词为名。最常见的例如：

    - more vs less（比较讽刺的是 less 几乎是 more 的超集，less is more, huh? ）
    - markup vs markdown

    以及 Unix 本身就是 [Multics](http://en.wikipedia.org/wiki/Multics) 的反义语。

3. 逆序式。

    这应该最初也是 Unix 的一种命名风格（至少就我所知，bourne shell 的条件语句 if 和 case 已经如此了），也就是对一个已有名字取其逆序为名。例如：

    - sl：ls 的逆序，不过其实和 ls 没什么关系，而是个很有趣的命令 :P （能够上 youtube 的同学可以点[这里](http://youtu.be/BPMd2dsSVR0)查看效果）。
    - tac：cat 的逆序，效果也是名副其实地逆序显示一个文件……
    - tig：正如其名的 git 的逆序，不过和上面提到的那些 just for fun 命令不同的是，tig 是一个**非常好用**的 git repository browser，强烈推荐。

4. 衍生式。

    其实这个我随意起的范式名不很确切，确切地说是“用另一种语言实现的某个已有程序”，所以或许“移植系”是个更贴切的名字，不过更难听也是了……移植的典范例如：

    - make -> rake -> cake：rake 是大名鼎鼎的 make 的 ruby 版（严格来说比原始的 make 功能增强了不少），而它的 coffeescript 版就变成了 cake（名字倒是挺诱人的，特别是配合 coffee 的时候 XD）。

    还有一个相对没那么有名的：

    - rack -> plack -> clack：plack 是 perl 版的 rack，clack 则是 common lisp 版的。

5. 递增式。

    这种命名范式最常出现在程序语言的命名中，最著名的当然就是 C 了。C 是 Ritchie 为 Unix 系统而开发的语言，由 [B 语言][2]（Thompson 开发）发展而来，因此很自然地以 B 的下一个字母 C 为名。可以猜到的是，[D 语言][3]，[E 语言][4]，[F 语言][5]什么的也都是存在的。比较奇怪的是唯独没有 A 语言……

6. YA 系。

    大名鼎鼎的 Yet Another 家族，想必是不用我再[介绍](http://en.wikipedia.org/wiki/Yet_another)什么了…… 几乎可以说是命名界（这是什么界？）的万金油，每当遇到重造了轮子又懒得想名字的时候，冠上 YA 总是个最轻松的解决方案。

7. GNU 系。

    GNU 可能是这种命名方式中最有名的，所以就姑且以它作为这种命名范式的名字，当然还有一个更学术的名字叫做[Recursive acronym](http://en.wikipedia.org/wiki/Recursive_acronym)。这一家族中有 YAML 这种无论怎么看都是 YA 家族的家伙混了进来，只因为作者事后硬要解释为 "YAML Ain't Markup Language" …… 所以当初就应该好好取名嘛不是！

8. 东方系。

    当然这里说的东方不是[那个东方](http://zh.wikipedia.org/wiki/%E6%9D%B1%E6%96%B9Project)……

    这里特指的是那些以东洋神秘文化（多是日语）为程序取名。例如：

    - zen-coding：禅编程，意境深远，嗯，其实只是写 html 而已……
    - Kendo UI：剑道用户接口，嗯，其实只有一个 JS 的 UI 库……
    - Dojo toolkit：道场工具箱，嗯，还是 JS 的工具库……

    不知为何前端特别喜欢这类名字，只能说是文化吧…… 不过这里还需要特别提到一个网站：Heroku！从这个古怪的名字想必就可以猜到了，它是 hero 和 haiku（俳句）的结合体，虽然我完全不能理解把这两个词拼在一起是什么意思…… 顺便 Heroku 的付费方案也是延续了这种风格：

    - Kappa：河童。
    - Ronin：浪人。
    - Fugu：河豚。
    - Ika：乌贼。
    - Zilla：哥斯拉。

    只能说 James Lindenbaum 或许是个日本迷吧……

9. z 系。

    z 系，顾名思义就是以 z 为首字母的程序族，特征是都接受压缩过的数据作为输入，进一步处理后以人类可读的方式输出，例如：

    - zcat：cat 的z版，显示压缩文件的内容。
    - zgrep：grep 的z版，对压缩文件的内容进行搜索。
    - zdiff：diff 的z版，对两个压缩文件的内容进行比较。
    - zless：less 的z版，分页显示压缩文件的内容。
    - zsh：sh 的z版，接受用户直接输入压缩后的二进制命令并……好吧，这个是玩笑。

10. 合成系。

    这主要说的是将两个约定俗成的缩写拼接在一起作为程序的名字，最大的好处就是可以让人一眼就明白这个程序是做什么的（其实所谓优秀的变量名也是如此）。这一系列的命令罗列起来就非常多了：

    - memstat：显示内存状态。
    - netstat：显示网络状态。
    - iostat：显示 IO 状态。
    - sysstat：显示 Linux 的系统状态。
    - lspci：显示 PCI 插槽上的设备。
    - lsusb：显示 USB 口的设备。
    - lsof：显示所有打开的文件描述符。
    - lsmod：显示 Linux 内核加载的所有模块。
    - nettop：动态显示网络状态。
    - iotop：动态显示 IO 状态。
    - netcat：对网络端口进行输入和输出。
    - socat：对 socket 进行读写。（其实和 netcat 功能相仿，不过更易使用）

    几乎任意两个词随意组合就能得到一个新的程序，我其实是最欣赏这种命名方式的，因为这种名字不但能够自我解释，更难得的是用户可以一定程度上“预测”某些程序的存在。上面这些命令中就有一些我其实事先并不知道，在尝试搜索了几种组合以后才发现确实存在的。

以上是随意整理的十种命名范式，其实是远远不足以涵盖 *nix 系统上所有命令的，例如还有使用三位作者姓名首字母缩写的 [awk](http://en.wikipedia.org/wiki/AWK)，以历史上[第一位程序员](http://en.wikipedia.org/wiki/Ada_Lovelace)命名的 [Ada][6] 等等。不过这里我想再说一些 Unix 上非常混淆的命令，例如下面三个：

- write：向其他用户发送消息。
- strings：在一个二进制文件中搜索 ASCII 字符串。
- kill：向一个进程发送信号。

其中 kill 恐怕是最容易让人误解的命令了，尽管在没有指定的情况下 kill 默认发送的信号是 TERM，因此不能说完全没有道理，但确实很困扰初学者，特别是[信号][7]这种 IPC 方式现在已经较少使用的情况下。

总得来说，命名永远是一门大学问，虽然平时写代码时我们总会犯[这样的错误](http://kottke.org/04/03/programmings-two-worst-variable-names)或者[那样的错误](http://www.quora.com/Computer-Programming/What-are-some-of-the-funniest-variable-names-youve-used-or-seen-in-source-code)，但在可能的情况下还是尽量起一个容易理解——最好还容易记——的名字，也算是对后人做出的贡献吧。

至于文章开头提到的那个故事，其实还有个温馨的结局。在 Unix 系统发明已经过去 30 多年，Ken Thompson 也离开贝尔实验室来到 Google 工作之后的 2008 年，他向 Golang 提交了这样一个 [commit](http://code.google.com/p/go/source/detail?r=4a3f6bbb5f0c6021279ccb3c23558b3c480d995f)。

    Log message

    spell it with an "e"

    R=rsc
    http://go/go-review/1025037

[1]: http://en.wikipedia.org/wiki/Exec_(computing)#C_language_prototypes
[2]: http://en.wikipedia.org/wiki/B_(programming_language)
[3]: http://en.wikipedia.org/wiki/D_(programming_language)
[4]: http://en.wikipedia.org/wiki/E_(programming_language)
[5]: http://en.wikipedia.org/wiki/F_(programming_language)
[6]: http://en.wikipedia.org/wiki/Ada_(programming_language)
[7]: http://en.wikipedia.org/wiki/Signal_(computing)
