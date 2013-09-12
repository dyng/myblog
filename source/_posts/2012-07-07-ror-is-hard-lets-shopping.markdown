---
author: admin
comments: true
date: 2012-07-07 11:39:20+00:00
layout: post
slug: '%e8%af%b4-ruby-on-rails-%e5%be%88%e7%ae%80%e5%8d%95%e4%bb%80%e4%b9%88%e7%9a%84%e9%83%bd%e6%98%af%e9%aa%97%e4%ba%ba%e7%9a%84%ef%bc%81'
title: 说 ruby on rails 很简单什么的都是骗人的！
wordpress_id: 117
---

最近偶然看到这样一篇抱怨 rails 的文章，和[《UNIX痛恨者手册》](http://en.wikipedia.org/wiki/The_UNIX-HATERS_Handbook)一样的吐槽风格，非常搞笑，今天趁闲于是翻译了过来。特别是在搞笑之余，因为涉及到的技术方面很多，对于 web 开发不是很熟悉的同学或许还能从中学到一些东西，我自己就是在这篇文章中了解了不少过去不知道的概念和软件，然后意识到追赶潮流实在是个无底洞……好吧，这其实没啥关系。

<!--more-->

作为一个 Win 用户想学着用 rails 写个应用发布的时候：

  1. 发现用 Windows 来学习根本是不可能的 => 只好装 Linux

  2. 但还是怀念 Windows 于是又装了 msysgit => 浪费了半天时间

  3. 不知怎么搞的 gem 却不是最新的

  4. 听说光用 gem 的话以后会有大麻烦还要装 rvm 或者 rbenv => 自然还得学这俩哥们的用法

  5. 终于装完 Rails 3.2 没有 javascript 引擎无法启动

  6. 终于启动

  7. 还在写 HTML 的你真是弱爆了 => 学习 haml

  8. 还在写 js 的你真是弱爆了 => 学习 coffee script

  9. 还在写 css 的你真是弱爆了 => 学习 scss

  10. "不写测试的代码那是原始人的代码" => 学习 Rspec

  11. "只有单元测试是远远不够的“ => 学习 capybara

  12. "fixture一般都不是手写的“ => 学习 FactoryGirl

  13. 发现测试运行得很慢 => 只好装上 spork 和 autotest

  14. 为了测试起来更方便大家都用 growl => 只好去买 mac

  15. "不用说，我想大家肯定都会用 git 了" => 学习 git

  16. "不用说，我想大家肯定都是在 Heroku 上部署服务的" => 学习 Heroku

  17. 发现 Heroku 很慢，只好用 passenger 在自己机子上架设服务器

  18. 为了部署服务又开始学习 Capistrano

  19. 尽管服务器负荷一点都不高但看到大家都在用所以也很想用 nginx 和 unicorn

  20. "现在还会有人不设定 CI 吗" => 学习 Jenkins

  21. 终于可以发布应用的时候，Rails 的新版本也出来了……

补充：

  1. 文本编辑器貌似 TextMate 用的人最多还是得买 mac

  2. 用了一阵的 Textmate 却经常看到 Vim 和 Emacs 只好开始学习编辑器

  3. 买了一堆 Rails2 书发现毫无用处

  4. 最有名的[那本书](http://book.douban.com/subect/2123092/)写着“敏捷开发”于是又开始学习敏捷开发

  5. 准备随便看看 ActiveRecord 结果开始[学习黑魔法](http://book.douban.com/subject/7056800/)

  6. 这些黑魔法到底是怎么做到的呢……一不小心又开始读那本 [秘传书](http://i.loveruby.net/ja/rhg/book/)

注：这里引用到的书我都以相应中译本的豆瓣链接替代之，除了最后那本日本人写的 ruby 源码分析似乎没有被翻译引进过的关系只好使用了原文链接。

再补：原文链接 ( [http://toyoshi.hatenablog.com/entry/2012/03/09/143753 ](http://toyoshi.hatenablog.com/entry/2012/03/09/143753))
