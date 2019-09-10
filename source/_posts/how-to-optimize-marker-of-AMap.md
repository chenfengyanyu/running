---
title: 如何优化高德地图（AMap）Marker 动画
date: 2017-08-28 23:56:28
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker.jpg
thumbnailImagePosition: left
tags: 
- angular
- amap
- 高德地图
comments: true
metaAlignment: center
categories: 技术博文
---
一般需要接入地图的应用，我们肯定会用到 Marker 即点标记对象。为了使点标记更新时有一些灵性，我尝试添加了掉落动画。然而，事与愿违，遂有此文。
<!-- more -->
#### 一、更新Marker
我们先来看一下问题代码（因为项目原因，这里使用了 angular，当然你也可以使用 react，vue 以及其它你喜欢的 js 框架）：
```js
$scope.updateMarkers = (data) => {
  if(!$scope.myMarkers) return;

  let content = `<span class="marker"><svg class="icon" aria-hidden="true"><use xlink:href="#icon-jartto"></use></svg></span>`;
  
  $scope.myMarkers.map(item => {
    if(data.sn === item.G.extData) {
      item.setAnimation('AMAP_ANIMATION_DROP');
      item.setContent(content); 
    }
  });
};
```
这里我们实现的是，当有数据推送过来，调用 updateMarkers 方法。
1. 预先写好了 content 字符串；
2. 遍历所有点标记，找到当前点标记；
3. setAnimation 为点标记设置动画；
4. setContent 画点；

