---
title: CSS 实用 Tips
date: 2017-11-12 21:19:26
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/tips0.png
thumbnailImagePosition: left
tags: 
- web
- css
comments: false
metaAlignment: center
categories: 技术博文 
---
作为前端，日常开发充满了各种挑战与乐趣，我们穷尽一切在奇葩的需求中提升用户体验。本文将列举一些前端开发中碰到的小众需求，并提供解决方案以供参考。
<!-- more -->
#### 一、修改鼠标手势图标
很多童鞋可能都用过：
```css
cursor: pointer | wait | hand | text | move | not-allowed;
```
但是，它还支持用户自定义图片，你知道吗？
```css
cursor: url(jartto.cur), url(http://jartto.wang/jartto.gif), auto;
```
下图为各浏览器支持情况：
![tips1](http://7xvi3w.com1.z0.glb.clouddn.com/tips1.png-blog)
{% alert success %}
记得在定义完自定义的游标之后在末尾加上一般性的游标，以防那些url所定义的游标不能使用。
{% endalert %}
这里有详细说明：
1.[css cursor 的可选值（鼠标的各种样式）](https://www.cnblogs.com/MY0101/p/6130089.html)
2.[Using URL values for the cursor property](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_User_Interface/Using_URL_values_for_the_cursor_property)

#### 二、box-shadow 三边阴影
`box-shadow` 大家都不陌生，我们一般这么用：
```css
box-shadow: h-shadow v-shadow blur spread color inset;
```
可是三边阴影该如何设置呢？请不要忽视设计师的脑洞🙈：
```css
#shadowBox {
  background-color: #ddd;
  margin: 0px auto;
  padding: 10px;
  width: 220px;
  box-shadow: 0px 8px 10px gray,
        -10px 8px 15px gray, 10px 8px 15px gray;
}
```
也许你会用得到，详情请参考：[CSS box-shadow on three sides of a div?](https://stackoverflow.com/questions/8738768/css-box-shadow-on-three-sides-of-a-div)

#### 三、超好用的 currentColor
先来举个例子，html代码：
```html
<a href="##" class="link"><i class="icon"></i>返回</a>
```
css 代码：
```css
.icon {
  display: inline-block;
  width: 16px; 
  height: 20px;
  background-image: url(http:jartto.wang/test.png);
  background-color: currentColor; /* 该颜色控制图标的颜色 */
  background-position: 0 0;
}
.link:hover {
  color: #333; /* 虽然改变的是文字颜色，但是图标颜色也一起变化了 */
}
```

{% alert success %}
currentColor 表示“当前的标签所继承的文字颜色”
{% endalert %}

了解更多，请参考：[CSS3 超高校级好用 CSS 变量](http://www.zhangxinxu.com/wordpress/2014/10/currentcolor-css3-powerful-css-keyword/)


#### 四、png 图片如何改颜色
网页中大多使用透明图片 `png` 格式，可是如果有天产品经理说，这个图标颜色能不能改成红色，恰巧设计师不能支持你，你该如何办？
其一：自己重新 `ps` 一下图片，换个颜色；
其二：只能上 `svg` 喽，搞成 `icon font`；

这里，我们来说第三种方式，妙不妙可以试试哦！
```css
.icon {
  display: inline-block;
  width: 20px; 
  height: 20px;
  overflow: hidden;
}
.icon-del {
  background: url(delete.png) no-repeat center;
}
.icon > .icon {
  position: relative;
  left: -20px;
  border-right: 20px solid transparent; /*下文注意点 2 有解释*/
  -webkit-filter: drop-shadow(20px 0);
  filter: drop-shadow(20px 0);    
}
```
html 代码:
```html
<p><strong>原始图标</strong></p>
<i class="icon icon-del"></i>
<p><strong>可以变色的图标</strong></p>
<i class="icon"><i class="icon icon-del"></i></i>
```
需要注意几点：
1.对于背景透明的 `png` 小图标而言，如果我们施加一个不带模糊的投影，就等同于生成了另外一个颜色的小图标；
2.在 `chrome` 浏览器下，如果一个元素的主体部分，无论以何种方式，只要在页面中不可见，其 `drop-shadow` 是不可见的。实体部分哪怕有 `1px` 可见，则 `drop-shadow` 完全可见。

{% alert success %}
原理其实很简单，使用了 `css3` 滤镜 `filter` 中的 `drop-shadow`，`drop-shadow` 滤镜可以给元素或图片非透明区域添加投影。
{% endalert %}

了解更多，请参考：
1.[PNG 格式小图标的 CSS 任意颜色赋色技术](http://www.zhangxinxu.com/wordpress/2016/06/png-icon-change-color-by-css/)
2.[demo 演示](http://www.zhangxinxu.com/study/201606/png-icon-color-fill.html)

#### 五、vh 与 vw
{% alert info %}
相对于视口的宽度。视口被均分为 `100` 单位的 `vw`，`vh` 同理。
{% endalert %}

我一般会在强制某一个容器占一整屏的时候使用，如：
```css
.box{
  height: calc(100vh - 50px);
}
```
视口高度减去页头固定区域，剩下的高度设置给容器。当然，我们也可以用它设置大小，如下：
```css
h1 {
  font-size: 8vw;
}
```
如果视口的宽度是 `200mm`，那么上述代码中h1元素的字号将为 `16mm`，即 (8x200)/100

值得注意的是，`less` 中使用需要加 `～`
```less
min-height: ~"calc(100vh - 68px)";
```

此外，还有一些如 `ch` ，`vmax`，`vmin` 等属性等着你去扩展，详情请移步：[css 参考手册之 vw](http://www.css88.com/book/css/values/length/vw.htm)

#### 六、如何去掉 chrome input 的背景黄色
当我们在做登陆页面的时候，在 `chrome` 中 `input` 会带上自动补全的黄色背景，大大影响美观。很多网站都没有去处理，但这并不难处理。作为高逼格的前端，这里就可以体现出你的价值，解决方案如下：
```css
input:-webkit-autofill {
  -webkit-box-shadow: 0 0 0px 1000px rgba(255, 255, 255, 0.5) inset !important;
}
```
当然，你也可以使用方案二，如下：
```css
input:-webkit-autofill {
  -webkit-animation-name: autofill;
  -webkit-animation-fill-mode: both;
}

@-webkit-keyframes autofill {
  to {
    color: #fff;
    background: transparent;
  }
}
```

深入了解请移步：
1.[chrome 表单自动填充去掉 input 黄色背景解决方案](http://blog.csdn.net/wangxiaohui6687/article/details/10149579)
2.[Removing input background colour for Chrome autocomplete](https://stackoverflow.com/questions/2781549/removing-input-background-colour-for-chrome-autocomplete)

#### 七、chrome 中设置小于 12px 的字体
这种改小字体，绝大部分肯定都是设计师的需求，因为小显得精致。
```css
ant-checkbox-wrapper {
 cursor: pointer;
 font-size: 10px;
 display: inline-block;
 -webkit-text-size-adjust: none; // 不支持
 transform: scale(0.9);
}
```

#### 八、0.5px border 如何操作
现行方法大致有如下几条：
1.使用渐变 `linear-gradient` 来操作，但需要注意浏览器兼容前缀；
```css
.div::after {
  content: " ";
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 1px;
  background-image: linear-gradient(0deg, transparent 50%, #e0e0e0 50%);
}
```
2.缩放 `scale` 处理：
```css
div::after{
  content: "";
  display: block;
  position: absolute;
  left: -50%;
  bottom: 0;
  width: 200%;
  height: 1px;
  background: #c3c3c3;
  -webkit-transform: scale(0.5);
}
```
3.使用 `background-image` 和 `css3` 的九宫格裁减
```css
.bd-t::after {
  content: " ";
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  border-top: 1px solid transparent;
  /* 下面用 stretch 和 round 都可以 */
  border-image: url('pic.png') 2 1 1 1 stretch; 
  -webkit-border-image: url('pic.png') 2 1 1 1 stretch;
}
```
深入了解可以参考：
1.[使用 css3 做 0.5px 的细线](http://blog.csdn.net/tyro_java/article/details/52013531)
2.[移动端页面0.5px border的实现](http://www.cnblogs.com/raoyunxiao/p/4241982.html)

#### 九、border 颜色渐变
```css
border-color:red green blue pink;
```
{% alert info %}
"border-width" 属性如果单独使用的话是不会起作用的。请首先使用 "border-style" 属性来设置边框。
{% endalert %}

#### 十、css3 画小箭头
这里推荐两种方式：
其一，通过 border 来实现
```css
/*箭头向上*/
.arrow-up {
  width:0;
  height:0;
  border-left:30px solid transparent;
  border-right:30px solid transparent;
  border-bottom:30px solid #fff;
}
/*箭头向下*/
.arrow-down {
  width:0;
  height:0;
  border-left:20px solid transparent;
  border-right:20px solid transparent;
  border-top:20px solid #0066cc;
}
```
其二，拼凑法（伪类或元素），将 `div` 隐藏两边或设置 `z-index`，然后旋转，放到合适位置。
```css
div {
  position: absolute;
  bottom: -2px;
  left: 7px;
  width: 10px;
  height: 10px;
  transform: rotate(-45deg);
  z-index: -1; /* 放在容器后，被遮盖住*/
}
```
比较简单，就不赘述了。

#### 十一、有趣的 drop-shadow 
用法如下：
```css
filter: drop-shadow(x偏移, y偏移, 模糊大小, 色值);
filter:drop-shadow(5px 5px 10px black)
```

{% alert info %}
CSS3 滤镜 filter 中的 drop-shadow，drop-shadow 滤镜可以给元素或图片非透明区域添加投影。
{% endalert %}

上文中已经提到了一种使用场景，这里还想说另一种，即使用拼凑法作出的小气泡，如果气泡需要阴影的话，请用 `drop-shadow` 来替代 `box-shadow`。

更多细节请查看：[drop-shadow 滤镜与 box-shadow 区别应用](http://www.zhangxinxu.com/wordpress/2016/05/css3-filter-drop-shadow-vs-box-shadow/)


#### 十二、修改浏览器默认滚动条
有时候一些不期而遇的滚动条会让页面很尴尬，这时候你还可以强行美化一下，参考代码如下：
```css
/*滚动条 start*/
::-webkit-scrollbar {
  width: 1px;
  height: 4px;
  background-color: #F5F5F5;
}
/*定义滚动条轨道 内阴影+圆角*/
::-webkit-scrollbar-track {
  box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
  background: #fff ;
}
/*定义滑块 内阴影+圆角*/
::-webkit-scrollbar-thumb {
  border-radius: 3px;
  box-shadow: inset 0 0 6px rgba(0,0,0,.3);
  // background-color:rgba(7, 170, 247, 0.7);
  background-color: transparent;
}
::-webkit-scrollbar-thumb:hover {
  border-radius: 3px;
  box-shadow: inset 0 0 6px rgba(0,0,0,.3);
  background-color:rgba(7, 170, 247, 1);
}
```
细节请参考：[修改浏览器默认的滚动条样式](http://blog.csdn.net/ning0_o/article/details/52188953)

#### 十三、safari placeholder bugs
在项目中遇到 `input` 的 `placeholder` 在 `safari` 下设置行高失效的问题，解决思路如下：
1.使用 `padding` 使提示文字居中，如果 `font-size:14px`, `UI` 高度为 `40px`,我们可以设 `height：14px,padding:13px 0;`
2.使用 `line-height:1px;`
3.使用 `vertical-align: middle;`

{% alert info %}
补充一条：Safari 来写 hack 即 [;line-height:1;]
{% endalert %}

详情请参考：[input 的 placeholder 在 safari 下设置行高失效](http://www.cnblogs.com/humaotegong/p/6629060.html?utm_source=itdadao&utm_medium=referral)

#### 十四、总结
这些都是在我日常开发中碰到的奇葩问题，回头想想其实挺有意思，遂整理总结之。如果日常开发中没有一些挑战，那开发还有什么乐趣。
