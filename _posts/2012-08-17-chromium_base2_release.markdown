---
layout: post
title: "chromium_base2 project introduction"
date: 2012-08-17 11:02
comments: true
categories: chromium_base
---

chromium_base2工程简介
==========

缺失的base
----

长期以来C++界缺少强大的基础库，对比Python，Ruby，Java中那些自带丰富的库，C++简直什么都没有
 
 - log库几乎是残疾的，不能区分加严重等级，没有调用堆栈
 - thread库不存在，自己制造平台依赖性太大
 - 字符串类型几乎不能使用，unicode支持都没做好，各个平台都有很多的字符串类型实现
 - 没有引用计数，scoped_ptr, 弱指针等内存管理工具
 - 没有同步相关的库

由于以上这些因素存在，各个平台，各种类型项目，甚至各个项目组都开始造轮子，MFC有线程、文件、同步、网络库、甚至还实现的数据结构库，（MFC出现的时候STL还没有诞生）。

base库有一个特定，就是依赖性大，几乎所有文件中都会引用几个，还会影响使用者的编程风格，用过MFC的同学应该知道，基本上所有基于MFC开发的工程，代码看起来跟MFC的一样。

理想的base库应该是：

 -  方便自己编译（C++各种编译属性太多）
 -  跨平台
 -  有强力的维护
 -  提供各平台的IDE文件，方便管理
 -  设计良好


chromium工程的base库
--

google的chromium工程，使用的是google四大语言中的首位C++，google将C++工程化应用提升到了新的高度，使用googe C++
编程规范，有效的避免容易和错误使用的C++特性，创造了一系列有用的工具，设计合理的各个模块。

chromium中base库可能来源于google内部使用的base库，这部分代码正好符合了理想的base库的条件。

但是google并没有将base库独立出来，单独创建一个版本库，降低这部分优秀代码的。现在又chromium_base2工程单独来提取这部分代码

chromium_base2
--
[github主页](https://github.com/lianliuwei/chromium_base2)

chromium\_base2和chromium\_base区别
--
chromium_base以前提取的实验，提取方法不合理，导致了使用中出现了很多问题，chromium_base2是博主最新提取版本，更加接近原生工程

详细介绍和使用指南见后续相关文章