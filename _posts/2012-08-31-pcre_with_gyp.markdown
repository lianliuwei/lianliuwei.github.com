---
layout: post
title: "pcre with gyp support"
date: 2012-08-31 9:45
comments: true
categories: project
tags: pcre gyp github regex C++
tagline: C++ Regex lib with gyp
---
C++正则表达式PCRE
==
事情的起因是，博主需要做一个MFC的验证Edit，查看了各种实现，发现Edit是不好搞的，这种验证都设计状态机。自己手写状态机不是累到吐血，后来灵光一现，搞一个正则表达式来判断不就轻松解决。（这段以后详细说明，到时把相关的验证对话框类给公开了）


用C++必要的一件事情就是到处找轮子，基础库完全弱爆了，连个正则表达式都没有。后来想偷懒，看看chrome中用的是哪个（是的，我是坚定不移的chrome代码粉），结果使用的是ICU的正则表达式，用来提供给Javasript使用（没有考证过，或者是V8，webkit自带的？），于是踏上慢慢寻找轮子的征程。

博主先后考察过：

 - boost::regex 考虑到要编译boost库，头就非常大
 - TR1 C++标准库，不过需要VS2008SP1以上版本
 - CAtlRegExp VS自带的ATL正则表达式，传说语法很不标准，VS2008以后需要自己单独下载库来使用
 - GRETA 微软研究院写的正则表达式，很老了，官网都没有了, 使用的很多C++异常机制
 - PCRE 这个更新比较勤快，而且最后发现google已经在使用这个东西了，还提交了C++封装

博主坚定不移的跟随了google的选择 :)

pcre
---
 官网 [http://www.pcre.org/](http://www.pcre.org/),google的[sawbuck](https://code.google.com/p/sawbuck/)中使用了它，而且提供了GYP工程，创建VS工程相当方便。而且pcre工程自带单元测试，第三方使用可以测试下，比较放心。

博主抽取了一个独立的版本，加上一些示例，方便后来人,[https://github.com/lianliuwei/pcre](https://github.com/lianliuwei/pcre)

不足：

 - 不是最新的版本，bug修复什么的没有及时更新。
 - 最新版本有一个大的变化，针对UTF-16使用的是分别的库，这个版本还不知道要怎么处理
 - 只能作为静态库使用，还不能当成动态库使用
 - 没有测试过非ASCII之外的情况

这些不足博主后续改正，同时可以借这个库看一下正则表达式库如何实现的。