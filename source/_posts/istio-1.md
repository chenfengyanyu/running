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
很多企业都会面临从单体应用向微服务架构的转型，也会衍生出更多的分布式场景需求。随着规模和复杂度的不断增长，如何才能理解和高效管理**服务网格**呢？

<!-- more -->
#### 一、初识 Istio
`Istio` 发音「意丝帝欧」，重音在**意**上。官方给出的 `Istio` 的总结，很简单明了：
```
Istio lets you connect, secure, control, and observe services.
```
{% alert info %}
连接、安全、控制和观测服务。
{% endalert %}


简单来说，`Istio` 针对现有的服务网格，提供一种简单的方式将连接、安全、控制和观测的模块，与应用程序或服务隔离开来，从而开发人员可以将更多的精力放在核心的业务逻辑上，以下是 `Istio` 的核心功能概述：

1.`HTTP`、`gRPC`、`WebSocket` 和 `TCP` 流量的自动负载均衡；
2.通过丰富的路由规则、重试、故障转移和故障注入，可以对流量行为进行细粒度控制；
3.可插入的策略层和配置 `API`，支持访问控制、速率限制和配额；
4.对出入集群入口和出口中所有流量的自动度量指标、日志记录和追踪；
5.通过强大的基于身份的验证和授权，在集群中实现安全的服务间通信；


#### 二、流程架构



#### 三、核心模块
