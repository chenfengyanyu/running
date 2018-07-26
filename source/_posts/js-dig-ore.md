---
title: 何谓 JS 挖矿
date: 2017-11-08 12:03:45
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/dig0.png
thumbnailImagePosition: left
tags: 
- js
comments: false
metaAlignment: center
categories: 技术杂谈
---
突然间“挖矿”，“门罗币”这样的字眼充斥在很多自媒体的文章中，这极大引起了我的好奇，本着“不抛弃，不放过”的学习理念，我们来做一些简单的了解。
<!-- more -->
在 IT 界，我们更多听到的是：“码农”，“搬砖”这样的字眼。可是从今往后，你就会知道，原来还有“挖矿”这一说。

#### 一、挖矿与 JS 为何会联系起来？
{% alert info %}
网页中嵌入 Javascript, 一旦用户打开该网站，浏览器便会按照脚本的指令变成一个门罗币挖矿机。这一段附加的挖矿代码通常因为大量占用 CPU，使用户的计算机变得异常卡顿甚至无法正常使用。
{% endalert %}
这样的网站会嵌入了 Coinhive JavaScript Miner 代码。该代码是基于 CryptoNight 挖矿算法编写，该算法可以产出 CryptoNote 类网络货币，如 Monero (门罗币)、Dashcoin (达世币)，DarkNetCoin (暗网币)等。

网络安全公司 Adguard 于近期发表一份研究报告，指出逾 220 家热门网站运营者早期就已经从 CoelHive 寻找到加密货币 Monero 的采矿脚本，并通过用户电脑进行试验挖掘加密货币，从而获取巨额收益。据悉，目前共有 5 亿用户在不知情下运行 JS 脚本、**挖掘加密货币**。

#### 二、挖矿平台
顺藤摸瓜，我们找到了一个挖矿平台的网站，它是这么介绍的：
{% alert info %}
Monetize Your Business With Your Users' CPU Power
{% endalert %}

