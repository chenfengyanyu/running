---
title: 重写 Audio 样式
date: 2018-04-25 08:26:16
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/audio.png
thumbnailImagePosition: left
tags: 
- html5
- audio
- css
- style
comments: false
metaAlignment: center
categories: 技术博文
---
众所周知，HTML5 已经可以很好的支持 Audio 和 Vedio 了，我们可以方便的在网页添加多媒体控件。然而，设计师总是与众不同，修改 Audio 默认样式已成常态。
<!-- more -->
本文将围绕如何通过 `CSS` 来修改 `HTML5` 的默认样式，撇开 `JS` 实现方式。当然，后续还会推出一篇文章：[打造与众不同的 Audio 播放器](#)，先占个坑吧！

#### 一、Audio 结构
Audio 的用法很简单，插入下面的代码，一个原生音频播放器就完成了：
```html
<audio controls="controls" preload="auto">
  <source src="jartto.ogg" type="audio/ogg" />
  <source src="jartto.mp3" type="audio/mpeg" />
Your browser does not support the audio element.
</audio>
```

#### 二、属性一览
- autoplay：如果出现该属性，则音频在就绪后马上播放。
- controls：如果出现该属性，则向用户显示控件，比如播放按钮。
- loop：如果出现该属性，则每当音频结束时重新开始播放。
- preload：如果出现该属性，则音频在页面加载时进行加载，并预备播放。如果使用 "autoplay"，则忽略该属性。
- src：要播放的音频的 URL。

更多详情，请参考文档：[HTML5 Audio 标签](http://www.w3school.com.cn/html5/tag_audio.asp)

#### 三、问题窘境
当我们打开开发者工具审查 `Audio` 元素的时候，除了 `Audio` 标签，什么也看不到。这时候，肯定有人想这么改：
```css
audio {
  // todo...
}
```
然而，并没有什么卵用。

{% alert warning %}
那么问题来了，如果只通过修改样式，能不能达到修改音频播放器的外观的功能呢？
{% endalert %}

#### 四、最终方案
答案是肯定的，我们来覆写 Audio 内置的一些全局样式，如：
```
audio::-webkit-media-controls-panel

audio::-webkit-media-controls-mute-button

audio::-webkit-media-controls-play-button

audio::-webkit-media-controls-timeline-container

audio::-webkit-media-controls-current-time-display

audio::-webkit-media-controls-time-remaining-display

audio::-webkit-media-controls-timeline

audio::-webkit-media-controls-volume-slider-container

audio::-webkit-media-controls-volume-slider

audio::-webkit-media-controls-seek-back-button

audio::-webkit-media-controls-seek-forward-button

audio::-webkit-media-controls-fullscreen-button

audio::-webkit-media-controls-rewind-button

audio::-webkit-media-controls-return-to-realtime-button

audio::-webkit-media-controls-toggle-closed-captions-button
```

嗯，意思很明了，就这么干。看起来并没有什么挑战，这仅仅只是开始！

#### 五、新的问题
我们碰到了新的问题，具体如下：
1. 播放／暂停 按钮不能替换；
2. 播放进度 slider 和 声音 slider 无法修改样式；

怎么办，这是个问题，距离设计师与众不同的音频播放器就差一步之遥了！

#### 六、继续探索
首先，突破点在于能否找到一个 `pause` 的样式类，来解决上述问题一。
其次，play 和 pause 如何触发图片互换。

我们知道，通过 `audio::-webkit-media-controls-play-button` 可以重新定义播放按钮的外观样式。