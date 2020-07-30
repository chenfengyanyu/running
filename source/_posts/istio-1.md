---
title: 了不起的 Istio
date: 2020-07-29 06:44:51
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/istio/logo.png
thumbnailImagePosition: left
tags: 
- istio
comments: false
metaAlignment: center
categories: 技术博文
---
很多企业都会面临从单体应用向微服务架构的转型，也会衍生出更多的分布式场景需求。随着规模和复杂度的不断增长，如何才能更好的理解、高效的管理**服务网格**呢？

<!-- more -->
#### 一、初识 Istio
`Istio` 发音「意丝帝欧」，重音在**意**上。官方给出的 `Istio` 的总结，简单明了：
```
Istio lets you connect, secure, control, and observe services.
```
{% alert info %}
连接、安全、控制和观测服务。
{% endalert %}

![官方介绍](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/istio/intro.png)

简单来说，`Istio` 针对现有的服务网格，提供一种简单的方式将连接、安全、控制和观测的模块，与应用程序或服务隔离开来，从而开发人员可以将更多的精力放在核心的业务逻辑上，以下是 `Istio` 的核心功能：

1.`HTTP`、`gRPC`、`WebSocket` 和 `TCP` 流量的自动负载均衡；
2.通过丰富的路由规则、重试、故障转移和故障注入，可以对流量行为进行细粒度控制；
3.可插入的策略层和配置 `API`，支持访问控制、速率限制和配额；
4.对出入集群入口和出口中所有流量的自动度量指标、日志记录和追踪；
5.通过强大的基于身份的验证和授权，在集群中实现安全的服务间通信；


#### 二、流程架构
`Istio` 服务网格逻辑上分为**数据平面**（`Control Plane`）和**控制平面**（`Data Plane`），架构图如下所示：

![Istio 架构图](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/istio/framework.png)

1.**数据平面**由一组以 `Sidecar` 方式部署的智能代理 `Envoy` 组成。

`Envoy` 被部署为 `Sidecar`，和对应服务在同一个 `Kubernetes pod` 中。这允许 `Istio` 将大量关于流量行为的信号作为属性提取出来，而这些属性又可以在 `Mixer` 中用于执行策略决策，并发送给监控系统，以提供整个网格行为的信息。

{% alert success %}
这些代理可以调节和控制微服务及 `Mixer` 之间所有的网络通信；
{% endalert %}

2.控制平面负责管理和配置代理来路由流量。此外控制平面配置 `Mixer` 以实施策略和收集遥测数据。
- `Mixer`：策略和请求追踪；
- `Pilot`：服务发现，智能路由，弹性（超时、重试、熔断器等）（`Envoy sidecar` 提供服务发现功能，- 为智能路由（例如 `A/B` 测试、金丝雀部署等）和弹性（超时、重试、熔断器等）提供流量管理功能）
- `Citadel`：分发 `TLS` 证书到智能代理
- `Sidecar injector`：可以允许向应用中无侵入的添加功能，避免为了满足第三方需求而添加额外的代码。

#### 三、核心模块
上文提到了很多技术名词，我们逐一解释：

1.什么是 `Sidecar` 模式？
`Sidecar` 是一种将应用功能从应用本身剥离出来作为单独进程的设计模式，可以允许向应用中无侵入的添加功能，避免为了满足第三方需求而添加额外的代码。在软件架构中，`Sidecar` 附加到主应用，或者叫父应用上，以扩展、增强功能特性，同时 `Sidecar` 与主应用是松耦合的。

{% alert info %}
Sidecar 是一种单节点、多容器的应用设计形式，主张以额外的容器来扩展或增强主容器。
{% endalert %}

2.`Envoy` 的作用是什么？
`Envoy` 是一个独立的进程，旨在与每个应用程序服务器一起运行。所有 `Envoy` 组成了一个透明的通信网格，其中每个应用程序发送和接收来自本地主机的消息，并且不用知道网络拓扑。

与传统的服务通信服务的库方法相比，进程外架构有两个实质性好处：
* Envoy支持任何编程语言写的服务。只用部署一个Envoy就可以在Java、C++、Go、PHP、Python等服务间形成网格。面向服务的体系结构使用多个应用程序框架和语言的情况越来越普遍。Envoy以透明的方式弥合了这些差距。
* 任何使用过大型面向服务的体系结构的人都知道，部署库升级可能会非常痛苦。Envoy可以在整个基础设施中迅速部署和升级。

更多详情请参考：[什么是 Envoy ?](https://www.jianshu.com/p/a6f7f46683e1?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation
)

3.

#### 四、项目实践
![Istio 架构图](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/istio/practice.png)