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


#### 三、强力推荐
上面介绍了很多可用的在线 `PPT` 类库，在我看来最酷炫的莫过于 `impress.js`，我们先来看一个演示：
![impress-demo](#)


#### 四、简单使用
```html
<div id="impress" class="impress-not-supported">
    <div id="bored" class="step slide" data-x="-1000" data-y="-1500">
        <q>Aren't you just <b>bored</b> with all those slides-based presentations?</q>
    </div>
    <div class="step slide" data-x="0" data-y="-1500">
        <q>Don't you think that presentations given <strong>in modern browsers</strong> shouldn't <strong>copy the limits</strong> of 'classic' slide decks?</q>
</div>
```

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


#### 七、更多资源
