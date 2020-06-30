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

结果，住了一段时间，想搬到另一片空地去。这时候，按以往的办法，我只能再次搬石头、砍木头、画图纸、盖房子。
![重复盖房](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/docker/store2.png)

这时，跑来一个老神仙教会我一种魔法。这种魔法可以把我盖好的房子复制一份，做成「镜像」，放在我的背包里。
![黑魔法](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/docker/store3.png)

等我到了另一片空地，就用这个「镜像」，复制一套房子，拎包入住。Build once，Run anywhere!

是不是很神奇？对应到我们的项目中来，房子就是项目本身，镜像就是项目的复制，背包就是镜像仓库。如果要动态扩容，从仓库中取出项目镜像，随便复制就可以了。

{% alert info %}
不用再关注版本、兼容、部署等问题，彻底解决了「上线即崩，无休止构建」的尴尬。
{% endalert %}

#### 二、虚拟机与容器
开始之前，我们来做一些基础知识的储备：

1.**虚拟机**：虚拟化硬件
虚拟机 `Virtual Machine` 指通过软件模拟的**具有完整硬件系统功能的、运行在一个完全隔离环境中的完整计算机系统**。在实体计算机中能够完成的工作在虚拟机中都能够实现。

在计算机中创建虚拟机时，需要将实体机的部分硬盘和内存容量作为虚拟机的硬盘和内存容量。**每个虚拟机都有独立的 `CMOS`、硬盘和操作系统，可以像使用实体机一样对虚拟机进行操作**。在容器技术之前，业界的网红是虚拟机。

虚拟机技术的代表，是 `VMWare` 和 `OpenStack`。更多请参看[百科虚拟机](https://baike.baidu.com/item/虚拟机/104440?fr=aladdin)。

2.**容器**：将操作系统层虚拟化，是一个标准的软件单元，它有以下特点
* 随处运行：容器可以将代码与配置文件和相关依赖库进行打包，从而确保在任何环境下的运行都是一致的。
* 高资源利用率：容器提供进程级的隔离，因此可以更加精细地设置 `CPU` 和内存的使用率，进而更好地利用服务器的计算资源。
* 快速扩展：每个容器都可作为单独的进程予以运行，并且可以共享底层操作系统的系统资源，这样一来可以加快容器的启动和停止效率。


3.**区别与联系**
- 虚拟机虽然可以隔离出很多「子电脑」，但占用空间更大，启动更慢。虚拟机软件可能还要花钱，例如`VMWare`；
- 容器技术不需要虚拟出整个操作系统，只需要虚拟一个小规模的环境，类似「沙箱」；
- 运行空间，虚拟机一般要几 `GB` 到 几十 `GB` 的空间，而容器只需要 `MB` 级甚至 `KB` 级；

我们来看一下对比数据：

| 特性 | 虚拟机 | 容器 |
| ----- | :-----: | :----: |
| 隔离级别 | 操作系统级 | 进程 |
| 隔离策略 | Hypervisor | CGroups |
| 系统资源 | 5 ～ 15% | 0 ～ 5% |
| 启动时间 | 分钟级 | 秒级 |
| 镜像存储 | GB - TB | KB - MB |
| 集群规模 | 上百 | 上万 |
| 高可用策略 | 备份、容灾、迁移 | 弹性、负载、动态 |

{% alert info %}
虚拟机属于虚拟化技术，而 Docker 这样的容器技术，属于轻量级的虚拟化。
{% endalert %}

#### 三、认识 Docker

`Docker` 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 `Linux` 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

`Docker` 技术的**三大核心概念**，分别是：镜像 `Image`、容器 `Container`、仓库 `Repository`。

{% alert warning %}
镜像不包含任何动态数据，其内容在构建之后也不会被改变。
{% endalert %}


#### 四、核心概念

{% alert success %}
三板斧：「搭建、发送、运行」，Build once，Run anywhere
{% endalert %}

1.`Docker` 本身并不是容器，它是创建容器的工具，是应用容器引擎；
2.`Docker` 三大核心概念，分别是：镜像 `Image`，容器 `Container`、仓库 `Repository`；
3.`Docker` 技术使用 `Linux` 内核和内核功能（例如 `Cgroups` 和 `namespaces`）来分隔进程，以便各进程相互独立运行。



`Docker` 轻量级的原因？为什么启动快？怎么做到和宿主机共享内核？



#### 五、快速上手
要掌握 `Docker` ，我们需要从下面这 `11` 个命令开始：

| 命令| 描述 |
| ----- | :---- |
| FROM | 基于哪个镜像来实现 |
| MAINTAINER | 镜像创建者 |
| ENV | 声明环境变量 |
| RUN | 执行命令 |
| ADD | 添加宿主机文件到容器里，有需要解压的文件会自动解压 |
| COPY | 添加宿住机文件到容器里 |
| WORKDIR | 工作目录 |
| EXPOSE | 容器内应用可使用的端口 |
| CMD | 容器启动后所执行的程序，如果执行 docker run 后面跟启动命令会被覆盖掉 |
| ENTRYPOINT | 与 CMD 功能相同，但需 docker run 不会覆盖，如果需要覆盖可增加参数 -entrypoint 来覆盖 |
| VOLUME | 将宿主机的目录挂在到容器里 |



#### 六、常规操作



#### 七、最佳实践



#### 八、总结
容器化技术必将是云时代不可或缺的技能之一，而 `Docker` 只是沧海一粟。随之而来的还有集群容器管理 `K8s`，复杂 `Service Mesh` 管理 `Istio` 等技术。打开 `Docker` 的大门，不断抽丝剥茧，逐层深入，你将感受到容器化的无穷魅力。

文章最后，打个小广告吧。如果你想搭上在线教育的快车快速成长，不妨加入我们。一起学习、一起成长、一起挑战。「跟谁学-高途课堂」欢迎你，请将简历私我～
