---
title: CSS 三大特性
date: 2017-02-08 07:37:26
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/logo.png
thumbnailImagePosition: left
tags: 
- css
- feature
comments: false
metaAlignment: center
categories: 技术博文 
---
最近看了许多 CSS 相关的文章，也深刻认识到自身的不足。本文属于查漏补缺，巩固基础知识。
<!-- more -->
#### 一、CSS 优先级
CSS 的组成单元是样式规则，也就是我们常说的 CSS Rule ，正如我们下面看到的这张图：
![css rule](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/css/rule.png)

众所周知，CSS 有一个核心特性：当多条样式规则中的同一个属性作用到了同一个元素，这些样式之间就会发生覆盖。
{% alert info %}
优先级高的 CSS 样式，将覆盖优先级低的 CSS 样式，成为最终网页元素的实际样式。
{% endalert %}

所以，为了保证页面有序并且正确的渲染，我们需要了解 CSS 优先级规则。大致的规则如下：
{% alert success %}
!important > 行内样式 > ID 选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性
{% endalert %}

当然，上述规则可以解决我们 80% 的疑惑，关键的是，还需要理解以下三点：
**1. CSS 属性继承（详情请看下文），我们来看一个例子：**
首先定义 html 结构：
```html
<div id="box">
  <p class="text">Jartto's blog</p>
</div>
```
为其添加样式规则：
```css
#box{color: red;}
.text{color: yellow;}
```
如上代码，那么到底显示什么颜色呢？答案就是 yellow 。你肯定会有这样的疑惑：ID 选择器的优先级高于 Class 选择器，且还有继承，应该是 red 。其实不然，这里不要被误导了，其实 box 提供的 color 对 text 来说只能继承 color 属性，而继承优先级是小于直接作用元素的。请务必明确如下规则：
- 继承得到的样式的优先级是最低的，在任何时候，只要元素本身有同属性的样式定义，就可以覆盖掉继承值；
- 在存在多个继承样式时，层级关系距离当前元素最近的父级元素的继承样式，具有相对最高的优先级；

