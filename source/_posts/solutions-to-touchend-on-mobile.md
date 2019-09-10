---
title: 移动端touchend事件不触发解决方案
date: 2015-06-25 21:00:07
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_u38.png
thumbnailImagePosition: left
tags: 
- touch
- 移动端
- 兼容
comments: true
metaAlignment: center
categories: 技术博文
---
移动端touchend事件不触发，需要在touchstart中加入：
```javascript
e.preventDefault();
```
<!--more-->
但是问题来了，阻止默认事件后，页面原先的滚动条也无法滚动了。

查阅了资料，在stackoverflow上得到如下解释：

> To workaround this bug you have to call preventDefault() on either the touchstart or first touchmove event. Of course, this prevents the native scrolling, ***so you will need to re-implement that yourself.***

看到最后一句真是内牛满面，我勒个去~

但是我还是不死心，丧心病狂的我找到了touchcancel事件，我们先来看看touch中这几个事件具体含义：

- ouchstart事件：当手指触摸屏幕时候触发，即使已经有一个手指放在屏幕上也会触发。

- touchmove事件：当手指在屏幕上滑动的时候连续地触发。在这个事件发生期间，调用preventDefault()事件可以阻止滚动。

- touchend事件：当手指从屏幕上离开的时候触发。

- touchcancel事件：当系统停止跟踪触摸的时候触发。关于这个事件的确切出发时间，文档中并没有具体说明，咱们只能去猜测了。

<span style="color:red">补充：touchend事件qq浏览器不执行，可能会需要touchcancel事件代替之。</span>

参考资料：
https://github.com/TNT-RoX/android-swipe-shim
http://www.2cto.com/kf/201402/276737.html