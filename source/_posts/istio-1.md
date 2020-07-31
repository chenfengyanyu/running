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

1.**数据平面`Data Plane`**由一组以 `Sidecar` 方式部署的智能代理 `Envoy` 组成。

`Envoy` 被部署为 `Sidecar`，和对应服务在同一个 `Kubernetes pod` 中。这允许 `Istio` 将大量关于流量行为的信号作为属性提取出来，而这些属性又可以在 `Mixer` 中用于执行策略决策，并发送给监控系统，以提供整个网格行为的信息。

{% alert success %}
这些代理可以调节和控制微服务及 `Mixer` 之间所有的网络通信；
{% endalert %}

2.**控制平面`Control Plane`**负责管理和配置代理来路由流量，此外控制平面配置 `Mixer` 以实施策略和收集遥测数据。
- `Mixer`：策略和请求追踪；
- `Pilot`：提供服务发现功能，为智能路由（例如 `A/B` 测试、金丝雀部署等）和弹性（超时、重试、熔断器等）提供流量管理功能；
- `Citadel`：分发 `TLS` 证书到智能代理；
- `Sidecar injector`：可以允许向应用中无侵入的添加功能，避免为了满足第三方需求而添加额外的代码；

#### 三、核心模块
上文提到了很多技术名词，我们逐一解释：

1.什么是 `Sidecar` 模式？
`Sidecar` 是一种将应用功能从应用本身剥离出来作为单独进程的设计模式，可以允许向应用中无侵入的添加功能，避免为了满足第三方需求而添加额外的代码。在软件架构中，`Sidecar` 附加到主应用，或者叫父应用上，以扩展、增强功能特性，同时 `Sidecar` 与主应用是松耦合的。

{% alert info %}
Sidecar 是一种单节点、多容器的应用设计形式，主张以额外的容器来扩展或增强主容器。
{% endalert %}

2.`Envoy` 的作用是什么？
`Envoy` 是一个独立的进程，旨在与每个应用程序服务器一起运行。所有 `Envoy` 组成了一个透明的通信网格，其中每个应用程序发送和接收来自本地主机的消息，并且不用知道网络拓扑。

与传统的服务通信服务的库方法相比，**进程外架构**有两个实质性好处：
* `Envoy` 支持任何编程语言写的服务。只用部署一个 `Envoy` 就可以在 `Java`、`C++`、`Go`、`PHP`、`Python` 等服务间形成网格。面向服务的体系结构使用多个应用程序框架和语言的情况越来越普遍，`Envoy` 以透明的方式弥合了这些差距。
* 任何使用过大型面向服务的体系结构的人都知道，部署库升级可能会非常痛苦。`Envoy` 可以在整个基础设施中迅速部署和升级。

3.`Mixer`
 Mixer 是一个独立于平台的组件，负责在服务网格上执行访问控制和使用策略，并从 Envoy 代理和其他服务收集遥测数据。代理提取请求级属性，发送到 Mixer 进行评估。有关属性提取和策略评估的更多信息，请参见 Mixer 配置。

Mixer 中包括一个灵活的插件模型，使其能够接入到各种主机环境和基础设施后端，从这些细节中抽象出 Envoy 代理和 Istio 管理的服务。


4.`Pilot`
控制面中负责流量管理的组件为Pilot

Pilot 为 Envoy sidecar 提供服务发现功能，为智能路由（例如 A/B 测试、金丝雀部署等）和弹性（超时、重试、熔断器等）提供流量管理功能。它将控制流量行为的高级路由规则转换为特定于 Envoy 的配置，并在运行时将它们传播到 sidecar。

#### 四、Envoy 进阶
**1.服务的动态注册和发现**
{% alert info %}
Envoy 可以选择使用一组分层的动态配置 API 来进行集中管理。
{% endalert %}

这些层为 `Envoy` 提供了以下方面的动态更新: 
后端群集的主机、后端群集本身、`HTTP` 路由、侦听套接字和通信加密。为了实现更简单的部署, 后端主机发现可以通过 `DNS` 解析 (甚至完全跳过) 完成, 层也可以替换为静态配置文件。

**2.健康检查**
构建 `Envoy` 网格的建议方法是将服务发现视为最终一致的过程。 `Envoy` 包括一个运行状况检查子系统，该子系统可以选择对上游服务集群执行主动运行状况检查。然后，`Envoy` 使用服务发现和运行状况检查信息的联合来确定健康的负载均衡服务器。`Envoy` 还支持通过异常检测子系统进行被动运行状况检查。

**3.高级负载均衡**
分布式系统中不同组件之间的负载平衡是一个复杂的问题。由于 `Envoy` 是一个独立的代理而不是库，因此它能够在一个位置实现高级负载平衡技术，并使任何应用程序都可以访问。

目前 `Envoy` 包括支持自动重试、断路、通过外部速率限制服务限制全局速率、请求隐藏和异常值检测。未来计划为 `Request Racing` 提供支持。

**4.前端/边缘系统代理支持**
虽然 `Envoy` 主要是为服务通信系统而设计的，但对前端/边缘系统也是很有用的（可观测性、管理、相同的服务发现和负载平衡算法等）。`Envoy` 包含足够的功能，使其可用作大多数 `Web` 应用服务用例的边缘代理。

这包括作为 `TLS` 的终点、`HTTP/1.1` 和 `HTTP/2` 支持, 以及 `HTTP L7` 路由。

**5.最好的观察统计能力**
`Envoy` 的首要目标是使网络透明。但是在网络级别和应用程序级都无法避免的容易出现问题。`Envoy` 包含了对所有子系统的强有力的统计支持。 `statsd` (和其他兼容的数据提供程序) 是当前支持的统计接收器，插入不同的统计接收器也并不困难。

`Envoy` 可以通过管理端口查看统计信息，还支持通过第三方供应商进行分布式追踪。

更多详情请参考：[什么是 Envoy ?](https://www.jianshu.com/p/a6f7f46683e1?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation
)
#### 五、项目实践

根据 header 内容分发流量
istio 可以基于内容分发流量，在这里我们让普通用户全部访问 v1 版，而特殊用户（jason）访问 v2 版。
使用如下配置，并提交给 k8s。

![Istio 架构图](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/istio/practice.png)