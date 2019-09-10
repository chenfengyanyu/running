---
title: 有趣的动态天气图标插件
date: 2017-04-10 23:22:48
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/weather0.gif
thumbnailImagePosition: left
tags: 
- plugin
comments: false
metaAlignment: center
categories: 技术博文 
---
偶然发现一个国外的网站接入了天气板块，很有意思的是该网站所有的天气图标都是会动的。于是我便有了学（chao）习（xi）的念头，我们来一探究竟。
<!-- more -->
#### 一、顺藤摸瓜
通过审查元素，我们很快定位到了一个叫 `skycons` 的 `js` 类库，顺藤摸瓜找到了 [github 地址](https://github.com/darkskyapp/skycons)。美中不足的是，`skycons` 文档地址不能访问了，于是顺手下载了一份 skycons.js 文件。

{% alert success %}
OK，万事具备，下面我们开始分步剖析。
{% endalert %}

#### 二、简单示例
首先，添加 `canvas`，并设置 `id`，以备后续使用，宽高可以随意设置，如下:
```html
<canvas id="jartto" width="128" height="128"></canvas>
```

其次，我们实例化 `Skycons`：
```js
let skycons = new Skycons({'color': 'pink'});
```

然后，在我们的元素上通过 `canvas id` 来添加动画，并选择[动画类型](#animateType)：
```js
skycons.add('jartto', Skycons.PARTLY_CLOUDY_DAY);
```

最后，别忘了：
```js
skycons.play();
```

{% alert info %}
通过调用 skycons.play() 来播放动画。
{% endalert %}

这样，一个简单的示例就完成了，是不是很简单呢？下面我们来看看参数详解。

#### 三、<a name="animateType">参数详解</a>
可设置的天气类型参数：
- CLEAR_DAY
- PARTLY_CLOUDY_DAY
- CLOUDY
- RAIN
- SLEET
- SNOW
- WIND
- FOG

添加动画：
```js
// 通过 canvas
skycons.add('jartto1', Skycons.PARTLY_CLOUDY_DAY);
// 通过 元素 id
skycons.add(document.getElementById('jartto2'), Skycons.PARTLY_CLOUDY_DAY);
```
播放动画：
```js
skycons.play();
```
暂停动画：
```js
skycons.pause()
```
动态更改 icon ：
```js
skycons.set('jartto1', Skycons.PARTLY_CLOUDY_NIGHT);
```
移除动画：
```js
skycons.remove('jartto2');
```

#### 四、实际应用
为了更好的说明问题，我们来做一个天气板块：
首先，引入 `skycons` 文件：
```html
<script src="js/skycons.js" type="text/javascript"></script>
```
其次，创建html结构：
```html
<ul class="box">
  <li>
    <span>晴：CLEAR_DAY</span>
    <canvas id="jartto1" width="100" height="100"></canvas>
  </li>
  <li>
    <span>夜间晴：CLEAR_NIGHT</span>
    <canvas id="jartto2" width="100" height="100"></canvas>
  </li>
  .....
</ul>
```
最后，添加 js 代码：
```js
let lists = ['CLEAR_DAY','CLEAR_NIGHT','PARTLY_CLOUDY_DAY','PARTLY_CLOUDY_NIGHT','CLOUDY','RAIN','SLEET','SNOW','WIND','FOG'];

lists.map((item,index) => {
  // 随机一个色值
  let color = Math.floor(Math.random() * (2 << 23)).toString(16);
  // 实例化 Skycons
  let skycons = new Skycons({ color: `#${color}`});
  skycons.add(`jartto${index+1}`, Skycons[item]);
  skycons.play();
})
```

是不是很简单呢，更多请查看：[代码地址](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/skycons)。

#### 五、问题汇总
1.实际的天气状况可能有很多，而 skycons 提供的选择并不多；
![weather2](http://7xvi3w.com1.z0.glb.clouddn.com/weather2.png-blog)
{% alert info %}
解决方案：合并同类的天气状况，可能的代码如下:
{% endalert %}
```js
switch (code) {
  case '100':
    val = 'CLEAR_DAY';
    break;
  case '101':
    val = 'CLOUDY';
    break;
  case '102':
  case '103':
  case '104':
    val = 'PARTLY_CLOUDY_DAY';
    break;

  ...

  default:
    val = 'CLEAR_DAY';
```

#### 六、源码分析
因为看不到文档，所以很好奇，大概瞅了一些源码。当然，也并不是一无所获，譬如：
1.`requestAnimationFram` 的兼容处理：
```js
 var raf = global.requestAnimationFrame       ||
           global.webkitRequestAnimationFrame ||
           global.mozRequestAnimationFrame    ||
           global.oRequestAnimationFrame      ||
           global.msRequestAnimationFrame     ,
     caf = global.cancelAnimationFrame        ||
           global.webkitCancelAnimationFrame  ||
           global.mozCancelAnimationFrame     ||
           global.oCancelAnimationFrame       ||
           global.msCancelAnimationFrame      ;
```

2.`add` 方法：
```
add: function(el, draw) {
  var obj;

  if(typeof el === "string")
    el = document.getElementById(el);

  // canvas 不存在则不执行
  if(el === null)
    return;

  // 对用户传如参数进行格式化处理，做了一些简单的容错，因为要直接调用方法
  draw = this._determineDrawingFunction(draw);

  // 当然，如果方法不存在，也得跳出
  if(typeof draw !== "function")
    return;

  obj = {
    element: el,
    context: el.getContext("2d"),
    drawing: draw
  };
  // 将每次添加的对象保存在数组中，方便修改和删除操作
  this.list.push(obj);
  this.draw(obj, KEYFRAME);
},
```
{% alert info %}
`add` 方法操作的是一个数组，也就意味着，我们只需要实例化一次 `skycons` 就可以添加多个 `icons` 。
{% endalert %}

3.定时器的时间设置技巧 1000 / 60，动画更加流畅：
```
play: function() {
  var self = this;

  this.pause();
  this.interval = requestInterval(function() {
    var now = Date.now(), i;

    for(i = self.list.length; i--; )
      self.draw(self.list[i], now);
  }, 1000 / 60);
},
```
起初不太理解作者为何要设置这个数值，后来才意识到：
{% alert info %}
大多数电脑显示器的刷新频率是 60 Hz，大概相当于每秒钟重绘 60 次。
{% endalert %}

大多数浏览器都会对重绘操作加以限制，不超过显示器的重绘频率，因为即使超过那个频率用户体验也不会有提升。因此，最平滑动画的最佳循环间隔是 1000 ms / 60，约等于 16.6 ms 。

4.各种 canvas 画太阳，云彩，风等方法，可以参考。
{% alert success %}
源码比较简单，详情请查看：[源码地址](https://github.com/chenfengyanyu/my-web-accumulation/blob/master/skycons/skycons.js)
{% endalert %}

#### 七、相关资源
1.[skycons](https://github.com/darkskyapp/skycons)
2.[天气 API](https://www.heweather.com/documents/condition)
3.[requestAnimationFrame](https://www.cnblogs.com/xiaohuochai/p/5777186.html)
