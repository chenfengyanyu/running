---
title: CSS 实用概要
date: 2018-03-06 10:59:15
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/outline0.png
thumbnailImagePosition: left
tags: 
- css
comments: false
metaAlignment: center
categories: 技术博文
---
用惯了框架和 UI 库，感觉对 CSS 的基础又有些模糊了。本文将列举一些 CSS 实用的知识点，想到哪就写到哪吧！
<!-- more -->
#### 一、Animation
`Animation` 使用起来并不难，但是因为属于简写属性且有六个动画属性，所以很容易混淆。
- animation-name
  - 规定需要绑定到选择器的 keyframe 名称
- animation-duration
  - 规定完成动画所花费的时间，以秒或毫秒计
- animation-timing-function:linear|ease|ease-in|ease-out|ease-in-out|...
  - 规定动画的速度曲线
- animation-delay
  - 规定在动画开始之前的延迟
- animation-iteration-count:n|infinite
  - 规定动画应该播放的次数
- animation-direction:normal|alternate
  - 规定是否应该轮流反向播放动画

{% alert info %}
此外，我们可能有动画停在最后一帧的需求，不要忘了 `CSS3` 的属性 `animation-fill-mode`
{% endalert %}

- animation-fill-mode : none | forwards | backwards | both;
  - forwards: 当动画完成后，保持最后一个属性值（在最后一个关键帧中定义）
  - backwards: 在 animation-delay 所指定的一段时间内，在动画显示之前，应用开始属性值（在第一个关键帧中定义）
  - both: 向前和向后填充模式都被应用

