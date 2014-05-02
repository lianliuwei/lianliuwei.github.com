---
layout: post
title: "oscilloscope serie 0"
description: ""
category: code
tags: [chromium_base, Google, C++]
---
{% include JB/setup %}

序言
==
博主就职于致远电子四年，主要是负责示波器软件开发，技术上的学习也是围绕这个主题展开的。主要受到google公司开源项目chromium的影响。
就借着示波器软件开发这个系列来说明一个桌面应用是应该如何设计和实现的。

现在热门的方向是手机App开发，后开开发，桌面应用开发似乎被遗忘了。而且由于Windows诞生已久，API也是相当早期的设计，没有给与桌面开发者合理的设计和实现指引。而且最近Windows对于桌面应用的方向主要是在C#和.net上面。C++桌面应用开发就处于一个很尴尬的位置，用着90年代的技术，却要和最新技术比拼的工作。

这里要赞一下chromium工程，展现了用C++和最新的设计思路，极佳的实现，做出了一个顶级的浏览器。

现在C++桌面开发排名应该是。chromium > 国内大厂(百度腾讯) > 互联网企业 > 各种其他行业企业。我厂就是在能力阶梯上最弱的，一般处在这个阶梯上的有这样几个特点。

1. 基于MFC：不仅界面用MFC，有时候数据结构都用MFC。开发人员首先学会的就是MFC，而且就停止学习了。
2. 模块使用C接口：本来就是简单的导出C++接口使用多方便。估计是各个开发者写的C++风格不大统一。
3. 二进制复用： "视代码如生命，试员工如盗贼"的理念，深深的印在了管理者的头脑中，于是你就只能开到你该看到的。
4. 线程无解： 基本上锁要加对了，这样的员工已经很负责了。任务就是线程，一般一个软件不开个十几个线程都不好意思。

这样的技术和这样的管理水平，也就注定了开发出来的软件，只要能正确跑，不崩溃也就谢天谢地了。吐槽完毕，回到系列的主旨。

本系列主要讲如下几个方面:

 1. 基于MVVM的界面设计
 2. 基于线程和Task的设计思想，如何设计多线程
 3. 只能单线程操作的硬件操作实现