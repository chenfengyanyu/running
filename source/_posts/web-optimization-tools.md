---
title: 网站优化，这些工具你一定用得着
date: 2019-09-08 18:52:36
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/logo.png
thumbnailImagePosition: left
tags: 
- web
- optimization
- tools
comments: false
metaAlignment: center
categories: 技术博文
---
工欲善其事，必先利其器。在「[网站优化实战](http://jartto.wang/2019/02/16/web-optimization/)」中我们提到了一些优化的相关经验，并没有对优化工具展开讨论，这节就让我们一起上手实践吧！
<!-- more -->
#### 一、概要
{% alert success %}
关于优化工具，我们主要从两方面说起：「性能评估工具」和「优化工具」。
{% endalert %}

1.性能评估工具
- Lighthouse
- PageSpeed
- YSlow

2.优化工具我们主要依赖「Chrome DevTools」，大致如下：
- Network
- Performance
- Show Third Party Badges
- Block Request URL
- Coverage
- DOM
- Rendering
- Layer

#### 二、Lighthouse
1.`Lighthouse` 安装
`Chrome Setting` - 更多工具 - 扩展程序 - 打开 `Chrome` 网上应用店 - `Lighthouse`

2.插件 - 生成报告
![lighthouse](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/lighthouse.png)

{% alert success %}
报告是我们的一个重要参考指标，这是网站评估的通用方法。
{% endalert %}

当然，网站也会有不同的类别，**关注指标**也不尽相同，后续我们会继续探讨「如何制定合理的网站优化性能指标」。

3.优化建议
`Lighthouse` 比较人性化的点在于他既提出了问题，同时也提出了解决建议。
![performance](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/performance.png)

#### 三、PageSpeed
1.使用 `PageSpeed`
我们可以在「Chrome DevTools」菜单栏中找到并打开：
![pagespeed](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/pagespeed.png)

2.分析报告
![pagescore](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/pagescore.png)

#### 四、Chrome DevTools - Network
1.关于 `Network` 我们重点关注标注的 3 处
![network](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/network.png)

2.`Timing` 也是优化不可缺少的工具：
![network1](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/network1.png)

补充说明一下：
`TTFB`：等待初始响应所用的时间，也称为第一字节的时间，这是我们判断服务器以及网络状况的重要指标。

{% alert info %}
此时间将捕捉到服务器往返的延迟时间，以及等待服务器传送响应所用的时间。
{% endalert %}

#### 五、Chrome DevTools - Performance

1.概览
![perf](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/perf.png)

2.版面主要由 4 部分构成
- 控制面板：录制，清除，配置记录期间需要捕获的信息
- `Overview`：页面性能的高级汇总，以及页面加载情况
- 火焰图：`CPU` 堆叠追踪的可视化
- 总览：饼图记录各部分耗时情况

3.Overview 详解
- FPS
每秒帧数。绿色竖线越高，`FPS` 越高。 `FPS` 图表上的红色块表示长时间帧，很可能会出现卡顿。

- CPU
`CPU` 资源。此面积图指示消耗 `CPU` 资源的事件类型。

- NET
每条彩色横杠表示一种资源。横杠越长，检索资源所需的时间越长。
每个横杠的浅色部分表示等待时间（从请求资源到第一个字节下载完成的时间）。
深色部分表示传输时间（下载第一个和最后一个字节之间的时间）。

{% alert danger %}
需要特别注意，`Performance` 工具中的每一种颜色其实都有自己的含义。
{% endalert %}

- HTML 文件为蓝色。
- 脚本为黄色。
- 样式表为紫色。
- 媒体文件为绿色。
- 其他资源为灰色。


小技巧：
{% alert info %}
使用无痕模式，减少 Chrome 扩展程序会给应用的干扰。
{% endalert %}

4.火焰图
- Network
`Network` 这里我们可以看出来，我们资源加载的一个顺序情况。什么时间加载了什么资源，通过这些，我们更直观的知道资源是否并行加载。

- Frames
[上文](http://jartto.wang/2019/09/08/web-optimization-tools/)提及到的页面帧情况。

- Interactions

- Timings 中如下 5 个指标是我们优化的方向      
    - First Paint
    - DOMContentLoaded Event
    - Onload Event
    - First Contentful Paint
    - First Meaningful Paint

- Main：展示了主线程运行状况。
`X` 轴代表着时间，每个长条代表着一个 `event`。长条越长就代表这个 `event` 花费的时间越长。
`Y` 轴代表了调用栈 `call stack` 。

在栈里，上面的 `event` 调用了下面的 `event`。

![perf1](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/perf1.png)

注意红色警告：
![perf2](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/perf2.png)

- JS Heap          
`JavaScript` 运行过程中的大部分数据都保存在堆 `Heap` 中，所以 `JavaScript` 性能分析另一个比较重要的方面是**内存**，也就是**堆的分析**。

![perf3](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/perf3.png)

- 打开 Performance 监视器

![perf4](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/perf4.png)

#### 六、Chrome DevTools - Show Third Party Badges
很多情况下，并不是我们网站本身的问题，有可能你使用的三方资源拖累了站点性能。所以，我们需要使用 `Show Third Party Badges` 来进行排查。

1.测试站点：https://techcrunch.com/

2.打开控制面板：`Command + Shift + P`
![badge](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/badge.png)

3.打开 `Network`，注意资源前面的**彩色标志**
![badge1](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/badge1.png)


{% alert success %}
三方资源都被标记出来了，移除或者替换那些影响性能的东西。
{% endalert %}

#### 七、Chrome DevTools - Block Request URL
对于项目中不确定是否有用的资源，我们可以使用 `Block Request URL` 来排除。

1.选中资源 - 右键 - `Block Request URL`
![block](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/block.png)

{% alert info %}
阻止某些资源加载，**控制变量法**来排查页面性能问题。
{% endalert %}

#### 八、Chrome DevTools - Coverage
1.打开控制面板：`Command + Shift + P`

2.输入：`Show Coverage`
![corvage](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/corvage.png)

3.找到相应的文件，可以看到文件左侧已经标**记出了部分代码的使用情况**
![coverage](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/coverage.png)

解决思路也很简单：
尽可能去通过 `Webpack` 来[拆包](http://jartto.wang/2019/02/16/web-optimization/)，控制大小在 `40KB` 以下，移除那些未使用代码。

#### 九、Chrome DevTools - DOM
我们经常提到要优化 `Dom`，那么节点控制在什么范围才合理呢？
- 总共少于 1500 个节点
- 最大深度为 32 个节点
- 不要存在子节点超过 60 个节点的父节点

查看所有 `DOM` 节点数：
```js
document.querySelectorAll('*').length
```
查看子元素个数：
```js
document.querySelectorAll('body > *').length
```

{% alert danger %}
通常，只在需要时查找创建 DOM 节点的方法，并在不再需要时**销毁**它们。
{% endalert %}


#### 十、Chrome DevTools - Rendering
关于重渲对页面的影响，我们就不多说了。那么如何知道**页面的渲染**过程呢？我们可以通过 `Rendering` 来可视化查看。

1.打开 Rendering 选项
![render](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/render.png)

2.刷新页面
{% alert warning %}
绿色区域越重，说明重复渲染的次数越多，通过优化 DOM 来减少无效渲染。
{% endalert %}

![render](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/render.gif)

#### 十一、Chrome DevTools - Layer
你可能会很好奇，为什么要查看图层？
{% alert info %}
这是因为，我们经常会在不知不觉的情况下搞乱了图层关系，或者增加了不合适的图层。
{% endalert %}

1.打开控制面板：`Command + Shift + P`
2.选择 `Layer` 选项
![layer](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/optimization-tools/layer.png)

是不是图层问题就清清楚楚的摆在眼前了～

#### 十二、总结
通过优化工具，我们可以轻而易举的对网站进行定位分析。之后就可以快速展开优化，让网站高性能的运转起来。优化，也不过如此。

后续我们会深入了解一些优化相关的原理细节，如果你有优化相关的问题，欢迎一起探讨，一起进步。

