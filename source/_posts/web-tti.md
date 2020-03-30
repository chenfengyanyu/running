---
title: 聚焦 Web 性能指标 TTI
date: 2020-03-29 22:23:53
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/tti/logo.jpg
thumbnailImagePosition: left
tags: 
- web
- tti
- optimization
comments: false
metaAlignment: center
categories: 技术博文
---
如果你经常做网站优化，可能会陷入一个性能指标的泥潭即「面向指标优化」。真正的用户体验从来不是指标决定，相反它应该最真实的反映用户行为。
<!-- more -->
所以本节我们就来研究 `TTI(Time to Interactive)`，话题展开之前，我们先来了解一些背景知识。

#### 一、RAIL 模型
`RAIL` 是一种以用户为中心的性能模型。每个网络应用均具有与其生命周期有关的四个不同方面，且这些方面以不同的方式影响着性能：
![RAIL 性能模型](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/tti/rail.png)

1.响应：输入延迟时间（从点按到绘制）小于 100 毫秒。
用户点按按钮（例如打开导航）。

2.动画：每个帧的工作（从 JS 到绘制）完成时间小于 16 毫秒。
用户滚动页面，拖动手指（例如，打开菜单）或看到动画。 拖动时，应用的响应与手指位置有关（例如，拉动刷新、滑动轮播）。 此指标仅适用于拖动的持续阶段，不适用于开始阶段。

3.空闲：主线程 JS 工作分成不大于 50 毫秒的块。
用户没有与页面交互，但主线程应足够用于处理下一个用户输入。

4.加载：页面可以在 1000 毫秒内就绪。
用户加载页面并看到关键路径内容。

{% alert info %}
如果要提升网站用户体验，RAIL 是个不错的评估模型。
{% endalert %}

#### 二、解读 TTI（页面可交互时间）
`TTI` 指的是应用既在视觉上都已渲染出了，可以响应用户的输入了。要了解 `TTI`，我们需要知道它的计算规则，先来看下面这张图：
![TTI](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/tti/tti.png)

在[官方文档](https://docs.google.com/document/d/1GGiI9-7KeY3TPqS3YT271upUVimo-XiL5mwWorDUD4c/preview#)中找到了如下描述：
`First Idle is the first early sign of time where the main thread has come at rest and the browser has completed a First Meaningful Paint.`

`Time to Interactive is after First Meaningful Paint. The browser’s main thread has been at rest for at least 5 seconds and there are no long tasks that will prevent immediate response to user input.`

我们可以简单的理解一下：
1.`First Idle` 是主线程处于静止状态且浏览器已完成 `First Meanfulful Paint` 的第一个早期迹象；
2.`TTI` 在 `FMP` 之后，浏览器主线程静止至少 `5s`，并且没有可以阻断用户交互响应的「长任务」。

如果你对 `FMP` 还不了解，不妨先看看这篇文章：[网站性能指标 - `FMP`](http://jartto.wang/2020/03/15/about-web-fmp/)。除此之外，第二条中提到的「长任务」又是什么呢？

#### 三、Long Task（长任务）
对于「长任务」，我们通过如下图示说明：
![长任务](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/tti/longtask.png)

{% alert info %}
对于用户而言，任务耗时较长表现为滞后或卡顿，而这也是目前网页不良体验的主要根源。
{% endalert %}

如何测量 `Long Task`？
```js
// Jartto's Demo
const observer = new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    // TODO...
    console.log(entry);
  }
});

observer.observe({entryTypes: ['longtask']});
```
控制台输出结果如下：
```js
{
  "name": "self",
  "entryType": "longtask",
  "startTime": 315009.59500001045,
  "duration": 99.9899999878835,
  "attribution": [
    {
      "name": "unknown",
      "entryType": "taskattribution",
      "startTime": 0,
      "duration": 0,
      "containerType": "window",
      "containerSrc": "",
      "containerId": "",
      "containerName": ""
    }
  ]
}
```

`Long Tasks API` 可以将任何耗时超过 `50` 毫秒的任务标示为可能存在问题，并向应用开发者显示这些任务。 选择 `50` 毫秒的时间是为了让应用满足在 `100` 毫秒内响应用户输入的 `RAIL` 指导原则。

实际开发过程中，我们可以通过一个 `hack` 来检查页面中「长任务」的代码：
```js
// detect long tasks hack
(function detectLongFrame() {
    let lastFrameTime = Date.now();
    requestAnimationFrame(function() {
        let currentFrameTime = Date.now();
        if (currentFrameTime - lastFrameTime > 50) {
        // Report long frame here...
        }
        detectLongFrame(currentFrameTime);
    });
}());
```


#### 四、如何计算 TTI？
在计算之前，我们先来看一下 `Timing API`：
![Timing API](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/tti/timing.png)

`Google` 官方文档中有一段描述：
`Note: Lower Bounding FirstInteractive at DOMContentLoadedEnd
DOMContentLoadedEnd is the point where all the DOMContentLoaded listeners finish executing. It is very rare for critical event listeners of a webpage to be installed before this point. Some of the firstInteractive definitions we experimented with fired too early for a small number of sites, because the definitions only looked at long tasks and network activity (and not at, say, how many event listeners are installed), and sometimes when there are no long tasks in the first 5-10 seconds of loading we fire FirstInteractive at FMP, when the sites are often not ready yet to handle user inputs. We found that if we take max(DOMContentLoadedEnd, firstInteractive) as the final firstInteractive value, the values returned to reasonable region. Waiting for DOMContentLoadedEnd to declare FirstInteractive is sensible, so all the definitions introduced below lower bound firstInteractive at DOMContentLoadedEnd.`

所以，我们可以通过 `domContentLoadedEventEnd` 来粗略的进行估算：
```js
// 页面可交互时间 
TTI: domContentLoadedEventEnd - navigationStart,
```

{% alert info %}
[domContentLoadedEventEnd](https://w3c.github.io/navigation-timing/#dom-performancenavigationtiming-domcontentloadedeventend)：文档的 DOMContentLoaded 事件的结束时间。
{% endalert %}

`The domContentLoadedEventEnd attribute MUST return a DOMHighResTimeStamp with a time value equal to the time immediately after the current document's DOMContentLoaded event completes.`

如果你觉得上述计算过于复杂，可以通过 `Google` 实验室提供的 `Polyfill` 来获取。

#### 五、TTI 指标监控
我们可以通过 [`Google TTI Polyfill`](https://github.com/GoogleChromeLabs/tti-polyfill)来对 `TTI` 进行监测。
1.安装
```bash
npm install tti-polyfill
```
2.使用
```js
import ttiPolyfill from './path/to/tti-polyfill.js';
ttiPolyfill.getFirstConsistentlyInteractive(opts).then((tti) => {
  // Use `tti` value in some way.
});
```

很简单，就不细说了。推荐几篇 `TTI` 相关文章：
[First Interactive and Consistently Interactive](https://docs.google.com/document/d/1GGiI9-7KeY3TPqS3YT271upUVimo-XiL5mwWorDUD4c/preview#)
[User-centric performance metrics](https://web.dev/user-centric-performance-metrics/#tracking_tti)
[Focusing on the Human-Centric Metrics](https://calibreapp.com/blog/time-to-interactive)

文章最后，打个小广告吧。如果你想搭上在线教育的快车，快速成长，不妨加入我们。一起成长，一起学习，一起挑战更多有趣的事情，「跟谁学-高途课堂」欢迎你，请将简历私我～






