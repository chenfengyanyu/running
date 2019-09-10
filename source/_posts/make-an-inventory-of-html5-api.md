---
title: HTML5 API大盘点
date: 2016-07-25 14:26:36
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/html5_F03E118B-C824-467C-A1D7-D1E0D606B02B.png
thumbnailImagePosition: left
tags: 
- html5
comments: true
metaAlignment: center
categories: 技术博文
---
在地铁里偶然听见有些人在谈论着html5，我听到了这样的声音：“不就是一些简单的网页吗，炒作的太厉害了”。真的只是一些简单的页面吗？
<!-- more -->
我决定顺着《HTML5高级教程》捋一捋，咱们来说道说道。嗯，好戏来了，不只是打脸哦～🙈

都说了API大盘点，所以“简写，语义化，表单支持”均不在本文的盘点范围内。
#### 一、Canvas
Canvas本质上是一个位图画步。
{% alert success %}
使用canvas编程，首先要获取其上下文（context）。接着在上下文中执行动作，最后将这些动作应用到上下文中。可以将canvas的这种编辑方式想象成数据库事务：开发人员先发起一个事务，然后执行某些操作，最后提交事务。
{% endalert %}
html结构：
```html
<canvas id="diagonal" style="border:1px solid;" width="200" height="200"></canvas>
```
js代码：
```js
function drawDiagonal(){
     //取的canvas元素及其绘图上下文
     var canvas = document.getElementById(‘diagonal’);
     var context = canvas.getContext('2d');

     // 用绝对坐标来创建一条路径
     context.beginPath();
     context.moveTo(70,140);
     context.lineTo(140,70);

     // 将这条线绘制到canvas上
     context.stroke();
}
window.addEventListener('load',drawDiagonal,true);
```
#### 二、Audio和Video
html5中的多媒体支持。
```html
<audio id="clickSound">
    <source src="a.ogg">
    <source src="b.mp3">
</audio>

<button id="toggle" onclick="toggleSound()">play</button>
```
```js
function toggleSound() {
    var music = document.getElementById('clickSound');
    var toggle = document.getElementById('toggle');

    if(music.paused){
        music.play();
        toggle.innerHTML = 'Pause';
    }else{
        music.pause();
        toggle.innerHTML = 'Play';
    }
}
```
#### 三、Geolocation
{% alert success %}
请求一个位置信息，如果用户同意，浏览器就会返回位置信息，该位置信息是通过支持HTML5地理定位功能的底层设备提供给浏览器。位置信息由纬度／经度坐标和一些其他的元数据组成。有了这些位置信息就可以构建引人注意目的位置感知类应用程序。
{% endalert %}
两种类型的定位请求API：单次定位请求和重复性的位置更新请求；
```js
window.addEventListener('load',loadDemo,true);

function loadDemo(){
    if(navigator.geolocation){
        navigator.geolocation.watchPosition(
            updateLocation,
            handleLocationError,
            {maximumAge:20000}
        );
    }
}

function updateLocation(position){
    var latitude = position.coords.latitude;
    var longitude = position.coords.longitude;
    var accuracy = position.coords.accuracy;
    var timestamp = position.timestamp;
}
```
#### 四、WebSockets
{% alert success %}
WebSocket作为HTML5一种新的协议，实现了浏览器与服务器的双向通讯。在 WebSocket API 中，浏览器和服务器只需要要做一个握手的动作，然后，浏览器和服务器之间就形成了一条快速通道。两者之间就直接可以数据互相传送。
{% endalert %}
WebSocket protocol 是HTML5一种新的协议。它实现了浏览器与服务器全双工通信(full-duplex)。

在此WebSocket 协议中，为我们实现即时服务带来了两大好处：
1. Header
互相沟通的Header是很小的-大概只有 2 Bytes
2. Server Push

```js
var  wsServer = 'ws://localhost:8888/Demo';
var  websocket = new WebSocket(wsServer);

websocket.onopen = function (evt) { onOpen(evt) };
websocket.onclose = function (evt) { onClose(evt) };
websocket.onmessage = function (evt) { onMessage(evt) };
websocket.onerror = function (evt) { onError(evt) };

function onOpen(evt) {
  console.log("Connected to WebSocket server.");
}
function onClose(evt) {
  console.log("Disconnected");
}
function onMessage(evt) {
  console.log('Retrieved data from server: ' + evt.data);
}
function onError(evt) {
  console.log('Error occured: ' + evt.data);
}
```
在实现websocket连线过程中，需要通过浏览器发出websocket连线请求，然后服务器发出回应，这个过程通常称为“握手” (handshaking)。

作为这一设计原则的一部分,WebSocket连接的协议规范定义了一个HTTP连接作为其开始生命周期,进而保证其与pre-WebSocket世界的完全向后兼容。通常来说从HTTP协议切换WebSocket称为WebSocket握手。

#### 五、Web storage
①localStorage和sessionStorage大同小异，主要区别在生存周期，下面细说。
```js
//存
localStorage.backtoken='jartto';
localStorage.setItem('backtoken','');
//取
localStorage.getItem('backtoken');
//删
localStorage.removeItem('backtoken');
```