有的时候，动画也不需要自己写，可以多参考参考：[Animate.css](https://daneden.github.io/animate.css/)

#### 二、box-sizing
在 `CSS` 中一提到盒模型，就会提到 `box-sizing` 属性，它有俩个重要属性值：
```
box-sizing: border-box|content-box;
```
默认是正常盒模型，也就是：`CSS` 定义的宽高只包含 `Content` 的宽高。而如果设置 `border-box`，那么就是：`IE`盒模型，`CSS` 定义的宽高包括了 `Content`，`Padding` 和 `Border`。

#### 三、清除浮动的三种方式
1.伪类法
```
.clearfix::after {
  content: '';
  display: block;
  clear: both;
}
```
2.元素法，使用 `clear: both;`，与伪类法的区别就是有没有实际的元素；
3.利用 `overflow` 属性，应用值为 `hidden` 或 `auto` 的 `overflow` 属性有一个有用的副作用，这会自动清理包含的任何浮动元素；
```css
.jartto {
　background: yellow;
　overflow:hidden;
}
```

可以参考这篇文章：[CSS 清除浮动的几种方式](http://www.cnblogs.com/houyulei/archive/2011/12/16/2290229.html)

#### 四、定宽比
```html
<div class="constant-width-to-height-ratio"></div>
```
CSS
```css
.constant-width-to-height-ratio {
  background: #333;
  width: 50%;
  padding-top: 50%;
}
```

#### 五、修改滚动条默认样式
```css
/* Document scrollbar */
::-webkit-scrollbar {
  width: 8px;
}
::-webkit-scrollbar-track {
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  border-radius: 10px;
}
::-webkit-scrollbar-thumb {
  border-radius: 10px;
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.5);
}
/* Scrollable element */
.some-element::webkit-scrollbar {
}
```

#### 六、控制文本选中的颜色
先来段文本：
```html
<p class="custom-text-selection">Select some of this text.</p>
```
使用 `::selection` 伪类来设置颜色：
```css
.custom-text-selection::selection {
  background: deeppink;
  color: white;
}
```
此外，通过 `user-select: none` 来设置文本不可被选择：
```css
.unselectable {
  user-select: none;
}
```

#### 七、简单的圆环 Loading
先设置元素：
```html
<div class="donut"></div>
```
再定义一个圆环样式：
```css
.donut {
  display: inline-block;
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-left-color: #7983ff;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  animation: donut-spin 1.2s linear infinite;
}
```
最后，添加动画效果：
```css
@keyframes donut-spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
```

#### 八、雕刻的文字效果
{% alert info %}
通过设置 `0px` 的水平阴影和 `2px` 的垂直阴影，同时将阴影调成白色，造成视觉上雕刻的感觉。
{% endalert %}

```css
.etched-text {
  text-shadow: 0 2px white;
  font-size: 1.5rem;
  font-weight: bold;
  color: #b8bec5;
}
```
这里需要注意两点：
1.深色背景有利于此效果；
2.文字颜色适当的浅一些，形成褪色效果；

#### 九、居中布局的几种方式
1.使用 `flex`：
```css
.flexbox-centering {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

2.使用 `grid`：
```css
.grid-centering {
  display: grid;
  justify-content: center;
  align-items: center;
}
```

3.行高辅助：
```css
.box {
  width: 100%;
  height: 300px;
  line-height: 300px;
  text-align: center;
}
```

#### 十、使用渐变
文字渐变：
```css
.gradient-text {
  background: -webkit-linear-gradient(pink, red);
  -webkit-text-fill-color: transparent;
  -webkit-background-clip: text;
}
```
说明：
- background: -webkit-linear-gradient(...) 为文字设置渐变背景
- webkit-text-fill-color: transparent 使用透明色填充文字
- webkit-background-clip: text 用文本剪辑背景，用渐变背景填充文本作为颜色

#### 十一、0.5px 问题
也就是我们常说的 `Hairline` 问题：
```css
.hairline-border {
  box-shadow: 0 0 0 1px;
}
@media (min-resolution: 2dppx) {
  .hairline-border {
    box-shadow: 0 0 0 0.5px;
  }
}
@media (min-resolution: 3dppx) {
  .hairline-border {
    box-shadow: 0 0 0 0.33333333px;
  }
}
@media (min-resolution: 4dppx) {
  .hairline-border {
    box-shadow: 0 0 0 0.25px;
  }
}
```

#### 十二、文本下划线动画
html:
```html
<p class="hover-underline-animation">Hover this text to see the effect!</p>
```
css:
```css
.hover-underline-animation {
  display: inline-block;
  position: relative;
  color: #0087ca;
}
.hover-underline-animation::after {
  content: '';
  position: absolute;
  width: 100%;
  transform: scaleX(0);
  height: 2px;
  bottom: 0;
  left: 0;
  background-color: #0087ca;
  transform-origin: bottom right;
  transition: transform 0.25s ease-out;
}
.hover-underline-animation:hover::after {
  transform: scaleX(1);
  transform-origin: bottom left;
}
```

#### 十三、CSS 定义和使用变量
```css
:root {
  --base-bottom-margin: 20px;
  --base-line-height:   120%;
  --text-color:         gray;
}
h1 {
  margin-bottom: var( --base-bottom-margin );
  font-size: 42px;
  line-height: var( --base-line-height );
  color: var( --text-color );
}
```

#### 十四、按钮点击效果－扩散光圈

```html
<button class="mouse-cursor-gradient-tracking">
  <span>Hover me</span>
</button>
```

```css
.mouse-cursor-gradient-tracking {
  position: relative;
  background: #7983ff;
  padding: 0.5rem 1rem;
  font-size: 1.2rem;
  border: none;
  color: white;
  cursor: pointer;
  outline: none;
  overflow: hidden;
}
.mouse-cursor-gradient-tracking span {
  position: relative;
}
.mouse-cursor-gradient-tracking::before {
  --size: 0;
  content: '';
  position: absolute;
  left: var(--x);
  top: var(--y);
  width: var(--size);
  height: var(--size);
  background: radial-gradient(circle closest-side, pink, transparent);
  transform: translate(-50%, -50%);
  transition: width 0.2s ease, height 0.2s ease;
}
.mouse-cursor-gradient-tracking:hover::before {
  --size: 200px;
}
```

```js
var btn = document.querySelector('.mouse-cursor-gradient-tracking')
btn.onmousemove = function(e) {
  var x = e.pageX - btn.offsetLeft
  var y = e.pageY - btn.offsetTop
  btn.style.setProperty('--x', x + 'px')
  btn.style.setProperty('--y', y + 'px')
}
```

{% alert warning %}
需要注意的是：如果该元素的父元素有 position: relative，我们也需要减去父元素的 offset：
{% endalert %}
```js
var x = e.pageX - btn.offsetLeft - btn.offsetParent.offsetLeft
var y = e.pageY - btn.offsetTop - btn.offsetParent.offsetTop
```

#### 十五、使用 hover 实现弹出菜单
先来定义 `hover` 容器：
```html
<div class="reference">
  <div class="popout-menu">
    Popout menu
  </div>
</div>
```
再来定义样式：
```css
.reference {
  position: relative;
}
.popout-menu {
  position: absolute;
  visibility: hidden;
  left: 100%;
}
.reference:hover > .popout-menu {
  visibility: visible;
}
```

这里有两个问题：
其一，因为 `popout-menu` 是 `reference` 的子容器，所以悬停父容器，子容器并不会收缩；
其二，这里子元素使用了绝对定位，所以多个元素需要注意重叠问题；


#### 十六、重置所有样式
通过一个属性，将样式重置到默认状态：
```css
.reset-all-styles {
  all: initial;
}
```

#### 十七、CSS 实现锯齿
html：
```html
<div class="banner-vertical"></div>
<div class="banner-horizontal"></div>
<div class="banner"></div>
```
scss：
```scss
body { margin: 2em; }
div { height: 150px; margin-bottom: 15px; }

@mixin saw-tooth-vertical($width: 12px, $bg: #fff) {
  background-image:
    /* Top jagged */
    linear-gradient(135deg, $bg 50%, transparent 50%),
    linear-gradient(225deg, $bg 50%, transparent 50%),
    /* Bottom jagged */
    linear-gradient(45deg, $bg 50%, transparent 50%),
    linear-gradient(-45deg, $bg 50%, transparent 50%);
  background-position:
    /* Top jagged */
    top left, top left,
    /* Bottom jagged */
    bottom left, bottom left;
  background-size: $width $width;
  background-repeat: repeat-x;
}
@mixin saw-tooth-horizontal($width: 12px, $bg: #fff) {
  background-image:
    /* Left jagged */
    linear-gradient(135deg, $bg 50%, transparent 50%),
    linear-gradient(45deg, $bg 50%, transparent 50%),
    /* Right jagged */
    linear-gradient(-135deg, $bg 50%, transparent 50%),
    linear-gradient(-45deg, $bg 50%, transparent 50%);
  background-position:
    /* Left jagged */
    top left, top left,
    /* Right jagged */
    top right, top right;
  background-size: $width $width;
  background-repeat: repeat-y;
}
.banner-vertical {
  background-color: yellowgreen;
  @include saw-tooth-vertical();
}

.banner-horizontal {
  background-color: crimson;
  @include saw-tooth-horizontal();
}

@mixin saw-tooth($width: 12px, $bg: #fff) {
  background-image:
    /* Top jagged */
    linear-gradient(135deg, $bg 50%, transparent 50%),
    linear-gradient(225deg, $bg 50%, transparent 50%),
    /* Bottom jagged */
    linear-gradient(45deg, $bg 50%, transparent 50%),
    linear-gradient(-45deg, $bg 50%, transparent 50%),
    /* Left jagged */
    linear-gradient(135deg, $bg 50%, transparent 50%),
    linear-gradient(45deg, $bg 50%, transparent 50%),
    /* Right jagged */
    linear-gradient(-135deg, $bg 50%, transparent 50%),
    linear-gradient(-45deg, $bg 50%, transparent 50%);
  background-position:
    /* Top jagged */
    top left, top left,
    /* Bottom jagged */
    bottom left, bottom left,
    /* Left jagged */
    top left, top left,
    /* Right jagged */
    top right, top right;
  background-size: $width $width;
  background-repeat: repeat-x, repeat-x, repeat-x, repeat-x, repeat-y, repeat-y, repeat-y, repeat-y;
}

.banner {
  background-color: skyblue;
  @include saw-tooth();
}
```
具体实例可参考：[CSS saw-tooth borders](https://codepen.io/yangg/pen/mJBWPG)

#### 十八、控制兄弟元素
使用 `:not`，控制所有兄弟元素的 hover 状态，而不控制当前：
```css
.sibling-fade:hover span:not(:hover) {
  opacity: 0.5;
}
```

#### 十九、系统默认字体设置
```css
.system-font-stack {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen-Sans, Ubuntu,
    Cantarell, 'Helvetica Neue', Helvetica, Arial, sans-serif;
}
```

#### 二十、总结
文中主要示例都来参考自[30 Seconds of CSS](https://atomiks.github.io/30-seconds-of-css/#box-sizing-reset)，当然也适当的补充了一些相关知识，总的来说，本文其实更像是一篇 `CSS` 实用笔记，目的就是对基础知识进行一次简单回顾。