{% alert info %}
完全按照[官方教程-自定义点标记](http://lbs.amap.com/api/javascript-api/example/marker/custom-icon-content)操作，看起来并没有什么问题，我们继续。
{% endalert %}

#### 二、性能被牺牲了，CPU居高不下？
很多用户反应地图页时间久了会变的卡顿，所以问题直接指向了 sockets 不定时更新 Marker 这里，请看下面这张图：
![CPU高占用图示](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker1.png)
{% alert warning %}
如上述简单的四步更新点位，初步怀疑是 setAnimation 导致，我们来验证一下。
{% endalert %}
我们试着关闭地图原生动画，注释掉 setAnimation 动画代码，代码如下：
```js
$scope.updateMarkers = (data) => {
  if(!$scope.myMarkers) return;

  let content = `<span class="marker"><svg class="icon" aria-hidden="true"><use xlink:href="#icon-jartto"></use></svg></span>`;
  
  $scope.myMarkers.map(item => {
    if(data.sn === item.G.extData) {
      // item.setAnimation('AMAP_ANIMATION_DROP');
      item.setContent(content); 
    }
  });
};
```
准备工作完毕，再来监测一下，如下图：
![CPU高占用图示](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker2.png)
这就夸张了，AMap的内置动画为什么如此耗费性能，究竟是什么原因呢？

#### 三、页面性能检测（Performance）
打开 Chrome DevTools－Performance，我们来看一下，地图原生的 Marker 动画帧频截图：
![地图原生动画帧频](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker3.png)
正如我们看到的，13.5 fps 的帧频，动画卡顿是必然的。那么其他数据呢，如下图：

![地图原生动画 Performance](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker4.png)
这里需要注意三个数据：
1. Frame 的帧频一直处于较低水平，截图中为 9 fps；
2. 大片紫色区域，CPU使用率很高；
3. 绿色区域，Composite Layers ，复合层也不逊色，像林海；

#### 四、重写动画
既然可以确定原生动画对性能有影响，那么我们对代码做一些简单修改：
1. 去掉 setAnimation 动画；
2. 为content 添加 play_drop 动画类；

代码如下：
```js
$scope.updateMarkers = (data) => {
  if(!$scope.myMarkers) return;

  let content = `<span class="marker play_drop"><svg class="icon" aria-hidden="true"><use xlink:href="#icon-jartto"></use></svg></span>`;
  
  $scope.myMarkers.map(item => {
    if(data.sn === item.G.extData) {
      item.setContent(content); 
    }
  });
};
```
css 代码添加了我们自定义的一个动画效果，代码如下：
```css
.play_drop{
  -webkit-animation: bounce 1.5s ease-in 0s 1;
  animation: bounce 1.5s ease-in 0s 1;
  will-change: transform;
}

@keyframes bounce {
  from, 20%, 53%, 80%, to {
    animation-timing-function: cubic-bezier(0.215, 0.610, 0.355, 1.000);
    transform: translate3d(0,0,0);
  }
  40%, 43% {
    animation-timing-function: cubic-bezier(0.755, 0.050, 0.855, 0.060);
    transform: translate3d(0, -76px, 0);
  }
  70% {
    animation-timing-function: cubic-bezier(0.755, 0.050, 0.855, 0.060);
    transform: translate3d(0, -38px, 0);
  }
  90% {
    transform: translate3d(0,-10px,0);
  }
}
```
先上图，对比看看：
![重写动画帧频](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker5.png)
太惊奇了，帧频已经到了 56.9 fps，这样基本很流畅了，如黄油般流畅（这个夸张了啊，不过我喜欢这个比喻😂）。再来看看 Performance ：

![地图原生动画 Performance](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker6.png)
很好，动画已经可以出现高帧渲染了，然而，别高兴的太早，后面这些东西搞什么？
![后面的低频动画](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker7.png)
{% alert danger %}
奇怪，为什么还是有低频动画，这又是什么影响的？越来越有意思，我们继续深入。
{% endalert %}
#### 五、Chrome 动画调试器（Animations）
这里其实已经有两个方向了，其一，查找为什么动画帧数不稳定；其二，什么导致了cpu过高；
首先，我们打开动画调试器：Chrome DevTools - more tools - Animations
![Animations 面板](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker18.png)
在这里，我们可以做以下几件事：
- 通过打开Animation Inspector(动画检查器)捕获动画。它会自动检测动画并将它们分组。
- 通过减慢动画，重播动画，或查看源代码，来检查动画。
- 通过更改动画的时间，延迟，持续时间或关键帧偏移来修改动画。

我们对动画再做一些细小的调整，如果你需要深入了解，请查看[检查动画](http://www.css88.com/doc/chrome-devtools/inspect-styles/animations/)。

#### 六、页面渲染情况（Rendering）
动画看来并没有什么大问题，我们接着来看相关渲染：Chrome DevTools - more tools - Rendering
![Rendering 面板](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker9.png)

页面渲染情况，我们用图来说明一下：
![Rendering 面板](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker10.png)
勾选Paint Flashing后，我们可以看到每次更新marker所重绘的区域，绿色框中的内容正是我们需要渲染的图层，这里就可以先排除图层渲染的问题了。

既然说到这里了，那么影响页面重绘的因素，都有哪些呢？
1、页面滚动
2、互动操作
1).Dom节点被Javascript改变，导致Chrome重新计算页面的layout。
2).动画不定期更新。
3).用户交互，如hover导致页面某些元素页面样式的变化。
4).调整窗口大小 和 改变字体
5).内容变化，比如用户在input框中输入文字
6).激活 CSS 伪类，比如 :hover
7).计算 offsetWidth 和 offsetHeight 属性
8).增加或者移除样式表

