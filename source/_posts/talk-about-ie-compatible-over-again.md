---
title: 再谈ie浏览器兼容问题
date: 2016-12-06 12:25:40
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/20161202_ie.png
thumbnailImagePosition: left
tags: 
- ie
- hack
- js
- 浏览器兼容
comments: true
metaAlignment: center
categories: 技术博文
---
随着技术日新月异的发展，我以为已经可以完全甩掉ie浏览器，驰骋在新技术的海洋里。却不想，ie兼容问题如影随行。
<!-- more -->
当然，尝试解决ie浏览器兼容问题，并不代表我会去拥护它。我想做的只是为解决浏览器兼容提供一些个人思路，毕竟，很多公司还会有此类的需求。

{% alert info %}
这里，我将提供一套解决方案，以及如果碰到ie浏览器兼容问题，我们该怎么办？
{% endalert %}

#### 一、明确你要兼容的浏览器范围
很简单，当你明确了要兼容的浏览器范围，不需要兼容的浏览器就可以一并处理了。例如：直接弹出提示，指引用户下载chrome，firefox等现代浏览器。简单粗暴，但却有效。

那么我们如何去过滤呢，这时候，条件注释就可以上场了，用法如下：
```html
<!--[if !IE]> 除IE外都可识别 <![endif]-->
<!--[if IE]> 所有的IE可识别 <![endif]-->
<!--[if IE 6]> 仅IE6可识别 <![endif]-->
<!--[if lt IE 6]> IE6以及IE6以下版本可识别 <![endif]-->
<!--[if gte IE 6]> IE6以及IE6以上版本可识别 <![endif]-->
<!--[if IE 7]> 仅IE7可识别 <![endif]-->
<!--[if lt IE 7]> IE7以及IE7以下版本可识别 <![endif]-->
<!--[if gte IE 7]> IE7以及IE7以上版本可识别 <![endif]-->
<!--[if IE 8]> 仅IE8可识别 <![endif]-->
<!--[if IE 9]> 仅IE9可识别 <![endif]-->
```
符号解释：
```
!(非)：[if !IE]
lt(小于)：[if lt IE 6]
lte(小于或等于)：[if lte IE 6]
gt(大于)：[if gt IE 8]
gte(大于或等于)：[if gte IE 9]
&(与)：[if (gt IE 6)&(lt IE 8)]
|(或)：[if (IE 6)|(IE 7)]
```
嗯，不错，一大半的负担被卸掉了。

因为我足够幸运，只需要解决ie9浏览器的兼容问题。所以，可以这么写：
```html
<!--[if lte IE 8]><script src="/zh/assets/js/ie-warning.js"></script><![endif]-->
```
js中设置了cookie和url重定向，引导用户去下载chrome浏览器，这里就不细说了。

当然，兼容问题哪有这么容易就搞定的，我们继续往下走～

#### 二、检查页面中的伪类和伪元素
这里还是以ie9示例，下面列出ie9对伪类的支持情况：
```css
//ie9不支持的伪类和伪元素
:default
:valid
:invalid
:in-range
ut-of-range
:required
ptional
:read-only
:read-write
::before
::after
::first-letter
::first-line
::value
::choices
::repeat-item
::repeat-index
```
{% alert info %}
如果你要兼容ie8，你肯定也会搜到一份类似的支持文档，其他ie版本以此类推。
{% endalert %}

对于不支持的情况，我们只能换方案，举个简单🌰：
清除浮动的方式有很多，为了代码优雅，恰巧我们选择了用after伪元素的方式，如下：
```css
.clear:after {
　content:".";
　height:0;
　visibility:hidden;
　display:block;
　clear:both;
}
```
很好，ie9说：“不可以！”。好，那就换一个大家都比较容易接受的方案，如下：
```css
.clean{
	clear:both;
}
```
不外乎就是在页面加个空容器，虽然不优雅，但足够通用。

说了这么多，想表达的意思无非就是：
{% alert success %}
不支持的伪类和伪元素，换通用的方案去兼容。
{% endalert %}

