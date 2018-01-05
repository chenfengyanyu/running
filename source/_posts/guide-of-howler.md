---
title: Howlerjs 音频播放插件
date: 2017-05-03 19:24:03
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/hower0.png
thumbnailImagePosition: left
tags: 
- howler
- audio
comments: true
metaAlignment: center
categories: 技术博文
---
日常的开发中，我们可能会碰到对音频操作的业务逻辑。原生的 Audio 并不是很好用，所以我们需要一个第三方的支持，这一节，我们来说说音频插件 Howler。
<!-- more -->
#### 一、关于 [Howlerjs](https://howlerjs.com)
{% alert info %}
Audio library for the modern web.
howler.js makes working with audio in JavaScript easy and reliable across all platforms.
{% endalert %}

#### 二、能做什么
![features](http://7xvi3w.com1.z0.glb.clouddn.com/hower2.png)

#### 三、安装
1.`npm` 安装
```bash
npm install howler
```

2.`yarn` 安装
```bash
yarn add howler
```

3.`bower` 安装
```bash
bower install howler
```

#### 四、简单使用
1.直接在浏览器中使用
首先引入类库文件：
```html
<script src="/path/to/howler.js"></script>
```
实例化 `Howl`：
```js
var sound = new Howl({
  src: ['sound.webm', 'sound.mp3']
});

sound.play();
```

2.初始化更多数据
```js
var sound = new Howl({
  src: ['sound.webm', 'sound.mp3', 'sound.wav'],
  autoplay: true,
  loop: true,
  volume: 0.5,
  onend: function() {
    console.log('Finished!');
  }
});
```

3.事件监听
```js
var sound = new Howl({
  src: ['sound.webm', 'sound.mp3']
});

// Clear listener after first call.
sound.once('load', function(){
  sound.play();
});

// Fires when the sound finishes playing.
sound.on('end', function(){
  console.log('Finished!');
});
```

4.多个声音如何控制
```js
var sound = new Howl({
  src: ['sound.webm', 'sound.mp3']
});

// Play returns a unique Sound ID that can be passed
// into any method on Howl to control that specific sound.
var id1 = sound.play();
var id2 = sound.play();

// Fade out the first sound and speed up the second.
sound.fade(1, 0, 1000, id1);
sound.rate(1.5, id2);
```

#### 五、更多参数
{% alert info %}
属性支持
{% endalert %}
- `src Array [] required`
资源路径，支持数组
- `volume Number 1.0`
设置声音大小，从 `0.0` 到 `1.0`
- `html5 Boolean false`
开启 `HTML5 Audio`
- `loop Boolean false`
是否循环播放
- `preload Boolean true`
定义 Howl 之后，就会开始自动化下载 audio 文件
- `autoplay Boolean false`
音频下载完毕，是否自动播放
- `mute Boolean false`
柔和过渡声音
- `sprite Object {}`
音频剪辑
```
{
  key: [offset, duration, (loop)]
}
```
- `rate Number 1.0`
声音播放速度
- `pool Number 5`
缓存池大小，存放激活的音频，从而提升性能
- `format Array []`
从扩展名自动检测文件格式
- `xhrWithCredentials Boolean false`
在 `XHR` 请求的时候，是否启用 `withCredentials`

{% alert info %}
事件支持
{% endalert %}
- `onload Function`
当音频加载完毕执行
- `onloaderror Function`
当音频加载异常的时候执行
- `onplayerror Function`
播放音频异常时触发
- `onplay Function`
播放音频时触发
- `onend Function`
音频播放完毕时触发
- `onpause Function`
音频暂停时触发
- `onstop Function`
音频停止播放时触发
- `onmute Function`
音频是否柔和过渡
- `onvolume Function`
更改了音频声音时触发
- `onrate Function`
更改音频速度时触发
- `onseek Function`
找到音频时触发
- `onfade Function`
音频播放完毕渐出时触发

{% alert info %}
可用方法
{% endalert %}
- `play([sprite/id])`
- `pause([id])`
- `stop([id])`
- `mute([muted], [id])`
- `volume([volume], [id])`
- `fade(from, to, duration, [id])`
- `rate([rate], [id])`
- `seek([seek], [id])`
- `loop([loop], [id])`
- `state()`
- `playing(id)`
- `on(event, function, [id])`
- `duration([id])`
- `once(event, function, [id])`
- `off(event, [function], [id])`
- `load()`
- `unload()`

详细 `API` 请移步：[文档地址](https://github.com/goldfire/howler.js#documentation) 




