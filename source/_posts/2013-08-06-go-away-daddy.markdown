---
layout: post
title: "走开，Daddy"
date: 2013-08-06 15:47
comments: true
categories: 杂谈
---

{% img center /images/20130807/goawaydaddy.jpg %}

回头发现，这个新博客的第一周大部分时间是在和GoDaddy的折腾中度过的。虽然过去一直对GoDaddy没有特别好的印象（可能和杂乱的界面以及创始人[Bob Parsons](http://en.wikipedia.org/wiki/Bob_Parsons)的前海军陆战队身份有关），但当决定注册一个新域名时第一个想到的依然是GoDaddy，于是我注册了，付款了，ping通了，一切都显得很顺利。

只是那时的我没想到之后的一周会如此折腾。

<!--more-->

差不过就是五天前，发现博客突然上不去了，DNSPod也显示域名无法解析。第一反应，可能是GoDaddy的问题吧，很自然地就想登录看看状况，这时我犯了第一个错误：

**GoDaddy登录使用的不是邮箱而是用户名。**

但是GoDaddy的错误信息是不管这些的，一概显示“用户名和密码不匹配”，我只好相信我又记错密码了，于是把可能的密码都输了一遍，直到我发现GoDaddy需要的是用户名而不是邮箱时。但已经晚了，当我输入正确的用户名和正确的密码时，得到的结果是：

> This account is locked. Please contact Customer Service.

好吧，现在我们就有两个问题了。

既然提示信息是“请联系客服”，所以我就开始搜索不联系客服的解决方法（之前的教训告诉我GoDaddy的提示信息是必须反着看的）。很快找到一个重置密码的办法，我试了，但是在email地址一栏显示"invalid value"，这种朴素平实的错误信息不禁让我想起公司某个即使把500 Internal Server Error抛给用户也一定要加一行大字“通信错误”的app。总之重置密码也试了好几次，但始终是"invalid value"而不得其解，我甚至都怀疑不会是因为邮箱名里有"virus"字样所以触发了过滤吧？这一次的教训就是：

**无论如何，起一个人畜无害的邮箱名总是好的。**

重置密码也无果，只能联系客服了。写邮件前顺便搜了一下邮箱，发现三封来自GoDaddy的未读邮件……因为都被标为不重要所以一直没有看到。草草浏览了一下，终于明白域名为什么被封了。

> Your account has been selected by our verification office as a precautionary measure to defend you from possible misuse of either your payment method or products.

好吧，原来我是幸运得被用户认证中心选中的用户，为了保护我不会成为信用卡盗用的受害者。

> During the login process, our secure site will prompt you to upload a viewable, scanned copy of the payment method account holder's government-issued photo identification, such as a driver's license or passport.  In the comments box, we ask that you also provide a brief explanation of how you intend to use the product(s) purchased. If we do not receive the requested documentation within the next 48 hours, your order(s) may be cancelled for your protection.

然后就是要求我必须上传含照片的护照扫描件，以及最后通牒式的48小时时限。当然我看到这封邮件已经是3天后了，所以点开另一封邮件是这样的：

> As a result of this review, your recent order(s) in shopper account xxxxxxxx have been canceled.

我不明白一个26刀的域名有什么必要值得我必须扫描个人ID并且上传给一个陌生人……不过域名毕竟已经开始使用了，不能轻易失去它。但身边一时也没有ID，只好先发一份邮件请求能否延缓两天（因为上一封邮件中要求当天就需要上传），并且说明了我现在无法登录的困境。由于时差关系等了一天，收到这样的回复：

> Your timely compliance will ensure a quick resolution.

说得好像是我不配合一样。无奈只好把外国人登录证扫描成图片，正准备上传时才发现：

**上传认证信息需要登录，而我因为没有通过认证所以账号处于被封状态。**

……于是我又开始试各种解封账号的方法（给客服的邮件前一天也发了，但没有回音），在尝试“找回密码提示”的时候，突然提示让我上传认证照片！大喜过望，忙不迭地把刚扫描好的外国人登录证传上去，为了防止他们不认识，又在留言一栏里附了解释外国人登录证的wiki。搞完这些，觉得终于没问题了吧。

又是等了一天，收到GoDaddy的回信：

> Unfortunately, the name on the identification we received does not match the name of the payment method account holder provided at the time of purchase.

外国人登录证上的是汉字，而信用卡的名义人是罗马拼音……算了，我也不指望你们会念一个中国人名字的日语读法。这时候离当初注册已经过去一个星期了，我为了一个不到3000日元的域名，拼命地证明自己不是盗刷信用卡的罪犯，浪费时间、心力，我实在受够这种折腾了——特别是，在查看了一下护照以后发现上面写的是名字的汉语拼音以后。

whois了一下，好样的，已经没有记录了。滚吧，Daddy，我浪费了足够多的时间在这件愚蠢的事上，是时候和你说再见了。哦不，我再也不想见到你了。

上onamae注册，下单，下楼FamilyMart缴费，搞定。

*ps：* 写完这篇文章后，在gmail的垃圾箱里无意中发现了GoDaddy客服对解封账号的回复，我明明设定了filter凡是来自GoDaddy的邮件都始终标注重要的啊……
