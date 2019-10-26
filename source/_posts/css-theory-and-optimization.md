---
title: CSS 渲染原理以及优化策略
date: 2019-10-23 21:04:53
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/logo1.png
thumbnailImagePosition: left
tags: 
- css
- optimazation
- 原理
- 优化
comments: false
metaAlignment: center
categories: 技术博文 
---
提起 CSS 很多童鞋都很不屑，尤其是看到 RedMonk 2019 Programming Language Rankings 的时候，CSS 竟然排到了第七位。
<!-- more -->
我们先来看看这张排行榜：
![Rank](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/rank.png)

#### 一、浏览器构成
![Browser](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/browser.png)

- User Interface：用户界面，包括浏览器中可见的地址输入框、浏览器前进返回按钮、书签，历史记录等用户可操作的功能选项。
- Browser engine：浏览器引擎，可以在用户界面和渲染引擎之间传送指令或在客户端本地缓存中读写数据，是浏览器各个部分之间相互通信的核心。
- Rendering engine：渲染引擎，解析 DOM 文档和 CSS 规则并将内容排版到浏览器中显示有样式的界面，也就是排版引擎，我们常说的浏览器内核主要指的就是渲染引擎。
- Networking：网络功能模块，是浏览器开启网络线程发送请求以及下载资源的模块。
- JavaScript Interpreter：JS 引擎，解释和执行 JS 脚本部分，例如 V8 引擎。
- UI Backend：UI 后端则是用于绘制基本的浏览器窗口内控件，比如组合选择框、按钮、输入框等。
- Data Persistence：数据持久化存储，涉及 Cookie、LocalStorage 等一些客户端存储技术，可以通过浏览器引擎提供的 API 进行调用。

![Render](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/render.png)

#### 二、渲染引擎
![Render2](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/render2.png)


#### 三、CSS 特性
1.优先级：
![specificity1](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/specificity1.png)
!important > 行内样式（权重1000） > ID 选择器（权重 100） > 类选择器（权重 10） > 标签（权重1） > 通配符 > 继承 > 浏览器默认属性

示例代码一：https://jsfiddle.net/a5xtdoq7/1/
```html
<div >
  <p id="box" class="text">Jartto's blog</p>
</div>
```

样式规则：
```css
#box{color: red;}
.text{color: yellow;}
```
升级一下：https://jsfiddle.net/a5xtdoq7/3/
```html
<div id="box">
  <p class="text">Jartto's blog</p>
</div>
```
样式规则：
```css
#box{color: red;}
.text{color: blue;}
```

2.继承性
* 继承得到的样式的优先级是最低的，在任何时候，只要元素本身有同属性的样式定义，就可以覆盖掉继承值；
* 在存在多个继承样式时，层级关系距离当前元素最近的父级元素的继承样式，具有相对最高的优先级；

