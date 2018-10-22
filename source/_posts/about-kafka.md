---
title: 了解 Kafka
date: 2018-10-12 05:21:52
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/kafka.png
thumbnailImagePosition: left
tags: 
- kafka
comments: false
metaAlignment: center
categories: 技术博文
---
经常听后端童鞋提到 `Kafka` 这个词，加上业务慢慢有了交集，对 `Kafka` 的好奇越来越强烈。索性花点时间，一探究竟，顺便扩展一下技能树。

<!-- more -->
#### 一、Kafka 是什么？
`Kafka` 是分布式发布-订阅消息系统。它最初由 `LinkedIn` 公司开发，之后成为 `Apache` 项目的一部分。`Kafka` 是一个分布式的，可划分的，冗余备份的持久性的日志服务。

{% alert info %}
Kafka 是用于构建实时数据管道和流应用程序。具有横向扩展，容错，wicked fast（变态快）等优点。
{% endalert %}

#### 二、简单理解
直接去解释概念可能比较枯燥，不如先委婉的理解一下。

举个[例子](http://orchome.com/kafka/index)，生产者消费者，生产者生产鸡蛋，消费者消费鸡蛋，生产者生产一个鸡蛋，消费者就消费一个鸡蛋，假设消费者消费鸡蛋的时候噎住了（系统宕机了），生产者还在生产鸡蛋，那新生产的鸡蛋就丢失了。

或者另一种情况：生产者很强劲（大交易量的情况），生产者 1 秒钟生产 100 个鸡蛋，消费者 1 秒钟只能吃 50 个鸡蛋，那要不了一会，消费者就吃不消了（消息堵塞，最终导致系统超时），消费者拒绝再吃了，「鸡蛋」又丢失了。

这个时候我们放个篮子在它们中间，生产出来的鸡蛋都放到篮子里，消费者去篮子里拿鸡蛋，这样鸡蛋就不会丢失了，都在篮子里，而这个篮子就是`Kafka`。

鸡蛋其实就是「数据流」，系统之间的交互都是通过「数据流」来传输的，也称为报文，也叫「消息」。消息队列满了，其实就是篮子满了，「鸡蛋」放不下了，那赶紧多放几个篮子，其实就是 `Kafka` 的扩容。

{% alert success %}
综上所述，Kafka 的概念就很好理解了，它就是那个「篮子」。
{% endalert %}

#### 三、使用场景
通过简单形象的比喻，相信很多童鞋都大概了解 `Kafka` 是怎么一回事了。

{% alert warning %}
那么我们要不要放「篮子」，以及什么情况我们需要去放这个「篮子」？
{% endalert %}

1.消息系统
对于一些常规的消息系统，`Kafka` 是个不错的选择。

2.检测网站活性
`Kafka` 可以作为「网站活性跟踪」的最佳工具。可以将网页/用户操作等信息发送到 `Kafka` 中。并实时监控，或者离线统计分析等。

3.日志系统
`Kafka` 的特性决定它非常适合作为「日志收集中心」;

{% alert success %}
kafka是一个分布式消息队列。一般在架构设计中起到解耦、削峰、异步处理的作用。
{% endalert %}

#### 四、重要概念
- Producers：生产者，就是它来生产「鸡蛋」的。
- Consumers：消费者，生出的「鸡蛋」它来消费。
- topic：你把它理解为标签，生产者每生产出来一个鸡蛋就贴上一个标签（topic），消费者可不是谁生产的「鸡蛋」都吃的，这样不同的生产者生产出来的「鸡蛋」，消费者就可以选择性的吃了。
- broker：就是篮子喽。


{% alert info %}
除了上面几个概念，还有一个也非常重要，那就是 `zookeeper`。
{% endalert %}


1) `Producer` 端使用 `zookeeper` 用来发现 `broker` 列表，以及和 `Topic` 下每个 `partition leader` 建立 `socket` 连接并发送消息。
2) `Broker` 端使用 `zookeeper` 用来注册 `broker` 信息，已经监测 `partition leader` 存活性。
3) `Consumer` 端使用 `zookeeper` 用来注册 `consumer` 信息,其中包括 `consumer` 消费的 `partition` 列表等，同时也用来发现 `broker` 列表，并和 `partition leader` 建立 `socket` 连接，并获取消息。

#### 五、Kafka 数据流
看完「鸡蛋」，「篮子」，我们逐渐清晰了，可这他娘的 `zookeeper` 又出来搅合了，是不是有点懵比了？不要着急，看看下面这张图，也许你就了然于胸了。

![kafka data flow](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/kafka1.png)

`Kafka` 的总体[数据流](https://www.jianshu.com/p/d3e963ff8b70)是这样的：`Producer` 往 `Broker` 里面的指定 `Topic` 中写消息，`Consumers` 从 `Brokers` 里面拉去指定 `Topic` 的消息，然后进行业务处理。

图中有两个 `topic`，`topic 0` 有两个 `partition`，`topic 1` 有一个 `partition` ，三副本备份。可以看到`consumer gourp 1` 中的 `consumer 2` 没有分到 `partition` 处理，这是有可能出现的。

那么，`Kafka` 又是如何[生产数据](https://www.jianshu.com/p/d3e963ff8b70)的？

![Producer](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/kafka2.png)

如上图所示：
- 首先创建一条记录，记录中一定要指定对应的 `topic` 和 `value` ，`key` 和 `partition` 可选。 
- 先序列化，然后按照 `topic` 和 `partition` ，放进对应的发送队列中。
- `kafka produce` 都是批量请求，会积攒一批，然后一起发送，不是调 `send()` 就进行立刻进行网络发包。

如果 `partition` 没填，那么情况会是这样的：
1.填写了 `key`：按照 `key` 进行哈希，相同 `key` 去一个 `partition`。（如果扩展了 `partition` 的数量那么就不能保证了）

2.没填 `key`：`round-robin` 来选 `partition`。

{% alert info %}
这些要发往同一个 `partition` 的请求按照配置，攒一波，然后由一个单独的线程一次性发过去。
{% endalert %}

#### 六、设计原理
`Kafka` 的设计初衷是希望作为一个统一的信息收集平台，能够实时的收集反馈信息，并需要能够支撑较大的数据量，且具备良好的容错能力。

1.持久性
2.性能
3.生产者
4.消费者
5.消息传送机制
6.复制备份
7.日志
8.分配

更多细节，请移步[Kafka 入门](http://www.aboutyun.com/thread-9341-1-1.html)。

#### 七、API 文档
学习新东西，一定要先去[官网](http://kafka.apache.org)转一圈。
- API
- 配置
- 常用操作
- 安全
- 流

#### 八、总结
由于文章篇幅原因，很多细节都没有说到。本文只起到推荐入门的作用，感兴趣的话，不妨深入学习一下。对于前端同学来说，这些就够了，因为如果你不常用，很可能很快就忘记了🙈。
