---
title: DevOps 简介
date: 2018-11-30 16:38:05
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/devops/logo.png
thumbnailImagePosition: left
tags: 
- devops
comments: false
metaAlignment: center
categories: 技术博文 
---
头一次听到这个东西，真的是一脸懵逼。感觉应该类似项目管理中的一些理念，却又不是很清晰。哎，请原谅我的孤陋寡闻。
<!-- more -->
#### 一、是什么？
[百度百科](https://baike.baidu.com/item/devops/2613029?fr=aladdin)中是这么定义的：`DevOps`（ `Development` 和 `Operations` 的组合词）是一组过程、方法与系统的统称，用于促进开发（应用程序/软件工程）、技术运营和质量保障（ `QA` ）部门之间的沟通、协作与整合。

它是一种重视「软件开发人员（`Dev`）」和「`IT` 运维技术人员（`Ops`）」之间沟通合作的文化、运动或惯例。透过自动化软件交付和架构变更的流程，来使得构建、测试、发布软件能够更加地快捷、频繁和可靠。

{% alert info %}
为了按时交付软件产品和服务，开发和运营工作必须紧密合作。
{% endalert %}

#### 二、常见开发模型
看了专业的解释，我们不难理解到，`DevOps` 是一种开发模型。那么什么是开发模型，又有哪些常见的开发模型呢？
1.什么是开发模型？
[软件开发模型](https://baike.baidu.com/item/软件开发模型/223381?fr=aladdin)(Software Development Model)是指软件开发全部过程、活动和任务的结构框架。软件开发包括需求、设计、编码和测试等阶段，有时也包括维护阶段。 软件开发模型能清晰、直观地表达软件开发全过程，明确规定了要完成的主要活动和任务，用来作为软件项目工作的基础。

2.常见的开发模型
- 瀑布式开发
- 敏捷开发
- DevOps

#### 三、化繁为简
`DevOps` 一词的来自于 `Development` 和 `Operations` 的组合，突出重视软件开发人员和运维人员的沟通合作，通过自动化流程来使得软件构建、测试、发布更加快捷、频繁和可靠。

{% alert info %}
DevOps 是为了填补开发端和运维端之间的信息鸿沟，改善团队之间的协作关系。
{% endalert %}

不过需要澄清的一点是，从开发到运维，中间还有测试环节。`DevOps` 其实包含了三个部分：开发、测试和运维。

专家们总结出了下面这个 `DevOps` 能力图，良好的闭环可以大大增加整体的产出：
![circle](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/devops/circle.png)

#### 四、使用场景
以下几方面因素可能促使一个组织引入 `DevOps`：
1、使用敏捷或其他软件开发过程与方法；
2、业务负责人要求加快产品交付的速率；
3、虚拟化和云计算基础设施（可能来自内部或外部供应商）日益普遍；
4、数据中心自动化技术和配置管理工具的普及；
5、有一种观点认为，占主导地位的传统美国式管理风格（斯隆模型 vs 丰田模型）会导致「烟囱式自动化」，从而造成开发与运营之间的鸿沟，因此需要 `DevOps` 能力来克服由此引发的问题。

{% alert info %}
DevOps 经常被描述为「开发团队与运营团队之间更具协作性、更高效的关系」。
{% endalert %}

由于团队间协作关系的改善，整个组织的效率因此得到提升，伴随频繁变化而来的生产环境的风险也能得到降低。

#### 五、DevOps 对应用程序发布的影响
{% alert danger %}
在很多企业中，应用程序发布是一项涉及多个团队、压力很大、风险很高的活动。
{% endalert %}

然而在具备 `DevOps` 能力的组织中，应用程序发布的风险很低，原因如下：
- 减少变更范围
与传统的瀑布式开发模型相比，采用敏捷或迭代式开发意味着更频繁的发布、每次发布包含的变化更少。由于部署经常进行，因此每次部署不会对生产系统造成巨大影响，应用程序会以平滑的速率逐渐生长。

- 加强发布协调
靠强有力的发布协调人来弥合开发与运营之间的技能鸿沟和沟通鸿沟；采用电子数据表、电话会议、即时消息、企业门户（`wiki`、`sharepoint`）等协作工具来确保所有相关人员理解变更的内容并全力合作。

- 自动化
强大的部署自动化手段确保部署任务的可重复性、减少部署出错的可能性。

与传统开发方法那种大规模的、不频繁的发布（通常以季度或年为单位）相比，敏捷方法大大提升了发布频率（通常以天或周为单位），同时也增大了出错的概率。

#### 六、DevOps 又有啥不同？它有什么好处？
减少变更范围与传统的瀑布式开发模型相比，采用敏捷或迭代式开发意味着更频繁的发布、每次发布包含的变化更少。由于部署经常进行，因此每次部署不会对生产系统造成巨大影响，应用程序会以平滑的速率逐渐生长。

{% alert info %}
加强发布协调靠强有力的发布协调人来弥合开发与运营之间的技能鸿沟和沟通鸿沟。
{% endalert %}

这里需要明确两点认识：
1.开发是由功能性需求（通常与业务需求直接相关）驱动的。
2.运营是由非功能性需求（例如可获得性、可靠性、性能等）驱动的。

#### 七、精益 7 原则
- 杜绝浪费
- 内建质量
- 创建知识(放大学习)
- 延迟决策(尽量延迟决定)
- 快速交付
- 尊重人员(团队授权)
- 全局优化

#### 八、DevOps 八荣八耻
偶然在网上看到了这个，觉得很形象，所以拿过来（感谢原作者）：
- 以可配置为荣，以硬编码为耻。
- 以系统互备为荣，以系统单点为耻。
- 以随时可重启为荣，以不能迁移为耻。
- 以整体交付为荣，以部分交付为耻。
- 以无状态为荣，以特殊化为耻。
- 以自动化工具为荣，以人肉操作为耻。
- 以无人值守为荣，以人工介入为耻。

真正理解上面这些规则，你才能熟练的应用于项目开发。

#### 九、反思
在了解 `DevOps` 之后，我陷入了深深的恐惧之中。总以为可以通过不断学习技术来弥补短板，然而现实并非如此。越是努力的学习，越是发现自己掌控之外的东西数不胜数。

所以，有时间真得去琢磨琢磨项目管理相关内容了。唯有博览群书，才能博学多才！

#### 十、参考
[1.百度百科](https://baike.baidu.com/item/devops/2613029?fr=aladdin)
[2.DevOps简介](https://www.cnblogs.com/liufei1983/p/7152013.html)
[3.一分钟了解DevOps](https://www.cnblogs.com/jetzhang/p/6068773.html)
[4.Devops 到底是什么？](https://www.cnblogs.com/servicehot/p/6510199.html)