- localStorage生命周期是永久，这意味着除非用户显示在浏览器提供的UI上清除localStorage信息，否则这些信息将永远存在。
- sessionStorage生命周期为当前窗口或标签页，一旦窗口或标签页被永久关闭了，那么所有通过sessionStorage存储的数据也就被清空了。
- localStorage和sessionStorage一样都是用来存储客户端临时信息的对象。
他们均只能存储字符串类型的对象（虽然规范中可以存储其他原生类型的对象，但是目前为止没有浏览器对其进行实现）。
- 不同浏览器无法共享localStorage或sessionStorage中的信息。相同浏览器的不同页面间可以共享相同的localStorage（页面属于相同域名和端口），但是不同页面或标签页间无法共享sessionStorage的信息。

{% alert info %}
这里需要注意的是，页面及标签页仅指顶级窗口，如果一个标签页包含多个iframe标签且他们属于同源页面，那么他们之间是可以共享sessionStorage的。
{% endalert %}
②离线存储
{% alert success %}
HTML5 的 Web storage API 采用了离线缓存，会生成一个清单文件（manifest file)，这个清单文件实质就是一系列的URL列表文件，这些URL分别指向页面当中的html,css,javascript,图片等相关内容。当使用离线应用时，应用会引入这一清单文件，浏览器会读取这一文件，下载相应的文件，并将其缓存到本地。使得这些web应用能够脱离网络使用，而用户在离线时的更改也同样会映射到清单文件中，并在重新连线之后将更改返回应用，工作方式与我们现在所使用的网盘有着异曲同工之处。
{% endalert %}
首先，需要在页面头加入manifest属性：
```html
<!DOCTYPE HTML>
<html manifest = "cache.manifest">
...
</html>
```
然后cache.manifest文件的书写方式为：
```
CACHE MANIFEST
#v0.11

CACHE:

js/app.js
css/style.css

NETWORK:
resourse/logo.png

FALLBACK:
/ /offline.html
```
离线存储的manifest一般由三个部分组成:
1.CACHE:表示需要离线存储的资源列表，由于包含manifest文件的页面将被自动离线存储，所以不需要把页面自身也列出来。
2.NETWORK:表示在它下面列出来的资源只有在在线的情况下才能访问，他们不会被离线存储，所以在离线情况下无法使用这些资源。不过，如果在CACHE和NETWORK中有一个相同的资源，那么这个资源还是会被离线存储，也就是说CACHE的优先级更高。
3.FALLBACK:表示如果访问第一个资源失败，那么就使用第二个资源来替换他，比如上面这个文件表示的就是如果访问根目录下任何一个资源失败了，那么就去访问offline.html。

#### 六、Communication
{% alert success %}
出于安全方面的考虑，运行在同一浏览器中的框架，标签页，窗口间的通信一直都受到了严格的限制。
如果浏览器内部能提供直接的通信机制，就能更好的组织这些应用。
为了满足上述需求，浏览器厂商和标准制定机构一致同意引入一种新功能：跨文档消息通信。
{% endalert %}
跨文档消息通信可以确保iframe，标签页，窗口间安全的进行跨源通信。它把postMessage API定义为发送消息的标准方式。利用postMessage发送消息非常简单，代码如下：
```js
chatFrame.contentWindow.postMessage(‘Hello,world’,’http://jartto.wang');
```
接收消息时仅需在页面中增加一个事件处理函数。当某个消息到达时，通过检查消息的来源来决定是否对这条消息进行处理。
```js
window.addEventListener('message',messageHandler,true);

function messageHandler(e){
    switch(e.origin) {
        case 'friend.example.com':
            // 处理消息
            processMessage(e.data);
            break;
        default:
            // 消息来源无法识别，消息被忽略
    }
}
```

#### 七、Web Workers
{% alert success %}
HTML5 Web Workers可以让Web 应用程序具备后台处理能力，它对多线程的支持非常好，因此，使用了HTML5的Javascript应用程序可以充分利用多核CPU带来的优势。将耗时长的任务分配给HTML5 Web Workers执行，可以避免弹出脚本运行缓慢的警告。

Web Workers不能直接访问Web 页面和DOM API。

Web Workers的另一个用途是监听由后台服务器广播的新闻消息，收到后台服务的消息后，将其显示在Web页面上。
{% endalert %}

#### 八、requestAnimationFrame
{% alert success %}
浏览器可以优化并行的动画动作，更合理的重新排列动作序列，并把能够合并的动作放在一个渲染周期内完成，从而呈现出更流畅的动画效果。比如，通过requestAnimationFrame()，JS动画能够和CSS动画/变换或SVG SMIL动画同步发生。另外，如果在一个浏览器标签页里运行一个动画，当这个标签页不可见时，浏览器会暂停它，这会减少CPU，内存的压力，节省电池电量。
{% endalert %}
```js
// shim layer with setTimeout fallback
window.requestAnimFrame = (function(){
  return  window.requestAnimationFrame       ||
          window.webkitRequestAnimationFrame ||
          window.mozRequestAnimationFrame    ||
          function( callback ){
            window.setTimeout(callback, 1000 / 60);
          };
})();


// usage:
// instead of setInterval(render, 16) ....

(function animloop(){
  requestAnimFrame(animloop);
  render();
})();
// place the rAF *before* the render() to assure as close to
// 60fps with the setTimeout fallback.
```



参考文章：
https://developer.mozilla.org/en-US/docs/Web/HTML/Using_the_application_cache
https://segmentfault.com/a/1190000000732617
http://www.webhek.com/requestanimationframe/




