---
title: 网站地图方案汇总，教你玩转可视化
date: 2019-01-06 22:07:39
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/map/logo.png
thumbnailImagePosition: left
tags: 
- 地图
- google
- 高德
- 百度
- 腾讯
- mapbox
comments: false
metaAlignment: center
categories: 前端分享
---
数据可视化一直是大数据时代的核心，因为可视化能被更多的人看懂，数据才有了价值。如果你的网站要做可视化，除了各类统计图外，唯一不可或缺的元素就是地图。
<!-- more -->
不用担心，我已经帮你整理好了一份超详细的网站地图方案，快来看看吧。

#### 一、方案汇总
{% alert success %}
Top 1：Mapbox（推荐指数：☆☆☆☆☆）
{% endalert %}

[`Mapbox`](https://www.mapbox.com/) 地图风格会让你耳目一新，同时如同游戏般体验的地图动画效果更是如虎添翼。需要商业化或者一些重要推广活动的场景，可以用来数据演示，效果非常酷炫。唯一遗憾就是，`Mapbox` 是收费的。

![mapbox](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/map/mapbox.png)


{% alert success %}
Top 2：Carto（推荐指数：☆☆☆☆☆）
{% endalert %}

[`Carto`](https://carto.com/) 是使用最佳数据流构建功能强大的位置智能应用程序的平台。当然，也是收费的。

![carto](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/map/carto.gif)

{% alert success %}
Top 3：Google Map（推荐指数：☆☆☆☆☆）
{% endalert %}
[`Google`](https://developers.google.com/maps/) 地图就不用多说了，全世界都在流行，大部分地图公司数据和接口都是基于 `Google` 的。除此之外，`Google Map` 交互和设计也很不错。如果你需要做全球化地图可视化，建议使用 `Google Map`。

![google](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/map/google.png)

{% alert success %}
Top 4：deck.gl 推荐指数：☆☆☆☆
{% endalert %}
[deck.gl](http://deck.gl/#/) 是一个 WebGL 驱动的框架，用于对大型数据集进行可视化探索性数据分析。

![deck-gl](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/map/deck-gl.png)

{% alert success %}
Top 5：AMap 推荐指数：☆☆☆☆
{% endalert %}

高德地图（[AMap](https://lbs.amap.com/)）算是国内做的比较好的地图服务，功能强大，文档全面，对开发者友好。基本上可以满足一些数据可视化需求，此外，高德地图完全免费。

![amap](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/map/amap.png)


{% alert success %}
Top 6：BMap 推荐指数：☆☆☆☆
{% endalert %}
百度地图加上 `ECharts` 一站式可视化，对开发者有着不小的吸引力。完善的中文文档和 `Demo`，强大的组件开发。此外，百度地图也是免费试用。

![bmap](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/map/bmap.png)

[`ECharts`](http://lbsyun.baidu.com) 内接百度地图
![echarts](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/map/echarts.png)

{% alert success %}
Top 7：QQ.Map 推荐指数：☆☆☆☆
{% endalert %}
腾讯地图[QQ.Map](https://lbs.qq.com)，个人觉得仅次于高德和百度，但其应用场景却无处不在。滴滴，美团，摩拜，大众点评这些都使用了腾讯地图。要跟用户发生关系，当然得找大型社交平台了。

![qqmap](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/map/qqmap.png)

{% alert success %}
Top 8：FengMap 推荐指数：☆☆☆☆
{% endalert %}
[蜂鸟](https://www.fengmap.com/)视图具备专业的可视化地图数据生产团队，可满足各领域客户对高精度三维模型、室内地图的需求，提供各类大型复杂场景的三维地图模型制作服务。

![fmap](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/map/fmap.jpeg)


#### 二、使用场景
上面介绍了很多款地图，总体来说，各有优缺点。我来大概总结一下：

1.如果你对预算没有要求，而且需要很酷炫的功能，建议使用 Mapbox；
2.如果对地图改动较大，建议选择 Google Map，灵活的 API 可以让你更友好的扩展；
3.如果对细节要求很高，譬如：要精确显示某一条街道。那么尽量不要选国外地图，高德、百度、腾讯都是不错的选择；
4.如果你有其他数据可视化的要求，可以考虑 ECharts ＋ BMap 组合，比较容易无缝接入；
5.如果2C的话，并且想要用户黏性，那么可以试试腾讯地图；
6.如果想要更加立体或者3D效果，偏向于室内应用，如商场，酒店之类的场景，可以考虑蜂鸟室内地图；

{% alert success %}
地图没有好坏之分，贴合自己的使用场景，选择最好的地图方案，才能达到最佳的效果。
{% endalert %}




