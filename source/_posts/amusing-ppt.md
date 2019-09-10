---
title: 酷炫的 HTML5 网页 PPT
date: 2019-04-15 20:07:39
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/ppt/logo.png
thumbnailImagePosition: left
tags: 
- ppt
- impress
- html5
comments: false
metaAlignment: center
categories: 技术博文
---
一般演讲、培训等场合，我们都离不开 PPT，那么如何做出一款与众不同而有充满科技感的 PPT 呢？这时候，我们可以尝试一下在线 PPT。
<!-- more -->

#### 一、选择在线 PPT 的几大原因
1.省去了上传下载的麻烦；
2.降低了客户端对 PPT 软件的依赖；
3.随时更新，更灵活；
4.同屏播放，全场互动，增加用户体验；
...

#### 二、多种选择
1.`revealjs`
一个可以帮我们轻易使用 `HTML` 创建漂亮演示文稿的框架。
![revealjs](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/ppt/ppt1.png)

2.`impress`
效果非常酷炫，下文将深入介绍。
![impress](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/ppt/ppt2.png)

3.`fathom.js`
使用 `HTML`，`CSS`，`JS` 来实现滑动页面，体验非常接近 `PPT`。
![fathom](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/ppt/ppt3.png)

4.focusky
操作界面简洁，易上手。支持 3D 幻灯片演示特效打破常规。
![focusky](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/ppt/ppt5.png)


#### 三、强力推荐
上面介绍了很多可用的在线 `PPT` 类库，在我看来最酷炫的莫过于 `impress.js`，我们先来看一个演示：
![impress-demo](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/ppt/demo.gif)

{% alert info %}
注意：在 Impress 中大量使用 3D 动画，可能会使页面变得卡顿。
{% endalert %}


#### 四、基本使用
1.定义结构
```html
<div id="impress"></div>
```

2.每页 `PPT` 使用 `step` 类来区分
```html
<div id="bored" class="step" data-x="-1000">
    <q>Aren’t you just <b>bored</b> with all those slides-based presentations?</q>
</div>
```

3.使用 `data-x`，`data-y` 完成平移操作
```html
<div id="bored" class="step" data-x="-1000" data-y="-1500">
    <q>Aren’t you just <b>bored</b> with all those slides-based presentations?</q>
</div>
```

4.缩放：`data-scale`
```html
<div id="title" class="step" data-x="0" data-y="0" data-scale="4">
    <span class="try">then you should try</span>
    <h1>impress.js<sup>*</sup></h1>
    <span class="footnote"><sup>*</sup> no rhyme intended</span>
</div>
```

5.页面旋转：`data-rotate`
```html
<div id="its" class="step" data-x="850" data-y="3000" data-rotate="90" data-scale="5">
    <p>
      It’s a <strong>presentation tool</strong> 
      inspired by the idea behind <a href="http://prezi.com">prezi.com</a> 
      and based on the <strong>power of CSS3 transforms and transitions</strong> in modern browsers.
    </p>
</div>
```

6.调整 `z` 方向层级 `data-z`，可以理解为离用户的远近距离
```html
<div id="tiny" class="step" data-x="2825" data-y="2325" data-z="-3000" data-rotate="300" data-scale="1">
    <p>and <b>tiny</b> ideas</p>
</div>
```

7.使用 `data-rotate-x`, `data-rotate-y`, `data-rotate-z` 实现 `3D` 旋转
```
<div id="its-in-3d" class="step" data-x="6200" data-y="4300" data-z="-100" data-rotate-x="-40" data-rotate-y="10" data-scale="2">
    <p>
      <span class="have">have</span>
      <span class="you">you</span>
      <span class="noticed">noticed</span>
      <span class="its">it’s</span>
      <span class="in">in</span>
      <b>3D<sup>*</sup></b>?
    </p>
    <span class="footnote">* beat that, prezi ;)</span>
</div>
```
此外，还有一些可用的 `css` 类，`js` 初始化方法，以及 `stepenter` 事件等，具体可以参考[官方文档](https://github.com/impress/impress.js/blob/master/DOCUMENTATION.md)。


#### 五、投屏互动
演讲者端，监听 `impress:stepgoto` 事件：
```js
let impressiv = function(presi,session) {
    let socket = io.connect('http://jartto.wang');
        socket.on('connect', function(data) {
        socket.emit('join', {presi: presi, session:session});
    });
    socket.on('disconnect', function(data) {
        alert('演示结束，欢迎观看！');
    });
    document.addEventListener('impress:stepgoto', function(event) {
        socket.emit('slide', {slide:event.target.id});
    });
}
```
观众端，接收 `slide` 消息，然后自动翻页：
```js
let impressiv = function(presi) {
  let socket = io.connect('http://jartto.wang');
  socket.on('connect', function(data) {
    socket.emit('join', {presi: presi});
  });
  socket.on('slide', function(data) {
    // 控制用户端翻页
    impress().goto(data.slide);
  });
  socket.on('disconnect', function(data) {
    alert('演示结束，欢迎观看！');
    // io.disconnect();
  });
}
```
需要特别注意，我们要禁止掉观众端的操作事件，否则你演讲过程就会被别人牵着鼻子走了🙈～
```js
$(document).unbind('touchstart');
$(document).unbind('click');
$(document).unbind('keydown');
$(document).unbind('keyup');
```


#### 六、可视化编辑
如果你不喜欢写代码，我们可以选择[可视化编辑器](http://qti3e.github.io/Imprezi/#/step-2)。
![visi](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/ppt/ppt4.png)

#### 七、更多资源
- [10 Best JavaScript And HTML5 Presentation Frameworks](https://devzum.com/2014/11/10-best-javascript-and-html5-presentation-frameworks/)
- [impress examples-and-demos](https://github.com/impress/impress.js/wiki/Examples-and-demos)
- [可视化编辑器](http://qti3e.github.io/Imprezi/#/step-2)
- [impress 导出 pdf](https://github.com/melix/deck2pdf)
- [impress api 文档](https://github.com/impress/impress.js/blob/master/DOCUMENTATION.md)