JS 使用方式也很简单，注册平台:
![dig sign up](http://7xvi3w.com1.z0.glb.clouddn.com/dig4.png-blog)
得到 Site Key 和 Secret Key:
![dig site key](http://7xvi3w.com1.z0.glb.clouddn.com/dig2.png-blog)
嵌入如下代码：
```html
<script src="https://coinhive.com/lib/coinhive.min.js"></script>


<script>
  var miner = new CoinHive.User('JXKRjYbooF1daPun4jz8O3eC3zQlBL6b', 'jartto');
  miner.start();
</script>
```
网站很人性化的还提供了一个 Dashboard 页面，方便去监控挖矿的进度:
![dig dashboard](http://7xvi3w.com1.z0.glb.clouddn.com/dig3.png-blog)

{% alert danger %}
CoinHive 是目前最受欢迎的 JS 挖矿引擎。安全专家表示，上述攻击手段主要是网站运营商在其网页内嵌入一段 JS 代码，只要用户访问，挖矿程序就会在网民的电脑中运行，从而占据大量系统资源，导致 CPU 利用率突然提升。
{% endalert %}

这里就不细说了，具体请查看：[A Crypto Miner for your Website](https://coinhive.com)。细心的童鞋可能发现了，在 CoinHive 设置面板中，需要我们填写 Monero Payment Adress，这又是什么？

#### 三、门罗币钱包
既然是在挖门罗币，那么自然引出了门罗币钱包。于是乎，我们找到了 [Monero](https://getmonero.org/downloads/)。

那就来试试，打开 Monero ，创建钱包：
![Monero](http://7xvi3w.com1.z0.glb.clouddn.com/dig5.png-blog)
创建完成：
![Monero](http://7xvi3w.com1.z0.glb.clouddn.com/dig6.png-blog)
接着，然后就得到了一个收款地址，也就是上文我们需要填写的 Monero Payment Adress：
![Monero payment](http://7xvi3w.com1.z0.glb.clouddn.com/dig7.png-blog)

{% alert success %}
到这里，终于形成了一个完整的闭环，挖矿 - 门罗币 - 交易。
{% endalert %}

#### 四、到底在挖掘什么？
如上所述，JS 挖矿代码会占用大量的客户端 CPU 资源，可是这有什么意义呢，这些代码在做什么呢？

很模糊的理解到，貌似“用户 CPU 计算能力”便是挖掘的目标。为了验证自己的猜想，我尝试了运行了一个例子：
<center>![demo png](http://7xvi3w.com1.z0.glb.clouddn.com/dig9.png)</center>
示例地址：[mining live demo](https://jartto.github.io/demo.html)

在钱包中，我们看到下面这样的描述，这也就解释了我们之前的疑惑：
- 挖矿同时能让您有机会赚取额外的门罗币，因为在挖矿时，您的计算机将被用来寻找 Monero 区块的解答，每当您找到一个区块的解答，您即可以获得其附带的门罗币奖励，祝您好运！
- 挖矿可增进 Monero 网络的安全性，只要越多使用者在挖矿，Monero 网络就会越难以被攻击。

下面这张动图可以很有意思，非常形象：
![gif](http://7xvi3w.com1.z0.glb.clouddn.com/dig8.gif-blog)

#### 五、如何感知以及防范？
其实这种 JS 挖矿的思路还是挺有意思的，用一段嵌入代码来整合网上的资源。唯一让人很不舒服的地方就是：它是未经用户授权而私自调用。不但降低了用户体验，而且也威胁到了用户隐私。

挖矿代码通常因为大量占用 CPU，使用户的计算机变得异常卡顿甚至无法正常使用。所以这也是我们防范的突破口，我们可以尝试以下的方式：

方式一：
打开浏览器的任务管理器，如果发现 CPU 占用率明显飙升，请注意该页面；

方式二：
既然是嵌入的 JS 代码片段，怎么可能没有痕迹？查看页面源文件，查找 `coinhive` 关键字，如果有的话，基本可以确定；

{% alert success %}
所以，当你的网页卡顿无比的时候，不要总觉得是电脑性能问题或者网站的优化问题，切记首先排除“恶意挖矿”。
{% endalert %}

方式三：
一些去广告的浏览器插件，可以禁止 JS 挖矿。

{% alert info %}
当然，如果挖矿代码加一些截流控制和 CPU 使用预警的话，用户就更难察觉了。
{% endalert %}

#### 六、怎么样去看待这样的操作？
就拿[贴吧](http://tieba.baidu.com/p/2404500396)中看到的这句话，说的不无道理：

首先，挖矿并不是自己制造货币，挖矿得来的受益来源于去中心化的货币结点（或者你可以理解成整个 p2p 网络）给予矿工的奖励分成、挖矿实际上是在向市场输送更多货币（因为供求关系嘛）。

其次，比特币的激励机制是这样的：如果矿工的受益不足以应付起成本也就是积极性被抑制时，就降低 gigahash 的生成难度，来刺激矿工参与挖矿，如果矿工的挖矿效率太高到易导致通货膨胀就进行抑制，升高难度。

因此从全局上来看个体的矿工永远不会大赚大亏。简而言之就是为整个比特币市场注入新的比特币（数据散列）然后市场会有一定的激励机制（按比例给你分配比特币）因为如果不给你没人会这么干 就好比你的计算机就是矿工 比特币就是酬劳。

{% alert danger %}
很多情况下，这些挖矿脚本的行为就像恶意程序，未经许可侵入用户的计算机和利用计算机资源。广告屏蔽工具现在成了防止劫持 CPU 的安全工具了。
{% endalert %}

#### 七、总结
起初不了解挖矿，所以非常好奇，然后就查询了相关资料。现在懂了，违反用户意愿并私自挖矿的行为必将受到用户的强烈反对，这与网站加恶意广告不无区别。

法律的灰色地带，也许只有时间能够证明这件事情的利弊了。

#### 八、参考
[门罗币是如何利用网页挖矿的？](http://liujinkai.com/2017/10/28/js-miner/?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)
[网站代码中暗藏JS挖矿机脚本](http://www.freebuf.com/articles/web/149107.html)
[贴吧－挖矿](http://tieba.baidu.com/p/2404500396)
[JS 采矿脚本，秘密挖掘加密货币](http://www.safedog.cn/news.html?id=2240)