#### 三、排查css3兼容问题
如果你对css3浏览器支持情况不是很了解，点击这里[检测](http://caniuse.com/#search=transform)
![检测动图](http://7xvi3w.com1.z0.glb.clouddn.com/20161202_compatible.png)

#### 四、使用css hack
到这里，浏览器危险属性已经都排查完了。我们开始局部细节调整，这里就需要用到css hack了。
ie6 hack:
```css
Selector { _property: value; } /* property: value 表示 css 的属性名: 属性值 下同 */
Selector { _property/**/: /**/value; }
Selector { -property: value; } /*IE6 css hack常用习惯推荐使用下划线_ */
```
ie7 hack:
```css
*+html Selector {}
*:first-child+html Selector {}
```
ie8 hack:
```css
Selector { /* 注意看value值的 */
    property: value1; /* W3C MODEL */
    property: value2\0; /* IE 8+ */
    property: value1\9\0; /* IE 9+ */
}
```
ie6,7,8共有hack:
```css
Selector { property: value\9; }
```
ie6,7共有hack：
```css
Selector { *property: value; }
Selector { #property: value; }
Selector { +property: value; }
```
ie8 hack:
```css
Selector { property: value\0; }
```
ie9 hack:
```css
Selector { property: value\9\0; }
```
根据实际的应用场景，选择不同的hack，用css覆写就可以了。再举一个简单的栗子：
```css
// ie9 hack
.index_scrm{
    .swiper-wrapper{
        overflow:hidden\9\0;
        height: 380px\9\0;
    }
}
```
想了解更详细的css hack，请走这边：[传送门](http://www.webhj.com/hj-650.html)

#### 五、js判断浏览器类型
css hack也用上了，但是某种情况下，hack也并不完美。所以，我们再加一道保险，用js来做局部控制。
{% alert info %}
这里使用js检测浏览器类型，不仅仅是为了解决兼容问题，还可以干一些你想干的其他事情，不要被局限。
{% endalert %}
方式一：
```js
var isIE=!!window.ActiveXObject;
var isIE6=isIE&&!window.XMLHttpRequest;
var isIE8=isIE&&!!document.documentMode;
var isIE7=isIE&&!isIE6&&!isIE8;
if (isIE){
	if (isIE6){
		alert('ie6');
	}else if (isIE8){
		alert('ie8');
	}else if (isIE7){
		alert('ie7');
	}
}
```
方式二：
```js
if(navigator.appName == 'Microsoft Internet Explorer' && navigator.appVersion.match(/6./i)=='6.'){ 
	alert('IE 6'); 
} 
else if(navigator.appName == 'Microsoft Internet Explorer' && navigator.appVersion.match(/7./i)=='7.'){ 
	alert('IE 7'); 
} 
else if(navigator.appName == 'Microsoft Internet Explorer' && navigator.appVersion.match(/8./i)=='8.'){ 
	alert('IE 8'); 
} 
else if(navigator.appName == 'Microsoft Internet Explorer' && navigator.appVersion.match(/9./i)=='9.'){ 
	alert('IE 9'); 
} 
```
方式三：
```js
if(navigator.userAgent.indexOf('Opera') != -1) { 
	alert('Opera'); 
} 
else if(navigator.userAgent.indexOf('MSIE') != -1) { 
	alert('Internet Explorer'); 
} 
else if(navigator.userAgent.indexOf('Firefox') != -1) { 
	alert('Firefox'); 
} 
else if(navigator.userAgent.indexOf('Netscape') != -1) { 
	alert('Netscape'); 
} 
else if(navigator.userAgent.indexOf('Safari') != -1) { 
	alert('Safari'); 
} 
else{ 
	alert('无法识别的浏览器。'); 
} 
```
方式四：
```js
if(!+'\v1' && !'1'[0]){ 
	alert('ie6或ie7') 
} 
```
以上js代码基本覆盖了大部分主流浏览器，到这里90%以上的浏览器兼容问题应该都可以搞定了吧。

#### 六、我的问题清单
大概总结一下，以下是已知ie9的兼容问题：
1.swiper3.0以上，不支持ie9；
解决方案：换swiper2.*版本，或者换插件。

2.after等伪元素不支持；
解决方案：用div或者其他元素等效替代。

3.ie9支持的属性background-size怎么也会失效；
解决方案：如下
```css
//这么用
>.two {
    background-image: url(../image/yunzi/temp04.png);
    background-size: 100%;
    width: 300px;
    background-position: center;
}
//而不要这么用
>.three {
    background-image: url(../image/yunzi/temp05.gif);
    background-size: 170%;//不给宽度，容易受影响，显示异常
    background-position: center 5%;
}
```

4.flex布局失效，如何解决；
解决方案：用float或者flex.css库去兼容。

5.display: none\9\0失效，需要改成display: none\9，好奇怪;
解决方案：好奇怪，不按套路？

6.ie9中特定条件下绝对定位失效？
相对定位内的绝对定位元素内部的相对定位元素，ie9下会嵌套异常。
解决方案：简化嵌套结构。

7.keyframes ，ie9不兼容；
解决方案：降级动画。

8.ie9中img只给width或者height会塌陷；
解决方案：严格按照图片比例，来计算显示比例，两个属性都要设值，否则图片显示异常。

#### 七、小结
文章结构我反复改了很多次，无非想让整个解决方案清晰明了。如果有不当或者表达不清楚的地方，希望大家可以提出来。共同探讨，共同进步～

#### 八、参考文章
浏览器类型判断：
http://www.jb51.net/article/27876.htm
css hack：
http://www.webhj.com/hj-650.html
ie9伪类支持：
http://blog.csdn.net/lml_little/article/details/51074779
css3属性检测：
http://caniuse.com/#search=transform