扩展阅读：
1. [Chrome渲染分析之Rendering工具使用](http://zhangzhaoaaa.iteye.com/blog/2183196)
2. [Chrome 的 Rendering 监听器](http://www.cnblogs.com/foreverZ/p/5817855.html)

#### 七、看看图层情况？（Layers）
到这里，问题还没有解决，我们接着来看：Chrome DevTools - more tools - Layers
{% alert success %}
一旦加载并解析页面，它就在浏览器中作为许多Web开发人员熟悉的结构来表示：DOM。然而，当呈现一个页面时，浏览器有一系列不直接暴露给开发者的中间表示。这些结构中最重要的是层。
{% endalert %}
![Layers 面板](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker11.png)
如上图，可以看到Layers板块分为三部分：
1. dom树；
2. 复合层边界显示；
3. 详细信息

这里就有意思了我们先了解一下第三个区域：
－Size：容器大小；
－Compositing Reasons：形成复合层原因
－Memory estimate：内存占用估算
－Paint count：绘制数量
－Slow scroll regions：缓慢滚动区域

检查了所有 dom 结构，找到如下几个原因：
1. 右下角按钮position:fixed属性，导致Paint count一直在增长；
2. 存在与其他复合层相互关联；
3. LayerForSquashingContents
4. backface-visibility:hidden属性
5. 垂直滚动 layerForVerticalScrollbar

那就照着一项项优化呗，那么结果又如何呢？
![复合层优化图示](http://7xvi3w.com1.z0.glb.clouddn.com/amap-marker12.png)

#### 八、GPU 与 复合层？（Layers）
在Chrome中其实有几种不同的类型：
- RenderLayers渲染层，这是负责的DOM子树
- GraphicsLayers图形层，这是负责的RenderLayers子树。

在这里，后者是最有趣的，因为GraphicsLayers被上传到GPU纹理。所以“层”的概念从这里出来说GraphicsLayer。

那么什么是GPU纹理？
把它作为一个位图图像，从主存储器（即内存）显存（即显存，GPU上）。一旦在GPU上，你可以把它映射到一个网状的几何体-在视频游戏或CAD程序中，这种技术被用来给骨骼3D模型“皮肤”。纹理可以被廉价地映射到不同的位置和转换，通过将它们应用到一个非常简单的矩形网格中。这就是3D CSS的工作原理，对于快速滚动也是很好的——但是这两方面都要更多。

影响GPU渲染的因素包括：
- 3D CSS 属性；
- video／canvas 元素；
- 一些随时改变的元素；
[Layers panel](http://umaar.github.io/devtools-nov-2013/slides/#14)

扩展阅读：
[Accelerated Rendering in Chrome](https://www.html5rocks.com/en/tutorials/speed/layers/)
[正确的使用GPU动画](https://www.smashingmagazine.com/2016/12/gpu-animation-doing-it-right/)

#### 九、优化 Dom
动画也重写了，图层渲染也检查了，复合层也优化了，是时候对dom做一些更改了。
{% alert info %}
这里抛出一个问题，字符串 dom 渲染快还是创建的 dom 元素渲染快？
{% endalert %}
正在试验，后续继续完善。

#### 十、总结
经过了一些列枯燥而又繁琐的处理，我们的优化也告一段落了。再来回答一下文章一开始跑出的两个问题：
1. 动画不流程，耗 CPU；
我们优化了动画，帧数，复合层等问题，提升了性能；
2. CPU 使用率忽高忽低；
由于 sockets 推送的不确定性，导致消息爆炸式推送，短时间内拉升了 CPU 使用率。而我们的最终处理办法也包含对 sockets 的一些处理，例如：使 sockets 形成队列，平缓的推送，前端可以有足够的时间渲染，而不是急剧上升。不管是前端来平缓处理sockets，还是后端来处理，问题都会得到解决。

在网上找到了两张有意思的图，可以表达一下优化前后的差别：
优化前与优化后：{% image fancybox left  group:travel http://7xvi3w.com1.z0.glb.clouddn.com/amap-markerscreencasting-realisation-before.gif 200px %}{% image fancybox left clear group:travel http://7xvi3w.com1.z0.glb.clouddn.com/amap-markerscreencasting-realisation-after.gif 200px %}

很高兴你能耐心看到这里，断断续续写了这么些内容，自己也是边研究边学习。感觉收获颇多，分享出来，大家共同进步吧。

#### 十一、参考资料
1. [AMap Marker](http://lbs.amap.com/api/javascript-api/reference/overlay#Marker)
2. [官方教程－自定义点标记](http://lbs.amap.com/api/javascript-api/example/marker/custom-icon-content)
3. [检查动画](http://www.css88.com/doc/chrome-devtools/inspect-styles/animations/)
4. [Chrome 的 Rendering 监听器](http://www.cnblogs.com/foreverZ/p/5817855.html)
5. [Chrome渲染分析之Rendering工具使用](http://zhangzhaoaaa.iteye.com/blog/2183196)
6. [Accelerated Rendering in Chrome](https://www.html5rocks.com/en/tutorials/speed/layers/)
7. [正确的使用GPU动画](https://www.smashingmagazine.com/2016/12/gpu-animation-doing-it-right/)
8. [Layers panel](http://umaar.github.io/devtools-nov-2013/slides/#14)