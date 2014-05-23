---
layout: post
title: "oscilloscope serie -- MVVM"
description: ""
category: code
tags: [Oscilloscope, C++]
---
{% include JB/setup %}

示波器界面篇
==

## UI技术

UI技术的变化和革新速度在软件技术中是首屈一指。Windows下就有WndProc过程式编程到MFC用class封装窗口面向对象编程、C++被更好的面向对象语言C#替代、WinForms被WPF替代。开头解决是是界面库的问题，用一个对象表示一个窗口，明显比用过程清晰明了，容易扩展。后来界面库的问题大体解决，开始考虑编码上如何设计，开头大家做做简单的应用，在按键回调函数里面调用一下后台具体业务代码就能实现。后来发现这种结构很容易导致两个问题：
 
 1. 业务代码阻塞界面
 1. 复杂视图、控件设计

第一个问题不是这篇内容，暂且跳过。复杂视图的问题，以一个树形控件为示例，有以下方法：

1 直接提供界面树形节点界面类

	这会出现界面代码和逻辑代码无法区分的情况，把界面相关的类暴露到逻辑层，无法使用其他界面库，无法跨平台。有的结构只有界面需要，但是接口不需要，也要暴露出来。

2 给每个节点一个ID，添加，删除，设置，通知全是通过ID

	这就退回了原始的C接口，无法使用继承定制自己的节点类。

3 给出逻辑数据结构类，和对应的界面类

	避免了上述两种的问题。逻辑部分代码可以单独测试。缺点是，实现复杂度增加，需要考虑界面数据结构和逻辑数据结构的同步。

经过上面的比较，对于复杂视图，唯一正确的实现方法3，这也是大名鼎鼎的MVVM模型，Windows在划时代的WPF界面框架的设计中深度支持这种模型。这些方法的演进也是，MVC，MVP, MVVM 这些界面设计模型的变化。

在C++ + 任何界面库中实现MVVM比在C# + WPF中实现难得多。

1. 没有强大的反射功能，导致对于子结构有不同接口，就需要自己加上个反射功能。
2. 没有WPF中的binding功能，使用Observer实现binding类似的功能，无比繁琐。
3. 没有GC，导致生命周期管理要异常小心

虽然有这些实现难度，但是MVVM的结构天生优秀，付出一些劳动实现完全值得。chromium工程大量用了这种模式，chrome/browser/ui/ 下代码
是ViewModel实现，chrome/broswer/ui/views/ chrome/broswer/ui/views/cocoa 下是View在个界面库的实现。


## WaveControl控件

博主自己写的这个控件文件组织结构也按照chromium ui的组织方式。主目录在wave_control/下, views/ 子目录是View的实现。
WaveControl的逻辑结构是，WaveControl下有WaveContainer节点，WaveContainer节点下是有Wave节点。WaveControl是一个容器，WaveContainer可以是容器也可不是。WaveContainer有子类YTWaveContainer

(待完成)

## 参考

Presentation Model
http://martinfowler.com/eaaDev/PresentationModel.html  ThoughWorks 工程师的阐述，和详细示例

