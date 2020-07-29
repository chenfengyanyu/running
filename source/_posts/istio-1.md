---
title: 强大的 Istio
date: 2020-07-24 06:44:51
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/istio/logo.png
thumbnailImagePosition: left
tags: 
- istio
comments: false
metaAlignment: center
categories: 技术博文
---
很多企业都会面临从单体应用向微服务架构的转型，也会衍生出更多的分布式场景需求。随着规模和复杂度的不断增长，如何才能更好的理解和高效的管理**服务网格**呢？

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

简单来说，`Istio` 针对现有的服务网格，提供一种简单的方式将连接、安全、控制和观测的模块，与应用程序或服务隔离开来，从而开发人员可以将更多的精力放在核心的业务逻辑上，以下是 `Istio` 的核心功能概述：

1.`HTTP`、`gRPC`、`WebSocket` 和 `TCP` 流量的自动负载均衡；
2.通过丰富的路由规则、重试、故障转移和故障注入，可以对流量行为进行细粒度控制；
3.可插入的策略层和配置 `API`，支持访问控制、速率限制和配额；
4.对出入集群入口和出口中所有流量的自动度量指标、日志记录和追踪；
5.通过强大的基于身份的验证和授权，在集群中实现安全的服务间通信；


#### 二、流程架构
`Istio` 服务网格逻辑上分为数据平面和控制平面：
1.数据平面由一组以 `Sidecar` 方式部署的智能代理 `Envoy` 组成。这些代理可以调节和控制微服务及 `Mixer` 之间所有的网络通信。
2.控制平面负责管理和配置代理来路由流量。此外控制平面配置 `Mixer` 以实施策略和收集遥测数据。

![官方介绍](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/istio/framework.png)

1.`Control Plane`
`Mixer`：策略和请求追踪
`Pilot`：服务发现，智能路由，弹性（超时、重试、熔断器等）（`Envoy sidecar` 提供服务发现功能，为智能路由（例如 `A/B` 测试、金丝雀部署等）和弹性（超时、重试、熔断器等）提供流量管理功能）
`Citadel`：分发 `TLS` 证书到智能代理
`Sidecar injector`：可以允许向应用中无侵入的添加功能，避免为了满足第三方需求而添加额外的代码。

2.`Data Plane`
`Envoy`：是一个独立的进程，旨在与每个应用程序服务器一起运行。所有 `Envoy` 组成了一个透明的通信网格，其中每个应用程序发送和接收来自本地主机的消息，并且不用知道网络拓扑。






#### 三、核心模块


#### 四、使用示例