有哪些属性是可以[继承](https://m.html.cn/qa/css3/12784.html)的呢，我们简单分一下类：
```
1.font-family,font-size,font-weight 等 f 开头的 CSS 样式；
2.text-align,text-indent 等 t 开头的样式；
3.color；
```
详细的规则，请看下图：
![inherent](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/inherent.png)

示例代码二：https://jsfiddle.net/a5xtdoq7/
```html
<div>
  <ol>
    <li> Jartto's blog </li>
  </ol>
</div>
```
样式规则定义如下：
```css
div { color : red!important; }
ol { color : green; }
```

3.层叠性
![specificity2](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/specificity2.png)
层叠就是浏览器对多个样式来源进行叠加，最终确定结果的过程。CSS 之所以有“层叠”的概念，是因为有多个样式来源。

CSS 层叠性是指 CSS 样式在针对同一元素配置同一属性时，依据层叠规则（权重）来处理冲突，选择应用权重高的 CSS 选择器所指定的属性，一般也被描述为权重高的覆盖权重低的，因此也称作层叠。

示例代码三：https://jsfiddle.net/a5xtdoq7/2/
```html
<div >
  <p class="two one">Jartto's blog</p>
</div>
```
样式规则如下：
```css
.one{color: red;}
.two{color: blue;}
```
升级代码：https://jsfiddle.net/a5xtdoq7/6/
```html
<div>
  <div>
    <div>Jartto's blog</div>
  </div>
</div>
```
样式规则：
```css
div div div { color: green; }
div div { color: red; }
div { color: yellow; }
```
继续升级：https://jsfiddle.net/a5xtdoq7/7/
```html
<div id="box1" class="one">
  <div id="box2" class="two">
    <div id="box3" class="three"> Jartto's blog </div>
  </div>
</div>
```
样式：
```css
.one .two div { color : red; }
div #box3 { color : yellow; }
#box1 div { color : blue; }
```
权重：
```
0 0 2 1
0 1 0 1
0 1 0 1
```
验证一下：https://jsfiddle.net/a5xtdoq7/9/
```html
<div id="box1" class="one">
  <div id="box2" class="two">
    <div id="box3" class="three"> Jartto's blog </div>
  </div>
</div>
```
样式如下：
```css
.one .two div { color : red; }
#box1 div { color : blue; }
div .three { color : green; }
```
权重：
```
0 0 2 1
0 1 0 1
0 0 1 1
```

#### 四、CSS 语法解析过程
![render3](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/render3.png)

![css-rule](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/css-rule.jpg)

![CSSOM](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/test_CSSOM.png)

![ast2](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/ast2.png)


![rule2](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/rule2.png)




CSS 解析和 Webkit 有什么关系？


#### 五、CSS 选择器执行顺序

#### 六、高效的 ComputedStyle

#### 七、CSS 书写顺序对性能有影响吗？


#### 八、优化策略
1.使用 id selector 非常的高效。在使用 id selector 的时候需要注意一点：因为 id 是唯一的，所以不需要既指定 id 又指定 tagName：
```css
/* Bad  */
p#id1 {color:red;}  

/* Good  */
#id1 {color:red;}
```

2.避免深层次的 node ，譬如：
```css
/* Bad  */
div > div > div > p {color:red;} 
/* Good  */
p-class{color:red;}
```

3.不要使用 attribute selector，如：p[att1=”val1”]。这样的匹配非常慢。更不要这样写：p[id=”id1”]。这样将 id selector 退化成 attribute selector。
```css
/* Bad  */
p[id="id1"]{color:red;}  
p[class="class1"]{color:red;}  
/* Good  */
#id1{color:red;}  
.class1{color:red;}
```

4.通常将浏览器前缀置于前面，将标准样式属性置于最后，类似：
```css
.foo {-moz-border-radius: 5px;border-radius: 5px; }
```
Css 规范：http://nec.netease.com/standard/css-sort.html

5.遵守 CSSLint 规则：https://github.com/CSSLint/csslint
```
font-faces        　　　　  　　　不能使用超过5个web字体
import        　　　　　　　 　　  禁止使用@import
regex-selectors        　　　　  禁止使用属性选择器中的正则表达式选择器
universal-selector    　　 　　  禁止使用通用选择器*
unqualified-attributes    　　　禁止使用不规范的属性选择器
zero-units            　　 　　　0后面不要加单位
overqualified-elements    　　　使用相邻选择器时，不要使用不必要的选择器
shorthand        　　　　　　　　 简写样式属性
duplicate-background-images    相同的url在样式表中不超过一次
```

6.减少 CSS 文档体积
-移除空的css规则（Remove empty rules）
-值为 0 不需要单位
-使用缩写
-属性值为浮动小数0.**，可以省略小数点之前的0；
-不给 h1-h6 元素定义过多的样式

7.CSS Will Change
WillChange 属性，允许作者提前告知浏览器的默认样式，使用一个专用的属性来通知浏览器留意接下来的变化，从而优化和分配内存。

8.不要使用@import
使用@import引入CSS会影响浏览器的并行下载。使用@import引用的CSS文件只有在引用它的那个css文件被下载、解析之后，浏览器才会知道还有另外一个css需要下载，这时才去下载，然后下载后开始解析、构建render tree等一系列操作。
多个@import会导致下载顺序紊乱。在IE中，@import会引发资源文件的下载顺序被打乱，即排列在@import后面的js文件先于@import下载，并且打乱甚至破坏@import自身的并行下载。

9.避免过分重排（reflow）
浏览器重新计算布局位置与大小。
常见的重排元素;
```
width 
height 
padding 
margin 
display 
border-width 
border 
top 
position 
font-size 
float 
text-align 
overflow-y 
font-weight 
overflow 
left 
font-family 
line-height 
vertical-align 
right 
clear 
white-space 
bottom 
min-height
```

10.高效利用 computedStyle 
公共类
慎用 ChildSelector

11.减少昂贵属性：
当页面发生重绘时，它们会降低浏览器的渲染性能。所以在编写CSS时，我们应该尽量减少使用昂贵属性，如box-shadow/border-radius/filter/透明度/:nth-child等。

12.依赖继承。如果某些属性可以继承，那么自然没有必要在写一遍。

13.遵守 CSS 顺序规则

