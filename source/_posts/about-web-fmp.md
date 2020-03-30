---
title: 网站性能指标 - FMP
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/fmp/logo.png
thumbnailImagePosition: left
tags: 
- web
- lighthouse
- fmp
- optimization
comments: false
metaAlignment: center
categories: 技术博文
---
对于前端童鞋来说，优化是一个永恒的话题。上线并不是网站开发的终点，如何吸引用户、提升用户体验，应该成为我们 `F2E` 的追求目标。
<!-- more -->
如果你对优化还不了解，建议先阅读如下两篇文章：
1. [网站优化实战](http://jartto.wang/2019/02/16/web-optimization/)
2. [网站优化，这些工具你一定用得着](http://jartto.wang/2019/09/08/web-optimization-tools/)

当然，衡量网站性能的指标有很多，今天我们就来看一个「令人费解而又头疼」的指标 - `FMP`。

{% alert success %}
`First Meaningful Paint` 是指页面的首要内容出现在屏幕上的时间。
{% endalert %}

#### 一、FMP 之怪现象
目前尚无标准化的 `FMP` 定义，因此也没有性能条目类型。 部分原因在于很难以通用的方式确定「有效」对于所有页面意味着什么。但是，一般来说，在单个页面或单个应用中，最好是将 `FMP` 视为主角元素呈现在屏幕上的时刻。

所以，我们经常会面临这样的问题：
![Lighthouse 评分](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/fmp/lighthouse.png)
`FCP` 在可接受范围，但是 `FMP` 却完全失控。

也可能是这样的问题：
![Performance FMP](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/fmp/fmp1.png)

或许还有这样的问题：
![Performance FMP](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/fmp/fmp2.png)

为什么结构类似的站点，`FMP` 加载却千差万别。要了解 `FMP` 我们需要知道它的计算规则，下面让我们一层层抽丝剥茧。

#### 二、刨根问底
究竟是什么导致 `FMP` 的时机差距如此之大？或许我们可以从 `FMP` 定义来说起。

`When FMP and FCP are the same time in seconds, they share the same identical score. If FMP is slower than FCP, say when there's iframe content loading, then the FMP score will be lower than the FCP score.`

什么意思呢，我们先来看一张官方图片：
![FMP 评分规则](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/fmp/fmp.png)

如果 `FCP` 是 `1.5s`，`FMP` 是 `3s`，那么 `FCP` 分数将会是 `99`，但是 `FMP` 分数将是 `75`。

除了上述影响外，我们还需要关注 `Lighthouse V3` 的记分规则：
![记分规则](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/fmp/score.png)

虽然 `FMP` 权重仅为 `1` ，很遗憾，因为如上规则的存在，我们站点无法到达满分💯。

{% alert info %}
确定页面上最关键的主角元素之后，我们应确保初始脚本加载仅包含渲染这些元素并使其可交互所需的代码。
{% endalert %}

相关源码：
[Lighthouse FMP 源码](https://github.com/GoogleChrome/lighthouse/blob/master/lighthouse-core/audits/metrics/first-meaningful-paint.js)
[Score a perfect 100 in Lighthouse audits](https://medium.com/@aswin_s/score-a-perfect-100-in-lighthouse-audits-part-1-3199163037)

#### 三、重要结论
了解了相关计算规则之后，我们继续来剖析 `FMP`。对于不同的站点，首要内容是不同的，例如：
* 对于博客文章：「大标题」 + 「首屏文字」是首要内容；
* 对于百度或者 `Google` 的搜索结果页：「首屏的结果」就是首要内容；
* 对于淘宝等购物网站来说，图片会极为重要，因此它是首要内容；

{% alert danger %}
需要注意的是，通常首要内容是不包括 Headers 和导航条的。
{% endalert %}

为了方便理解，我们用一个简单的公式表示：「首次有效绘制 = 具有最大布局变化的绘制」。

很好，此刻我们不用纠结「首次有效渲染」了，转而去了解「最大布局变化的绘制」。基于 `Chromium` 的实现，这个绘制是使用 `LayoutAnalyzer` 进行计算的，它会收集所有的布局变化，当布局发生最大变化时得出时间。

具有最大布局变化的绘制如何计算呢？
1.侦听页面元素的变化；
2.遍历每次新增的元素，并计算这些元素的得分总；
3.如果元素可见，得分为 `1 * weight`，如果元素不可见，得分为 `0`；


还是很抽象，我们继续探索 `LayoutAnalyzer`，在源码中得到如下公式：
{% alert info %}
布局显著性 = 添加的对象数目 / max(1, 页面高度 / 屏幕高度)
{% endalert %}

这下清晰了，既然可以算出来，那么优化 `FMP` 指日可待。从上面可以看出来「布局显著性」是通过添加对象数目与页面高度来计算的。似乎对象数目成了解决问题的关键节点。


我们继续探索，如何去找到对象数目呢？很简单，打开 `Chrome DevTool`，切到 `Layout` 面板。如果你还不会使用 `Layout` 面板，可以先看看[网站优化工具](http://jartto.wang/2019/09/08/web-optimization-tools/)。

这里就不展开了，直接上图，看看红框部分：
![Layout Objects](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/fmp/layout.png)

惊不惊喜，我们精简 `DOM` 似乎可以将公式中分子变小，或者让页面高度大于屏幕高度。到这里，所有谜团都解开了，优化 `FMP` 也就变得毫无挑战了。

相关源码：
[LayoutAnalyzer 源码](https://cs.chromium.org/chromium/src/third_party/blink/renderer/core/layout/layout_analyzer.h?type=cs&q=LayoutAnalyzer&sq=package:chromium&g=0&l=76)
[Tracing Results](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/trace-event-reading)
[Understanding about:tracing results](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/trace-event-reading)


#### 五、总结
我们从扑簌迷离的 `FMP` 表象一层层找到了 `Lighthouse` 的记分规则，又从 `Tracing Results` 中得知最大布局变化的计算规则，因此转向 `LayoutAnalyzer` 源码研究，最终找到 `Layout Objects`，从而解决了问题。虽然波折，但结局令人舒适。

最后，再啰嗦一下，以下结论对理解 `FMP` 很重要：
* 对于博客文章：「大标题」 + 「首屏文字」是首要内容；
* 对于百度或者 `Google` 的搜索结果页：「首屏的结果」就是首要内容；
* 对于淘宝等购物网站来说，图片会极为重要，因此它是首要内容；

如果你觉得还是过于复杂，不妨去试试 `SSR` 吧，`FMP` 将不再是烦恼。

文章最后，打个小广告吧。如果你想搭上在线教育的快车，快速成长，不妨加入我们。一起成长，一起学习，一起挑战更多有趣的事情，「跟谁学-高途课堂」欢迎你，请将简历私我～
