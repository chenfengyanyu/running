---
title: Docker 边学边用
date: 2020-06-20 22:10:13
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/docker/logo.png
thumbnailImagePosition: left
tags: 
- docker
comments: false
metaAlignment: center
categories: 技术博文
---
富 `Web` 时代，应用变得越来越强大，与此同时也越来越复杂。集群部署、隔离环境、灰度发布以及动态扩容缺一不可，而容器化则成为中间的必要桥梁。
<!-- more -->
本节我们就来探索一下 `Docker` 的神秘世界，从零到一掌握 `Docker` 的基本原理与实践操作。别再守着前端那一亩三分地，是时候该开疆扩土了。

#### 一、讲个故事
为了更好的理解 `Docker` 是什么，我们先来讲个[故事](https://my.oschina.net/jamesview/blog/2994112)：

我来到一片空地，想建个房子，于是我搬石头、砍木头、画图纸、盖房子。一顿操作，终于把这个房子盖好了。
![盖房子](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/docker/store1.png)

结果，我住了一段时间，想搬到另一片空地去。这时候，按以往的办法，我只能再次搬石头、砍木头、画图纸、盖房子。
![重复盖房](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/docker/store2.png)

但是，跑来一个小仙女，教会我一种魔法。这种魔法，可以把我盖好的房子复制一份，做成「镜像」，放在我的背包里。
![黑魔法](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/docker/store3.png)

等我到了另一片空地，就用这个「镜像」，复制一套房子，拎包入住。

是不是很神奇？对应到我们的项目中来，房子就是项目本身，镜像就是项目的复制，背包就是镜像仓库。如果要动态扩容，从仓库中取出项目镜像，随便复制就可以了。

不用再关注项目版本，兼容，部署环境，运行环境等问题，真的解决了 

Build once，Run anywhere!

#### 二、虚拟机与容器
开始之前，我们来做一些基础知识的储备：

1.虚拟机：虚拟化硬件
在容器技术之前，业界的网红是虚拟机。虚拟机技术的代表，是 `VMWare` 和 `OpenStack`。虚拟机，类似于「子电脑」，在「子电脑」里，你可以和正常电脑一样运行程序，例如开微信。

如果你愿意，你可以变出好几个「子电脑」，里面都开微信。「子电脑」和「子电脑」之间，是相互隔离的，互不影响。虚拟机属于虚拟化技术。而 `Docker` 这样的容器技术，也是虚拟化技术，属于轻量级的虚拟化。

2.容器：将操作系统层虚拟化
我们都知道容器就是一个标准的软件单元，它有以下特点：
* 随处运行：容器可以将代码与配置文件和相关依赖库进行打包，从而确保在任何环境下的运行都是一致的。
* 高资源利用率：容器提供进程级的隔离，因此可以更加精细地设置 `CPU` 和内存的使用率，进而更好地利用服务器的计算资源。
* 快速扩展：每个容器都可作为单独的进程予以运行，并且可以共享底层操作系统的系统资源，这样一来可以加快容器的启动和停止效率。


3.区别与联系
- 虚拟机虽然可以隔离出很多「子电脑」，但占用空间更大，启动更慢。虚拟机软件可能还要花钱，例如`VMWare`；
- 容器技术不需要虚拟出整个操作系统，只需要虚拟一个小规模的环境，类似「沙箱」；
- 运行空间，虚拟机一般要几 `GB` 到 几十 `GB` 的空间，而容器只需要 `MB` 级甚至 `KB` 级；

#### 三、认识 Docker

`Docker` 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 `Linux` 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

`Docker` 技术的三大核心概念，分别是：镜像 `Image`、容器 `Container`、仓库 `Repository`


{% alert warning %}
镜像不包含任何动态数据，其内容在构建之后也不会被改变。
{% endalert %}


#### 四、核心概念

{% alert success %}
三板斧：「搭建、发送、运行」，Build once，Run anywhere
{% endalert %}

1.`Docker` 本身并不是容器，它是创建容器的工具，是应用容器引擎；
2.`Docker` 三大核心概念，分别是：镜像 `Image`，容器 `Container`、仓库 `Repository`；

`Docker` 轻量级的原因？为什么启动快？怎么做到和宿主机共享内核？



#### 五、快速上手



#### 六、常规操作



#### 七、最佳实践



#### 八、总结
容器化技术必将是云时代不可或缺的技能之一，而 `Docker` 只是沧海一粟。随之而来的还有集群容器管理 `K8s`，复杂 `Service Mesh` 管理 `Istio` 等技术。打开 `Docker` 的大门，不断抽丝剥茧，逐层深入，你将感受到容器化的无穷魅力。

文章最后，打个小广告吧。如果你想搭上在线教育的快车快速成长，不妨加入我们。一起学习、一起成长、一起挑战。「跟谁学-高途课堂」欢迎你，请将简历私我～
