---
title: 系统负载看不懂？
date: 2020-01-20 10:54:40
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/linux/logo.png
thumbnailImagePosition: left
tags: 
- linux
- load
- system
comments: false
metaAlignment: center
categories: 技术博文
---
Web 开发会经历「开发-上线-部署」三个过程，部署之后站点的运行状态如何却鲜有关注。如果我们要估算网站能承受多大的并发，你可能需要真正了解系统负载。
<!-- more -->

#### 一、先看场景
1.服务器状态（`Linux` 服务器通过 `Top` 命令查看）
![服务器状态](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/linux/system-load.png)


2.数据监控
![平均负载](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/linux/load-avg.png)

上面是两个典型应用场景，我们可以直接在服务器查看系统负载。当然，也可以获取数据本地可视化显示。听起来不错，可是问题来了：

Q1：`load average`: 0.03, 0.12, 0.07 是什么？
Q2：为什么同时监控 1 分钟，5 分钟，15 分钟？

如果你对此有疑问，不妨继续阅读。

#### 二、参数说明

{% alert info %}
为了便于理解，我们从「一个比喻」，「两个概念」和「三个边界」来说明。
{% endalert %}

1.**一个比喻**
我们可以把 `CPU` 比喻成一条马路，进程任务就是马路上飞驰的汽车，`Load` 则表示马路的拥挤程度。

2.**两个概念**
系统负载（System Load）：
系统 `CPU` 繁忙程度的度量，即有多少进程在等待被 `CPU` 调度（进程等待队列的长度）。

平均负载（Load Average）：
一段时间内系统的平均负载，这个一段时间一般取 1 分钟、5 分钟、15 分钟。

3.**三个边界**
Load = 0，路上一辆车也没有；
Load = 0.7，一大半路上有车；
Load = 1，所有路段都有车，基本饱和状态，但是道路仍然能够通行；

阮一峰老师[理解 Linux 系统负荷](https://www.ruanyifeng.com/blog/2011/07/linux_load_average_explained.html)中举的这个例子很形象：
- 系统负荷为 1.7，意味着车辆太多了，桥已经被占满了（100%），后面等着上桥的车辆为路面车辆的 70%。
- 系统负荷 2.0，意味着等待上桥的车辆与桥面的车辆一样多；
- 系统负荷 3.0，意味着等待上桥的车辆是桥面车辆的 2 倍。


总之，当系统负荷大于1，后面的车辆就必须等待了；系统负荷越大，过桥就必须等得越久。

{% alert success %}
道路的通行能力，就是 `CPU` 的最大工作量；道路上的车辆，就是一个个等待 `CPU` 处理的进程（`Process`）。
{% endalert %}

#### 三、多核负载如何计算？
上文我们不管是路还是桥的例子，都是默认电脑只有一个 `CPU`，那如果多 `CPU`，情况又是如何呢？

{% alert success %}
很简单，`2` 个 `CPU`，意味着电脑的处理能力翻了一倍，能够同时处理的进程数量也翻了一倍。
{% endalert %}

`2` 个 `CPU` 表明系统负载可以达到 `2.0`，此时每个 `CPU` 都达到 `100%` 的工作量。如果你的服务器是 `4` 核 `CPU`，那么系统负载极限就是 `4.0`。

#### 四、答题时间
到这里，相信我们的 `Q1` 问题已经解决了。我们重点来看 `Q2`：为什么同时监控 `1` 分钟，`5` 分钟，`15` 分钟？

[一分钟理解负载 LoadAverage ](https://www.w3cschool.cn/architectroad/architectroad-loadaverage.html) 中有很好的解释：

- Load < 0.7时：系统很闲，马路上没什么车，要考虑多部署一些服务
- 0.7 < Load < 1时：系统状态不错，马路可以轻松应对
- Load == 1时：系统马上要处理不多来了，赶紧找一下原因
- Load > 5时：马路已经非常繁忙了，进入马路的每辆汽车都要无法很快的运行

那么如果按照 1 分钟来评估系统负载，会被系统短暂的抖动所影响。

{% alert success %}
所以 1 分钟更多是作为一个参考度量，综合 5 分钟和 10 分钟使监控指标更加准确。
{% endalert %}

1 分钟 Load > 5，5 分钟 Load < 1，15 分钟 Load < 1
短期内繁忙，中长期空闲，初步判断是一个「抖动」或者是「拥塞前兆」

1 分钟 Load > 5，5 分钟 Load > 1，15 分钟 Load < 1
短期内繁忙，中期内紧张，很可能是一个「拥塞的开始」

1 分钟 Load > 5，5 分钟 Load > 5，15 分钟 Load > 5
短中长期都繁忙，系统「正在拥塞」

1 分钟 Load < 1，5 分钟 Load > 1，15 分钟 Load > 5：
短期内空闲，中长期繁忙，不用紧张，系统「拥塞正在好转」
