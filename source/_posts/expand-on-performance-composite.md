---
title: 详谈层合成（composite） 
date: 2017-09-29 22:46:23
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/20161208_order.png
thumbnailImagePosition: left
tags: 
- performance
- composite
comments: false
metaAlignment: center
categories: 技术博文
---
前不久写了一篇关于如何使用 Chrome DevTools [优化高德地图动画](http://jartto.wang/2017/08/28/how-to-optimize-marker-of-AMap/)的文章，其中提到了 composite，但是并没有细谈。思考许久，还是觉得有必要再总结一下。
<!-- more -->
#### 一、什么是 composite ？
通俗来说：在 DOM 树中每个节点都会对应一个 LayoutObject，当他们的 LayoutObject 处于相同的坐标空间时，就会形成一个 RenderLayers ，也就是渲染层。
RenderLayers 来保证页面元素以正确的顺序合成，这时候就会出现层合成（composite），从而正确处理透明元素和重叠元素的显示。

{% alert info %}
一旦加载并解析页面，它就在浏览器中作为许多Web开发人员熟悉的结构来表示：DOM。然而，当呈现一个页面时，浏览器有一系列不直接暴露给开发者的中间表示。这些结构中最重要的是层。
{% endalert %}

到这里，层的概念是有了。composite 翻译过来就是我们常说的合成，那么他是怎么工作的？

#### 二、RenderLayers 与 GraphicsLayers
先贴出一张流程图，稍后我们细说：
![layer trees](http://7xvi3w.com1.z0.glb.clouddn.com/layers/1.png)
在 Chrome 中其实有几种不同的层类型：
- RenderLayers 渲染层，这是负责对应 DOM 子树
- GraphicsLayers 图形层，这是负责对应 RenderLayers 子树。

提到 RenderLayers 不得不说 RenderObjects ：
RenderObjects 保持了树结构，一个 RenderObjects 知道如何绘制一个 node 的内容， 他通过向一个绘图上下文（GraphicsContext）发出必要的绘制调用来绘制 nodes。

每个 GraphicsLayer 都有一个 GraphicsContext，GraphicsContext 会负责输出该层的位图，位图是存储在共享内存中，作为纹理上传到 GPU 中，最后由 GPU 将多个位图进行合成，然后 draw 到屏幕上，此时，我们的页面也就展现到了屏幕上。
{% alert info %}
GraphicsContext 绘图上下文的责任就是向屏幕进行像素绘制(这个过程是先把像素级的数据写入位图中，然后再显示到显示器)，在chrome里，绘图上下文是包裹了的 Skia（chrome 自己的 2d 图形绘制库）
{% endalert %}
某些特殊的渲染层会被认为是合成层（Compositing Layers），合成层拥有单独的 GraphicsLayer，而其他不是合成层的渲染层，则和其第一个拥有 GraphicsLayer 父层公用一个。

#### 三、composite 隐式合成
对于隐式合成，[CSS GPU Animation](https://www.smashingmagazine.com/2016/12/gpu-animation-doing-it-right/) 中是这么描述的：
{% alert success %}
This is called implicit compositing: One or more non-composited elements that should appear above a composited one in the stacking order are promoted to composite layers — i.e. painted to separate images that are then sent to the GPU.
{% endalert %}
大概意思就是：一个或多个非合成元素应出现在堆叠顺序上的合成元素之上，被提升到合成层，即被绘制成分离的图像，然后将图像交给 GPU 处理。

我们先来看下面的图示：
<iframe src="https://sergeche.github.io/gpu-article-assets/examples/example4.html#.b:anim-translate" height="280" frameborder="no" allowtransparency="true" style="width: 100%;"></iframe>

假设一种场景，我们需要 A 显示在 B 之上，然后为 B 添加移动的动画，这里就会出现一个逻辑问题：B 因为有动画，被提升到了合成层，最终在 GPU 上合成了屏幕图像，而 A 需要显示在 B 之上，我们并没有做任何处理。所以为了使 A 和 B 正常显示，我们需要设置 z-index ，这时浏览器将强制提升 A 为复合层，随后进行 repaint。

这时候，composite 隐式合成就出现了。当然，还有更多的场景，我们继续。

#### 四、影响 composite 因素
我们知道，在某些特定条件下，浏览器会主动将渲染层提至合成层，那么影响 composite 的因素有哪些？
1. 3D transforms: translate3d, translateZ 等;
2. video, canvas, iframe 等元素;
3. 通过 Element.animate() 实现的 opacity 动画转换;
4. 通过 СSS 动画实现的 opacity 动画转换;
5. position: fixed;
6. will-change;
7. filter;
8. 有合成层后代同时本身 overflow 不为 visible（如果本身是因为明确的定位因素产生的 SelfPaintingLayer，则需要 z-index 不为 auto）
等等...

这里只举出部分例子，[无线性能优化：Composite](http://taobaofed.org/blog/2016/04/25/performance-composite/)这篇文章描述的很详细，这里就不赘述了。

#### 五、层压缩与层爆炸
1.层压缩：
类似我们举出的层隐式合成的例子，可能简单的重叠就会产生大量的合成层，这样会占用很多无辜的 CPU 和 内存资源，严重影响了页面的性能。这一点浏览器也考虑到了，因此就有了层压缩（Layer Squashing）的处理。
{% alert danger %}
浏览器的自动的层压缩也不是万能的，有很多特定情况下，浏览器是无法进行层压缩的。
{% endalert %}
如下所示，而这些情况也是我们应该尽量避免的：
- 无法进行会打破渲染顺序的压缩
- video 元素的渲染层无法被压缩同时也无法将别的渲染层压缩到 video 所在的合成层上
- iframe、plugin 的渲染层无法被压缩同时也无法将别的渲染层压缩到其所在的合成层上
- 无法压缩有 reflection 属性的渲染层（squashingReflectionDisallowed）
- 无法压缩有 blend mode 属性的渲染层（squashingBlendingDisallowed）
- 当渲染层同合成层有不同的裁剪容器（clipping container）时，该渲染层无法压缩（squashingClippingContainerMismatch）
- 相对于合成层滚动的渲染层无法被压缩（scrollsWithRespectToSquashingLayer）
- 当渲染层同合成层有不同的具有 opacity 的祖先层（一个设置了 opacity 且小于 1，一个没有设置 opacity，也算是不同）时，该渲染层无法压缩（squashingOpacityAncestorMismatch，同 squashingClippingContainerMismatch）
- 当渲染层同合成层有不同的具有 transform 的祖先层时，该渲染层无法压缩（squashingTransformAncestorMismatch，同上）
- 当渲染层同合成层有不同的具有 filter 的祖先层时，该渲染层无法压缩（squashingFilterAncestorMismatch，同上）
- 当覆盖的合成层正在运行动画时，该渲染层无法压缩（squashingLayerIsAnimating），当动画未开始或者运行完毕以后，该渲染层才可以被压缩 

此处摘录自[无线性能优化：Composite](http://taobaofed.org/blog/2016/04/25/performance-composite/)，demo 请查看原文。

{% alert info %}
如果多个渲染层同一个合成层重叠时，这些渲染层会被压缩到一个 GraphicsLayer 中，以防止由于重叠原因导致可能出现的“层爆炸”。
{% endalert %}
2.层爆炸：
通过之前的介绍，我们知道同合成层重叠也会使元素提升为合成层，虽然有浏览器的层压缩机制，但是也有很多无法进行压缩的情况。也就是说除了我们显式的声明的合成层，还可能由于重叠原因不经意间产生一些不在预期的合成层，极端一点可能会产生大量的额外合成层，出现层爆炸的现象。
{% alert success %}
解决层爆炸的问题，最佳方案是打破 overlap 的条件，也就是说让其他元素不要和合成层元素重叠，譬如巧妙的使用 z-index 属性。
{% endalert %}

#### 六、内存消耗
上面提到了层合成的过程会产生内存消耗，那么我们如何来评估层消耗的内存，下面举例来说明：
```html
<!-- jartto test -->
<div id="a"></div>
<div id="b"></div>
```
```css
#a, #b {
 will-change: transform;
}

#a {
 width: 100px;
 height: 100px;
 background: rgb(255, 0, 0);
}

#b {
 width: 10px;
 height: 10px;
 background: rgb(255, 0, 0);
 transform: scale(10);
}
```
如上，我们创建了两个容器 #a 和 #b，#a 的物理尺寸是 100×100px（100×100×3 = 30000 字节），而 #b 只有10×10px（10×10×3 = 300 字节）但放大了 10 倍。#b 由于存在 will-change 属性，transform 动画将通过 GPU 来渲染图层。
{% alert info %}
我们通过图像的高度乘以图像的宽度来获得图像中像素的数量。然后，我们将其乘以3，因为每个像素都用三个字节（RGB）描述。那么不难理解，如果图像包含透明区域，我们要乘以4，因为需要额外的字节来描述透明度：（RGBA）：100×100×4 = 40000 字节。
{% endalert %}

从上面的例子中，我们得出了一个非常有意义的结论，从而帮助我们去做一些简单的优化。例如：如果你想要为一张大图添加动画，你可以先下载缩小版（原版 10% ）然后放大显示。这对用户是无感知的，但是我们却精简了页面加载，从而提升了用户体验。

#### 七、Reflowing and Repainting
很好，我们可以通过上述来评估内存消耗了，这里引出了两个术语 Reflow 和 Repaint ，简单温习一下：
- Reflow（回流）：浏览器要花时间去渲染，当它发现了某个部分发生了变化影响了布局，那就需要倒回去重新渲染。 
- Repaint（重绘）：如果只是改变了某个元素的背景颜色，文字颜色等，不影响元素周围或内部布局的属性，将只会引起浏览器的repaint，重画某一部分。 

{% alert info %}
Reflow要比Repaint更花费时间，也就更影响性能。所以在写代码的时候，要尽量避免过多的Reflow。
{% endalert %}

reflow 的原因：
- 页面初始化的时候； 
- 操作DOM时； 
- 某些元素的尺寸变了； 
- 如果 CSS 的属性发生变化了。

减少 reflow / repaint
- 不要一条一条地修改 DOM 的样式。与其这样，还不如预先定义好 css 的 class，然后修改 DOM 的 className。 
- 不要把 DOM 结点的属性值放在一个循环里当成循环里的变量。 
- 为动画的 HTML 元件使用 fixed 或 absoult 的 position，那么修改他们的 CSS 是不会 reflow 的。 
- 千万不要使用 table 布局。因为可能很小的一个小改动会造成整个 table 的重新布局。

#### 八、硬件加速
既然整个渲染过程如此耗时，那么大多数人喜欢使用 translateZ(0) 与 will-change 开启硬件加速的行为就很容易理解了。

转了一圈，又回到了本文的重点：合成层。提升合成层的最好方式是使用 CSS 的 will-change 属性。而 will-change 设置为 opacity、transform、top、left、bottom、right 可以将元素提升为合成层。

先来看看 will-change 的浏览器支持情况，[点击查看](http://caniuse.com/#search=will-change)
![will-change](http://7xvi3w.com1.z0.glb.clouddn.com/composite-will-change.png)
总体支持情况还不错，所以我们可以像下面这样使用：
```css
#jartto {
  will-change: transform;
}
```
当然，对于个别不支持的浏览器，我们使用 translateZ(0) 来解决，[点击查看](http://caniuse.com/#search=translate)
![translate](http://7xvi3w.com1.z0.glb.clouddn.com/composite-translate.png)
```css
#jartto {
  transform: translateZ(0);
}
```

{% alert info %}
We already know that animation of transform and opacity via CSS transitions or animations automatically creates a compositing layer and works on the GPU.
{% endalert %}

那么问题来了，硬件加速依赖 GPU ，而 GPU 为什么会比 CPU 快，我们接着来看。

#### 九、CPU（中央处理器）和 GPU（图形处理器）
文中反复提到了 CPU 和 GPU ，相信很多童鞋可能会产生这样的疑惑：为什么要开启硬件加速，以及 GPU 优势到底在哪里？

我们先简单的了解一下 GPU 的工作原理，GPU 处理数据的过程大概是这样的：
- 将每个复合层绘制成一个单独的图像；
- 准备层数据（尺寸、偏移量、透明度等）；
- 准备动画着色器（如果适用）；
- 将数据发送到GPU；

从 [pu-accelerated-compositing-in-chrome](http://www.chromium.org/developers/design-documents/gpu-accelerated-compositing-in-chrome) 这篇文章可以看出，硬件合成的好处有三种：
1. GPU 上合成图层可以在涉及大量像素的绘图和合成操作中实现比 CPU（无论是在速度和功耗方面）还要好的效率。硬件专为这些类型的工作负载而设计。
2. GPU 上的内容不需要昂贵的回读（例如加速视频 Canvas2D 或 WebGL ）。
3. CPU 和 GPU 之间的并行性，可以同时运行以创建高效的图形管道。

{% alert info %}
组成缓存元素的图像会更快，而这正是 GPU 的强势之处：它能够很快地用亚像素精度合成图像，这给动画增加了显著的平滑度。
{% endalert %}

我们可以这么理解，GPU 是一个单独的计算机：每一个现代设备的一个重要部分实际上是一个独立的单元，有自己的处理器和自己的内存和数据处理模型。与其他应用程序或游戏一样，浏览器必须像外部设备那样与 GPU 对话。

更多 GPU 的介绍，请看这里[传送门](http://www.chromium.org/developers/design-documents/gpu-accelerated-compositing-in-chrome)，这里就不扯远了。

#### 十、补充：CPU 与 GPU 各自的职责
这里我再补充一点：
{% alert success %}
我们可以说 CPU 所做的工作都在软件层面，而 GPU 在硬件层面，我们可以用软件（使用 CPU ）做任何事情，但是对于图像处理，通常用硬件会更快，因为GPU使用图像对高度并行浮点运算做了优化。
{% endalert %}

这也是我之前一直困扰的地方，我们一味的强调硬件加速，而忽略了 CPU 本身的作用。大致过程可能如下：
1. CPU 计算好显示内容提交到 GPU
2. GPU 渲染完成后将渲染结果放入帧缓冲区
3. 视频控制器会按照 VSync 信号逐行读取帧缓冲区的数据，经过可能的数模转换传递给显示器显示

我们看到了 GPU 确实很强势，但是我们最好不要把所有东西一股脑儿抛给 GPU ，问题在于 GPU 并没有无限制处理性能，而且一旦资源用完的话，性能就会开始下降了（即使 CPU 并没有完全占用）。事实上他们有自己的职责，各司其职，各尽其才，才能发挥出更大的作用。

#### 十一、工具使用
我们不打无准备的仗，合理的利用工具，才能大大提高编程效率。这里自荐一篇文章[优化高德地图动画](http://jartto.wang/2017/08/28/how-to-optimize-marker-of-AMap/)，内容包括：
- 页面性能检测
- Chrome 动画调试器（Animations）
- 页面渲染情况（Rendering）
- 图层（Layers）
- GPU 与 复合层

原谅我标题党了，起初是为了找寻优化高德地图动画的方案，结果写成了实际的例子，一步步介绍了 Chrome DevTools 。文章已经发表，所以就没在去更改题目，如果可以的话，我希望的题目是：动画优化之如何使用 Chrome DevTools

#### 十二、优化建议
提升为合成层简单说来有以下几点好处：

- 合成层的位图，会交由 GPU 合成，比 CPU 处理要快
- 当需要 repaint 时，只需要 repaint 本身，不会影响到其他的层
- 元素提升为合成层后，transform 和 opacity 才不会触发 paint，如果不是合成层，则其依然会触发 paint。

如果你已经把一个元素放到一个新的合成层里，那么可以使用 Timeline 来确认这么做是否真的改进了渲染性能。别盲目提升合成层，一定要分析其实际性能表现。

实际上，在内存资源有限的设备上，合成层带来的性能改善，可能远远赶不上过多合成层开销给页面性能带来的负面影响。同时，由于每个渲染层的纹理都需要上传到 GPU 处理，因此我们还需要考虑 CPU 和 GPU 之间的带宽问题、以及有多大内存供 GPU 处理这些纹理的问题。

所以，你就会明白，这里我们使用方式二而不使用方式一的原因了：
```css
/*jartto：方式一*/
@keyframes move {
 from { left: 30px; }
 to { left: 100px; }
}
```

```css
/*jartto：方式二*/
@keyframes move {
 from { transform: translateX(0); }
 to { transform: translateX(70px); }
}
```

#### 十三、总结
优化其实是一个过程，我们需要一个点一个点的处理、突破。没有什么是一蹴而就的，更没有所谓的银弹。就像高中物理书中所说的误差：“误差是不可避免的，只能减少”。

优化也一样，我们只能尽力去做，而不能强求。不断尝试，方是永恒。

参考：
[无线性能优化：Composite](http://taobaofed.org/blog/2016/04/25/performance-composite/)
[CSS GPU Animation](https://www.smashingmagazine.com/2016/12/gpu-animation-doing-it-right/)
[web优化之composite](https://github.com/hello2dj/blog/blob/master/%08web优化之composite.md)
[浏览器渲染](http://blog.csdn.net/xiaozhuxmen/article/details/52014901)
[gpu-accelerated-compositing-in-chrome](http://www.chromium.org/developers/design-documents/gpu-accelerated-compositing-in-chrome)
[视图渲染、CPU和GPU卡顿原因及其优化方案](http://blog.csdn.net/smnisbear/article/details/51170932)




