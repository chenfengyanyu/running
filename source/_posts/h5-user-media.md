---
title: HTML5 之摄像头调用（User Media API）
date: 2017-11-28 20:27:37
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/h5.png
thumbnailImagePosition: left
tags: 
- HTML5
- media
- API
comments: false
metaAlignment: center
categories: 技术博文 
---
提起移动端调用设备摄像头，我们很容易就想到借助 PhoneGap 或 Cordova 。如果只是想在 Web 页面简单的用一下，那不妨试试 HTML5 的 User Media API 。
<!-- more -->
#### 一、先来看看支持情况
![User Media API](http://7xvi3w.com1.z0.glb.clouddn.com/media0.png)

#### 二、说明文档
MediaDevices.getUserMedia()方法提示用户允许使用一个视频和/或一个音频输入设备，例如相机或屏幕共享和/或麦克风。
- 如果用户给予许可，就返回一个Promise 对象，MediaStream 对象作为此 Promise 对象的 Resolved［成功］状态的回调函数参数；
- 相应的，如果用户拒绝了许可，或者没有媒体可用的情况下，PermissionDeniedError 或者 NotFoundError 作为此 Promise 的 Rejected［失败］状态的回调函数参数。

```
navigator.mediaDevices.getUserMedia(constraints)
.then(function(mediaStream) { ... })
.catch(function(error) { ... })
```
{% alert danger %}
注意，由于用户不会被要求必须作出允许或者拒绝的选择，所以返回的 Promise 对象可能既不会触发 resolve 也不会触发 reject。
{% endalert %}

使用文档请移步：
[ MediaDevices.getUserMedia ](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaDevices/getUserMedia)
[MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getUserMedia)

#### 三、使用说明
1.使用 Promise ：
```js
var p = navigator.mediaDevices.getUserMedia({ audio: true, video: true });

p.then(function(mediaStream) {
  var video = document.querySelector('video');
  video.src = window.URL.createObjectURL(mediaStream);
  video.onloadedmetadata = function(e) {
    // Do something with the video here.
  };
});

p.catch(function(err) { console.log(err.name); }); // always check for errors at the end.
```
2.宽度和高度
{% alert info %}
这是个使用mediaDevices.getUserMedia()的例子，包含了polyfill来适配旧版的浏览器。
{% endalert %}
```js
var promisifiedOldGUM = function(constraints) {
  // First get ahold of getUserMedia, if present
  var getUserMedia = (navigator.getUserMedia ||
      navigator.webkitGetUserMedia ||
      navigator.mozGetUserMedia);
  // Some browsers just don't implement it - return a rejected promise with an error
  // to keep a consistent interface
  if(!getUserMedia) {
    return Promise.reject(new Error('getUserMedia is not implemented in this browser'));
  }
  // Otherwise, wrap the call to the old navigator.getUserMedia with a Promise
  return new Promise(function(resolve, reject) {
    getUserMedia.call(navigator, constraints, resolve, reject);
  });
}
// Older browsers might not implement mediaDevices at all, so we set an empty object first
if(navigator.mediaDevices === undefined) {
  navigator.mediaDevices = {};
}
// Some browsers partially implement mediaDevices. We can't just assign an object
// with getUserMedia as it would overwrite existing properties.
// Here, we will just add the getUserMedia property if it's missing.
if(navigator.mediaDevices.getUserMedia === undefined) {
  navigator.mediaDevices.getUserMedia = promisifiedOldGUM;
}
// Prefer camera resolution nearest to 1280x720.
var constraints = { audio: true, video: { width: 1280, height: 720 } };
navigator.mediaDevices.getUserMedia(constraints)
.then(function(stream) {
  var video = document.querySelector('video');
  video.src = window.URL.createObjectURL(stream);
  video.onloadedmetadata = function(e) {
    video.play();
  };
})
.catch(function(err) {
  console.log(err.name + ": " + err.message);
});
```
3.帧率
{% alert info %}
在某些情况下，比如WebRTC上使用受限带宽传输时，低帧率可能更适宜。
{% endalert %}
```js
var constraints = { video: { frameRate: { ideal: 10, max: 15 } } };
```
4.在移动设备（电话）上，前置或者后置摄像头
```js
var front = false;
document.getElementById('flip-button').onclick = function() { front = !front; };

var constraints = { video: { facingMode: (front? "user" : "environment") } };
```
5.权限
{% alert info %}
在一个可安装的app（如Firefox OS app）中使用 getUserMedia() ，你需要在声明文件中指定以下的权限：
{% endalert %}
```json
"permissions": {
  "audio-capture": {
    "description": "Required to capture audio using getUserMedia()"
  },
  "video-capture": {
    "description": "Required to capture video using getUserMedia()"
  }
}
```

#### 四、使用插件
[插件](https://github.com/tagawa/GumWrapper)
[源码地址](https://github.com/tagawa/GumWrapper/blob/master/gumwrapper.js)