**2. CSS 权重计算：**
CSS 权重计算其实就是统计选择符中的不同组成元素的个数，并以 (a,b,c,d) 这种形式来判断。其中，abcd分别代表了不同类别的选择符组成元素，且战斗力分别在不同的数量级，a最强，d最弱。从强到弱，这4个字母分别代表的类别是：
- 内联样式（Inline style） a = 1 – (1,0,0,0)；
- Id选择符 b = 1 – (0,1,0,0)
- 类选择符 （Class），伪类选择符（Pseudo class），属性选择符（Attribute） c = 1 – (0,0,1,0)
- 元素选择符（Element），伪元素选择符（Pseudo Element） d = 1 – (0,0,0,1)
这里引用一张图来说明：
![specificity_calculate_example](http://acgtofe.com/assets/used-images/posts/201304/specificity_calculate_example.png)

**3. !important 标识符；**
毫无疑问，!important 会有最高的优先级，这也经常让我们执行强制覆写样式。但是需要注意，一定是直接作用元素，不要和继承搞混淆，我们继续用上面的演示代码举例：
html 结构不变：
```html
<div id="box">
  <p class="text">Jartto's blog</p>
</div>
```
样式规则稍作修改，我们增加 !important 规则：
```css
#box{color: red!important;}
.text{color: yellow;}
```
明白了上述规则后，我们不难得出结论，结果依然是黄色，因为 !important 再牛，在这里他也是 color 继承，显然干不过直接作用类 text。

深入阅读请查看：
[CSS 优先级详细解析](http://acgtofe.com/posts/2013/04/css-specificity-explain-in-detail)

#### 二、CSS 继承性
{% alert success %}
CSS 继承性是指被包在内部的标签将拥有外部标签的样式性，即子元素可以继承父元素的属性。
{% endalert %}
有哪些属性是可以继承的呢，我们简单分一下类：
1.font-family,font-size,font-weight 等 f 开头的 CSS 样式；
2.text-align,text-indent 等 t 开头的样式；
3.color；

#### 三、CSS 层叠性
CSS 层叠性是指 CSS 样式在针对同一元素配置同一属性时，依据层叠规则（权重）来处理冲突，选择应用权重高的 CSS 选择器所指定的属性，一般也被描述为权重高的覆盖权重低的，因此也称作层叠。

每个 CSS 选择器都会有一个权重，当两个选择器同时配置同一元素的同一属性时（比如一个设置 color：red，另一个 color：black ），就会产生冲突，而解决冲突的方案就是 CSS 选择器的权重，权重高的来覆盖权重低的。

{% alert info %}
层叠就是浏览器对多个样式来源进行叠加，最终确定结果的过程。CSS 之所以有“层叠”的概念，是因为有多个样式来源。
{% endalert %}

#### 四、示例解说
为了巩固上面的知识，我们来高搞一些容易混淆的例子，简单的测试一下。
示例一：
```html
<div>
  <ol>
    <li> Jartto's blog </li>
  </ol>
</div>
```
样式规则，定义如下：
```css
div { color : red!important; }
ol { color : green; }
```
结果是 green ，因为 div 和 ol 都不是直接作用于 li 元素，所以他们拼的是继承性，根据属性继承的**就近原则**，显然 ol 更胜一筹。

示例二：
```html
<div id="box1" class="one">
  <div id="box2" class="two">
    <div id="box3" class="three"> Jartto's blog </div>
  </div>
</div>
```
CSS 规则如下：
```css
.one .two div { color : red; }
div #box3 { color : yellow; }
#box1 div { color : blue; }
```
猜一猜，结果是什么？

示例三：
```html
<div>
  <div>
    <div>Jartto's blog</div>
  </div>
</div>
```
为其设置如下规则：
```css
div div div { color: green; }
div div { color: red; }
div { color: yellow; }
```
{% alert info %}
CSS 规则都作用于文本，权重第一个大于第二个大于第三个（比较标签选择器数量），显示 green。
{% endalert %}

示例三，来自[CSS 三大特性](http://blog.csdn.net/k491022087/article/details/52319252)：
{% alert danger %}
注意：这里摘录的代码只为了演示 CSS 优先级以及层叠性，但是有悖于 CSS 解析规则，详情请参考[探究 CSS 解析原理](http://jartto.wang/2017/11/13/Exploring-the-principle-of-CSS-parsing/)。
{% endalert %}

```html
<div id="father" class="c1">
  <p id="son" class="c2"> Jartto's blog </p>
</div>
```
为了演示效果，我们制定如下 CSS 规则：
```css
#father #son { color: red };
#father p.c2 { color: green };
div.c1 p.c2 { color: yellow };
#father { color: pink !important};
div#father.c1 { color: blue };
```
我们暂且不去质疑这段代码的正确性，先来分析一下最终的呈现效果：
- 首先，`#father #son { color: red };` 设置了 `#father` 后代的 `#son` 字体颜色为 `red`；
- 其次，`#father p.c2 { color: green };` 和 `div.c1 p.c2 { color: yellow };` 想去作用，但 class 优先级低于 id ，所以 `Jartto's blog` 仍然为 `red`;
- 然后，又设置了 `#father { color: pink !important};` ，企图用 `!important` 去强制使 `pink` 生效，但是这对 `Jartto's blog` 来说只是 CSS 继承，根据权重规则，继承权重低于 id ，很显然颜色依然是 `red`；
- 最后，`div#father.c1 { color: blue };` 肯定权重更低了，所以依然无效，最终颜色只能是 `red`。

这是一个极端的例子，但是可以很好的理解这些例子大家可以去[ Runjs ](http://runjs.cn/code)在线测试一下，这样有助于更好的理解 CSS 的特性。

#### 五、补充说明
好了，相信到这里，大家对 CSS 的特性也有了深刻的认识。我们来解决一下之前抛出的疑问，再来看看这段拷贝过来的代码：
```html
<div id="father" class="c1">
  <p id="son" class="c2"> Jartto's blog </p>
</div>
```
CSS 规则如下：
```css
#father #son { color: red };
#father p.c2 { color: green };
div.c1 p.c2 { color: yellow };
#father { color: pink !important};
div#father.c1 { color: blue };
```
本文的重点也说完了，这里来挑一下刺，看看这段代码的问题：
1. `#father #son { color: red };` 两个 `id` 选择器，简直是浪费；
2. `id` 选择器中使用 `!important` 这就尴尬了；
3. `div#father` 这种写法把 `id` 选择器变成了一个不精准的类选择器了；
4. 代码可读性很差，几乎很难把握到重点，全是冗余代码；

可以看到，`#son { color: red };` 这样简单一行代码搞定的事情，被写的乱七八糟，漏洞百出。所以，我们不能只写代码，而不知其所以然，否则很有可能产生这样的后果，十分尴尬。

上文基本也涵盖了此部分内容，这里就不赘述了，详情请参考：[CSS 三大特性](http://blog.csdn.net/k491022087/article/details/52319252)