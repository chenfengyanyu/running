---
title: 如何检测页面滚动并执行动画
date: 2016-08-18 11:17:06
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/ani_animate.png
thumbnailImagePosition: left
tags: 
- 滚动监听
- 动画
- scroll
- animation
comments: true
metaAlignment: center
categories: 技术博文
---
H5页面动画，在推广运营中应用的相当广泛。越是酷炫的动画，越能吸引用户。动画当然算是比较重要的一个环节，除此之外不得不提到的就是－动画滚动交互效果。
<!-- more -->
#### 一、每个section怎样单独执行动画？
首先，引入动画库，这里我们使用animate.css（[传送门](https://daneden.github.io/animate.css/?)）
```html
<link rel="stylesheet" href="css/animate.css">
```
{% alert info %}
请注意：如果你只用了少量动画效果，请直接拷贝具体动画代码，而不再引入整个动画库。
{% endalert %}
其次，像往常一样编写你的html代码：
```html
<section  id="wan_banner">
  <div class="warp">
    <div id="left" class="animate">
      <div class="pos bounce_in_down">
        <h3>α Space 物联网基站</h3>
        <i>革命性通信技术</i>
        <i>超低功耗传感器网络基站</i>
      </div>
    </div>
    <div id="right" class="animate">
    	<img class="bounce_in_right" src="../image/product2.png" 
    	width="200" height="406"/>
    </div>
  </div>
</section>
```
我们来简单说一下这里的处理细节：
1.我们为#left，#right容器添加了animate类；
2.为animate子容器分别添加了bounce_in_down，bounce_in_right类；

接下来，我们来处理一下动画细节：
```css
/*方式一*/
.animate{
    animation-play-state: paused;
    -webkit-animation-play-state:paused;
}
.play .animate {
    animation-play-state: running;
    -webkit-animation-play-state:running;
}
/*方式二*/
.animate{
    opacity: 0;
}
.play .animate {
    opacity: 1;
}
/*方式三*/
.animate{
    display: none;
}
.play .animate {
    display: block;
}
/*动画类，此处举一个栗子，请确保bounceInDown动画已经引入*/
.play .bounce_in_down{
    -webkit-animation:bounceInDown 2s ease-in-out;
}
```
上面列举了三种方式，方式一主要是先暂停动画，等待运行时机。方式二，三先隐藏元素，激活后，显示元素。
{% alert success %}
个人比较推荐第一种和第二种方式，第三种方式元素容器会变化，某种情况下会引起不必要的问题。
{% endalert %}
这里着重说明一下play类，我们暂且称它为动画触发类。思路就是，需要运行动画的元素添加animate类，先做暂停处理；当需要激活该元素动画的时候，为其父元素添加play类，从而触发动画运行。

很好，动画也可以触发了，那么最重要的运行时机怎么确定？下面我来介绍一下，滚动触发动画运行的方式。

#### 二、如何检测页面滚动到了哪个section容器？
场景：页面分为多个section，我要实现，页面滚动到具体哪个section，为其添加play类。

方案一：
```js
$(window).scroll(function (event) {
  var scroll = $(window).scrollTop();
  // Do something
  console.log(scroll);
});
```
检测滚动的高度，然后取页面每个section位置，放入数组，如果当前滚动高度在section某个区间，那么就为其添加play类；

可是感觉比较麻烦，看看有没有其他好的办法。继续寻找方案二。。。。

方案二：
借助插件，实现监听滚动[传送门](https://edull24.github.io/ScrollWatch/)
还是上面的html结构：
```html
<section data-scroll-watch  id="wan_banner">
  <div class="warp">
    <div id="left" class="animate">
      <div class="pos bounce_in_down">
        <h3>α Space 物联网基站</h3>
        <i>革命性通信技术</i>
        <i>超低功耗传感器网络基站</i>
      </div>
    </div>
    <div id="right" class="animate">
      <img class="bounce_in_right" src="../image/product2.png" 
      width="200" height="406"/>
    </div>
  </div>
</section>
```
{% alert info %}
section部分添加了data-scroll-watch，这就告诉了ScrollWatch，此部分需要做滚动监听。
{% endalert %}
接下来，你需要初始化ScrollWatch插件，并做一些简单的配置调整。
```js
var sw = new ScrollWatch({
	watchOnce: false,
	infiniteScroll: false,
	infiniteOffset: 50,
	inViewClass: 'play',
	onElementInView: function(data) {
	  // console.log(data.el, '...is now in view');
	}
});
```
哈哈，大家可能看出来了，方案二就是偷懒模式🙈。我们借助scrollwatch来完成，详细信息请查看api。

#### 三、继续升级：section进入视口，执行动画；section滚出视口，反向执行动画？
交互师又提出了一个需求：
当滚动到该区域时，动画执行；当该区域离开视图区域时，动画反向执行。我们接受挑战，看看如何实现。

第一反应当然是看看有没有现成的插件，这样可以省去重复造轮子的时间成本。很幸运，我看到了这篇文章[jQuery Smoove-华丽的CSS3滚动效果](http://www.jq22.com/jquery-info499)。

嗯，jquery smoove是个不错的方案。
{% alert warning %}
注意：jquery smoove为另外一种实现方式，与之前说的上文没有任何关系！
{% endalert %}
首先：引入类库，请先引入jquery文件。
```js
<script src="//cdnjs.cloudflare.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
<script src="//cdnjs.cloudflare.com/ajax/libs/jquery-smoove/
0.2.6/jquery.smoove.min.js"></script>
```
我们对上面的html结构做一下简单的调整：
```html
<section   id="wan_banner">
  <div class="warp">
    <div id="left" class="my_animate" data-move-x="-100px">
      <div class="pos">
        <h3>α Space 物联网基站</h3>
        <i>革命性通信技术</i>
        <i>超低功耗传感器网络基站</i>
      </div>
    </div>
    <div id="right" class="my_animate" data-move-x="100px">
      <img src="../image/product2.png" width="200" height="406"/>
    </div>
  </div>
</section>
```
jquery smoove帮我们做好了动画，我们只需要为其添加特定属性。

请注意：#left和#right的容器内部已经变成了my_animate类，同时添加了data-move-x="-100px"和data-move-x="100px"语法。

用插件，肯定需要做一些初始化工作：
```js
$(document).ready(function(){ 
	$('.my_animate').smoove({
		offset  : '45%'
	});
})
```
当该元素滚动到距离视口底部45%距离时，触发动画。更多的动画效果，请查看[jquery-smoove文档地址](https://github.com/abeMedia/jquery-smoove)。

到这里，整个处理方式应该很明晰了吧，希望能够帮到你。



