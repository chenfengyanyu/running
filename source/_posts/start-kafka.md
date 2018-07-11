---
title: 快速上手 Kafka
date: 2018-10-28 21:50:48
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/kafka.png
thumbnailImagePosition: left
tags: 
- kafka
- demo
comments: false
metaAlignment: center
categories: 技术博文
---
[上一篇](http://jartto.wang/2018/10/12/about-kafka/)我们对 Kafka 做了很多概念性的解释，试图去理解它的内部机制。但这还远远不够，所以，这里我们来起手一个 Demo，加强理解。
<!-- more -->
#### 一、下载 kafka
要快速上手，我们需要[下载](https://www.apache.org/dyn/closer.cgi?path=/kafka/2.0.0/kafka_2.11-2.0.0.tgz) kafka 的包文件，因为下面的演示基于此。

#### 二、解压并进入目录
找到上面下载好的 `tgz` 文件，解压并进入目录：
```bash
> tar -xzf kafka_2.11-2.0.0.tgz
> cd kafka_2.11-2.0.0
```

#### 三、启动服务
`kafka` 使用了 `ZooKeeper`，所以我们需要先启动 `ZooKeeper` 服务。这里直接执行：
```
> bin/zookeeper-server-start.sh config/zookeeper.properties
```

然后，再启动 `kafka` 服务：
```
> bin/kafka-server-start.sh config/server.properties
```

控制台会输出：
```
...
[2018-10-27 14:41:16,579] INFO [KafkaServer id=0] started (kafka.server.KafkaServer)
```
{% alert success %}
[KafkaServer id=0] started 可以看到服务启动了。
{% endalert %}

#### 四、创建 topic
这里，我们尝试创建一个 `test topic`，它只包含一个分区，只有一个副本。
```
> bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
```

控制台会输出：
```
Created topic "test”.
```

我们可以通过下面的命令，查看 `topic`列表：
```
> bin/kafka-topics.sh --list --zookeeper localhost:2181
```
控制台会输出：`test`，当然，我们也可以配置 `broker` 来创建 `topic`，从而代替手动去创建。

#### 五、发送消息
{% alert info %}
到这里，我们前期的准备工作都做好了，接下来我们会尝试发送一些消息。
{% endalert %}

`kafka` 附带一个命令行客户端，它将从文件或标准输入中获取输入，并将其作为消息发送到 `kafka` 集群。 默认情况下，每行将作为单独的消息发送。

运行 `producer` 然后在控制台输入消息，发送到服务器：
```
> bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```
启动后，就进入了生产者模式，也就是可以我们可以发送消息了，输入：
```
> Hello, jartto!
> Let’s go!
```

#### 六、接收消息
`kafka` 还有一个命令行使用者，它会将消息转储到标准输出，这就是我们的 `consumer`。我们执行：
```
> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```
启动后，就会输出：
```
Hello, jartto!
Let's go!
```
这样，我们就接收到了消息。这时候，在 `producer` 窗口，我们输入的消息，都会在 `consumer` 显示。下面这张图可以清楚的看到我们的所有服务，主要包含 `ZooKeeper`，`Topic`，`Producer`，`Consumer`：
![]()

#### 七、设置多代理集群
上面我们演示了单一的 `borker`，这并没有什么意思。我们来尝试一下 `multi-broker` 集群，让我们把集群扩展到三个节点，这才是真正的使用场景。

首先，我们来修改一下配置文件，拷贝出两份副本：
```
> cp config/server.properties config/server-1.properties
> cp config/server.properties config/server-2.properties
```

然后稍微做一些调整，我们要在增加两个「篮子」了，我们需要为每个「篮子」编号，指定他们的监听端口，以及日志输出位置。
```
# config/server-1.properties
broker.id=1
listeners=PLAINTEXT://:9093
log.dirs=/tmp/kafka-logs-1

# config/server-2.properties
broker.id=2
listeners=PLAINTEXT://:9094
log.dirs=/tmp/kafka-logs-2
```
{% alert info %}
因为我们启动了 Zookeeper 和 一个节点，所以这里只要启动刚才新增的这俩节点就可以了。
{% endalert %}
启动 server-1：
```
> bin/kafka-server-start.sh config/server-1.properties &
…
[2018-10-27 16:06:24,642] INFO [KafkaServer id=1] started (kafka.server.KafkaServer)
```
启动 server-2：
```
> bin/kafka-server-start.sh config/server-2.properties &
...
[2018-10-27 16:07:19,178] INFO [KafkaServer id=2] started (kafka.server.KafkaServer)
```
{% alert warning %}
需要注意的是：这里的 & 是默认后台启动。
{% endalert %}

接下来，我们创建一个新的 `topic`，包含 3 个副本：
```
> bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic
```

那么问题来了，我们现在有了一个集群，如果知道每个 `broker` 在干什么，如何查看呢？
```
> bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic

Topic:my-replicated-topic    PartitionCount:1    ReplicationFactor:3    Configs:
    Topic: my-replicated-topic    Partition: 0    Leader: 2    Replicas: 2,1,0Isr: 2,1,0
```
输出解释：第一行是所有分区的摘要，其次，每一行提供一个分区信息，因为我们只有一个分区，所以只有一行。

这里需要解释三个概念：
1.`leader`：该节点负责该分区的所有的读和写，每个节点的 `leader` 都是随机选择的。
2.`replicas`：备份的节点列表，无论该节点是否是 `leader` 或者目前是否还活着，只是显示。
3.`isr`：「同步备份」的节点列表，也就是活着的节点并且正在同步 `leader`。

当然，我们也可以看看 `test topic` 的相关信息：
```
> bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic test
Topic:test  PartitionCount:1    ReplicationFactor:1 Configs:
    Topic: test Partition: 0    Leader: 0   Replicas: 0 Isr: 0
```
这并不奇怪，刚才创建的主题没有 `Replicas`，并且在服务器 0 上，我们创建它的时候，集群中只有一个服务器，所以是 0 。

接下来，我们来使用 `my-replicated-topic` 创建几条消息：
```
> bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my-replicated-topic
输入：
> my test message 1
> my test message 2
```

然后，我们来消费这几条消息：
```
> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic my-replicated-topic
```
可以看到，控制到收到两条消息：
```
my test message 1
my test message 2
```

#### 八、集群容错性
上面我们创建了有三个节点的集群，那么是时候检测一下容错性了。正如上面的情况，`Broker 1` 是 `leader`，我们杀掉他的进程：
```
> ps aux | grep server-1.properties
7564 ttys002    0:15.91 /System/Library/Frameworks/JavaVM.framework/Versions/1.8/Home/bin/java…
> kill -9 7564
```

备份节点之一成为新的 `leader` ，而 `broker1` 已经不在同步备份集合里了。我们看一下目前的情况：
```
> bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic
Topic:my-replicated-topic   PartitionCount:1    ReplicationFactor:3 Configs:
    Topic: my-replicated-topic  Partition: 0    Leader: 2   Replicas: 1,2,0 Isr: 2,0
```

但即使最初接受写入的 `leader` 已经被干掉了，但并消息并没有丢：
```
> bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic my-replicated-topic
my test message 1
my test message 2
```

#### 九、使用 Kafka Connect 导入/导出数据
有时候，我们想导入或者导出一些数据，这时候就可以使用 `Kafka Connect` 来完成。对于大多数系统，可以使用 `Kafka Connect`，而不需要编写自定义集成代码。

{% alert info %}
Kafka Connect 是一个可扩展的工具，运行连接器，实现与外部系统交互的自定义逻辑。
{% endalert %}

首先，`Mock` 一些数据，新建一个 `txt` 文件：
```
> echo -e "foo\nbar" > test.txt
```

接下来，我们开始 2 个连接器运行在独立的模式，这意味着它们运行在一个单一的，本地的，专用的进程。我们提供 3 个配置文件作为参数。首先是 `Kafka Connect` 处理的配置，包含常见的配置，例如要连接的 `Kafka broker` 和数据的序列化格式。其余的配置文件都指定了要创建的连接器。包括连接器唯一名称，和要实例化的连接器类。以及连接器所需的任何其他配置。

```
> bin/connect-standalone.sh config/connect-standalone.properties config/connect-file-source.properties config/connect-file-sink.properties
```

`kafka` 附带了这些示例的配置文件，并且使用了刚才我们搭建的本地集群配置并创建了 2 个连接器：
第一个是源连接器，从输入文件中读取并发布到Kafka主题中；
第二个是接收连接器，从 `kafka` 主题读取消息输出到外部文件。

在启动过程中，你会看到一些日志消息，包括一些连接器实例化的说明。一旦 `kafka Connect` 进程已经开始，导入连接器应该读取从 `test.txt` 和写入到 `topic connect-test` 导出连接器从主题 `connect-test` 读取消息写入到文件
`test.sink.txt` ，我们可以通过验证输出文件的内容来验证数据数据已经全部导出：
```
more test.sink.txt
foo
bar
```

注意，导入的数据也已经在 `Kafka` 主题 `connect-test` 里，所以我们可以使用该命令查看这个主题：
```
bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic connect-test --from-beginning
{"schema":{"type":"string","optional":false},"payload":"foo"}
{"schema":{"type":"string","optional":false},"payload":"bar"}
…
```

连接器继续处理数据，因此我们可以添加数据到文件并通过管道移动：
```
echo "Another line" >> test.txt
```

你应该会看到出现在消费者控台输出一行信息并导出到文件。

#### 十、API 文档
学习新东西，一定要先去[官网](http://kafka.apache.org)转一圈。下面是比较重要的一些点：
- API
- 配置
- 常用操作
- 安全
- 流

`API` 文档还是有很多重点内容的，这部分等回头看完了，整理整理，再行补充吧。

#### 十一、总结
由于文章篇幅原因，很多细节都没有说到。本文只起到入门的作用，感兴趣的话，不妨深入学习一下。对于前端同学来说，这些就够了，因为如果你不常用，很可能很快就忘记了🙈。
