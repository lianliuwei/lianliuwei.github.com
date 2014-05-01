---
layout: post
title: "chromium observer"
description: ""
category: code
tags: [chromium_base, Google, C++]
---
{% include JB/setup %}

chromium 中的 Observer
==
具体文件讲的是`base/observer_list.h`。

Observer模式可能是设计模式中除了Singleton之外，最经常使用的模式了吧。Observer模式名字有点名不副实，有种Observer主动实时查询状态变化的感觉，也许叫Pub-Sub模式更贴切。Observer模式的精髓就在于Publisher不用了解具体的Subscriber和数量。只要在相关的事件发生的时候，通知一下。理论上是完美的，但是实践上就是问题就多了。

### 常见错误 ###
1. Subscriber之间有依赖关系。这就对Publisher通知的顺序有要求，这是设计问题。可以考虑重新设计。
2. 跨线程观察，你要问自己三次"Did I really need THIS!?"。基本上出现这种设计，都是设计上有问题。先看看锁有没有加好。
3. Publisher自身内部状态还没变化完成就通知。通知中，Subscriber很可能调用Publisher获得状态，拿到一个中间状态就大事不妙。这就是大多数通知代码都是放在最后调用的原因。
4. Subscirber回调Publisher导致递归通知。这也是设计问题。
5. 忘记取消Sub。清理自己产生的是一种美德，如果遇到很难取消的情况，你的Subscriber和Publisher生命周期设计有问题，一般是Publisher比Subscriber长。

说完了这些常见的问题，可以开始实践了。

### 使用observer_list.h ###
由于C++不是C#，没有在语言层面支持Observer模式，所以你要自己添加`AddObserver() RemoveObserver() HasObserver()`方法。
自己定义好通知的类，加上`ObserverList<>`对象。一个观察者就完成了。需要通知的时候使用`FOR_EACH_OBSERVER(ObserverType, observer_list, func)`进行Publish。应该是最精简的步骤了。

### chromium中的其他Observer ###
`base/observer_list_threadsafe.h`实现了多线程下的Observer，实现使用的是Task机制，所以是异步的。跟理论上的模型有差距，使用请先了解清楚。

`content/ 中的 notification_service` 实现了一套全局的Observer机制，优点是，这是除了worker线程外全部线程都能Subscribe。而且在线程存在期间都是有效的，适合于做全局的广播。也是异步通知，而且使用的是公用参数，还需要向下强转类型。
