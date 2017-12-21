---
title: HTML5 拖放实现
date: 2017-10-23 23:47:24
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/dragdrop.png
thumbnailImagePosition: left
tags: 
- HTML5
- drag
- drop
- demo
comments: false
metaAlignment: center
categories: 技术博文 
---
对于拖拽我们并不陌生，尤其是在 HTML5 支持拖拽后，让这一切变的更加容易了。本文旨在探讨 H5 拖拽方案、实现步骤，以及相关原理。
<!-- more -->
#### 一、我理解的拖拽
为了更好的说明，这里我强行分类，大致如下：
1. 操作类拖拽，譬如，拖拽上传；
2. 功能性拖拽，拖拽排序，具有指向性，位置互换；
3. 使用方便型，对某些固定元素，使其随处可放；

{% alert info %}
拖拽实现的思路都是一致的，唯一差别就是表现形式。
{% endalert %}

#### 二、直接上手
让我们逐步深入，从示例开始吧（文中所用[示例地址](https://github.com/chenfengyanyu/dragAndDrop)）！

1.首先，设置可拖动元素，我们使用 `draggable`

```html
<img src="img/1.png" width="250" draggable="true" />
```
为了方便下文具体说明，这里我添加了 `id` 和 `ondragstart` 事件，如下：
```html
<img id="jartto" src="img/1.png" width="250"  draggable="true" ondragstart="drag(event)" />
```
`ondragstart` 属性调用了一个函数，drag(event)，它规定了被拖动的数据。

2.然后，规定当元素被拖动时，会发生什么。关键词 `setData()`
```js
function drag(ev)
{
  ev.dataTransfer.setData('Text',ev.target.id);
}
```
`dataTransfer.setData()` 方法设置被拖数据的数据类型和值，后面我会[详细说明](#dataTransfer)。
{% alert info %}
这里需要注意 setData 的数据类型是 'Text'，值是可拖动元素的 id 。
{% endalert %}

3.最后，放到何处，`ondragover` 事件规定在何处放置被拖动的数据，当放置被拖数据时 `ondrop` 会被触发。
```html
<div class="box" ondrop="drop(event)" ondragover="allowDrop(event)"></div>
```
```js
function drop(ev)
{
  ev.preventDefault();
  var data=ev.dataTransfer.getData("Text");
  ev.target.appendChild(document.getElementById(data));
}

function allowDrop(ev)
{
  ev.preventDefault();
}
```
这里需要注意几个问题：
1. 默认地，无法将数据/元素放置到其他元素中。如果需要设置允许放置，我们必须阻止对元素的默认处理方式，即通过调用 ondragover 事件的 event.preventDefault() 方法；
2. 调用 preventDefault() 来避免浏览器对数据的默认处理（drop 事件的默认行为是以链接形式打开）；
3. 通过 dataTransfer.getData('Text') 方法获得被拖的数据。该方法将返回在 setData() 方法中设置为相同类型的任何数据。

详情请查看，[demo1 示例代码](https://github.com/chenfengyanyu/dragAndDrop/blob/master/demo1.html)。

#### 三、来回拖放
到这里我们已经可以将一个元素拖到指定容器中，那么如何让元素在两个容器中来回拖放呢？
继续保持之前的思路，我们做一点简单的调整：

1.首先，确保有俩元素；
```css
.jartto1,
.jartto2 {
  float: left;
  width: 150px;
  height: 140px;
  margin: 10px;
  border-radius: 3px;
  border: 1px solid #aaaaaa;
}
```
2.更改 html 结构
```html
<div class="jartto1" ondrop="drop(event)" ondragover="allowDrop(event)">
  <img id="drag1" src="img/2.png" width="150" 
  draggable="true" ondragstart="drag(event)" />
</div>

<div class="jartto2" ondrop="drop(event)" ondragover="allowDrop(event)"></div>
```
3.触发方法
```js
function allowDrop(ev) {
  ev.preventDefault();
}

function drag(ev) {
  ev.dataTransfer.setData('Text', ev.target.id);
}

function drop(ev) {
  ev.preventDefault();
  var data = ev.dataTransfer.getData('Text');
  ev.target.appendChild(document.getElementById(data));
}
```
代码看着变化并不大，重点其实在这里：
```html
<div class="jartto1" ondrop="drop(event)" ondragover="allowDrop(event)">
  <img id="drag1" src="img/2.png" width="150" 
  draggable="true" ondragstart="drag(event)" />
</div>
```
{% alert success %}
可以看到，我们对两个容器都设置了 `ondrop` 和 `ondragover` , 这样两个容器都允许放置，也就实现了来回拖放。
{% endalert %}

详情请查看，[demo2 示例代码](https://github.com/chenfengyanyu/dragAndDrop/blob/master/demo2.html)。

#### 四、随处拖放 <a name="dragEverywhere">drag everywhere</a>
嗯，来回拖拽也实现了，我们继续升级，来看看随处拖放。这里稍微复杂一点，先简单梳理一下思路：

1.首先设置 `dragover` 事件作用于整个 `document`，确保元素可以随处放置；      
2.在 `dragstart` 事件中计算鼠标拖拽的位置；             
3.在 `drop` 事件中将上步计算的 `offset` 和 `clientX`，`clientY` 运算，计算出元素放置位置；            

这里我以 Angularjs 举例说明，其他框架实现形式可能有差别，仅作为参考。

首先，创建指令，并设置作用元素的 `draggable` 属性；
```js
angular.module('app')
  .directive('dragdrop', [function () {
    return {
      link: (scope, element, attrs) => {
        var el = element[0];
        el.draggable = true;
      }
    };
  }
]);
```
其次，存储拖拽数据；
```js
el.addEventListener('dragstart',
  function(e) {
    let style = window.getComputedStyle(e.target, null);
    e.dataTransfer.setData('text/plain', 
      `${(parseInt(style.getPropertyValue('left')) - e.clientX)},${(parseInt(style.getPropertyValue('top')) - e.clientY)}`
    );
  },false);
```
然后，我们必须阻止对元素的默认处理方式，从而使元素可以放置到其他元素中；
```js
document.body.addEventListener('dragover',function(e) {
  e.preventDefault(); 
  return false; 
},false); 
```
再次，将拖拽数据取出，重新确定元素位置；
```js
document.body.addEventListener('drop',function(e) {
  let offset = e.dataTransfer.getData('text/plain').split(',');
  el.style.left = (e.clientX + parseInt(offset[0])) + 'px';
  el.style.top = (e.clientY + parseInt(offset[1])) + 'px';
  e.preventDefault();
  return false;
},false);
```
最后，我们可以这样使用：
```html
<div id="jartto" dragdrop></div>
```
{% alert info %}
其实我们还可以在拖拽的过程中做一些样式处理（加类减类），以获取更好的用户体验效果，但这并不是必要的，所以代码中并没有体现。
{% endalert %}

详情请查看，[demo6 示例代码](https://github.com/chenfengyanyu/dragAndDrop/blob/master/demo6.js)

当然，我们也可以通过 `mouse` 事件来实现，如下：
```js
element.on('mousedown', function(event) {
  // Prevent default dragging of selected content
  event.preventDefault();
  startX = event.pageX - x;
  startY = event.pageY - y;
  $document.on('mousemove', mousemove);
  $document.on('mouseup', mouseup);
});
```
这里只展示了代码片段，详情请查看，[demo6 示例代码](https://github.com/chenfengyanyu/dragAndDrop/blob/master/demo3.js)

#### 五、细节问题处理
整个过程看起来相当顺利，其实还是有很多细节问题的，这里简单罗列一下：
第一，fix 定位的元素，拖拽会失效；
第二，容器存在包含关系，除非你要 `drag everywhere`，否则拖拽将失效；
第三，需要阻止浏览器的默认行为，譬如重定向；
第四，默认地，无法将数据/元素放置到其他元素中。如果需要设置允许放置，我们必须阻止对元素的默认处理方式，即通过调用 `ondragover` 事件的 `event.preventDefault()` 方法；
第五，移动兼容较差，慎重选择；

#### 六、drag 和 drop 事件
我们列举一下 `drag` 和 `drop` 事件，意思很明了，这里就不赘述了。
- drag
- dragstart
- dragend
- dragover
- dragenter
- dragleave
- drop

#### 七、关于 <a name="dataTransfer">DataTransfer</a>
我们在  [drag everywhere](#dragEverywhere)  例子中使用到了 `DataTransfer.setData()` ，再熟悉一下。
{% alert info %}
`DataTransfer.setData()` 方法用来设置拖放操作的 drag data 到指定的数据和类型。
{% endalert %}

- 如果给定类型的数据不存在，则将其添加到拖动数据存储的末尾，使得 types 列表中的最后一个项目将是新类型。
- 如果给定类型的数据已经存在，现有数据将被替换为相同的位置。也就是说，替换相同类型的数据时 types列表的顺序不会更改。

示例数据类型为：`"text/plain"` 和 `"text/uri-list"`

语法：
```
void dataTransfer.setData(format, data);
```
参数：
- format: 一个DOMString 表示要添加到 drag object的拖动数据的类型。
- data: 一个 DOMString表示要添加到 drag object的数据。

更多请移步 [mozilla setData](https://developer.mozilla.org/zh-CN/docs/Web/API/DataTransfer/setData)

#### 八、Window.getComputedStyle()
除了 `DataTransfer.setData()` 这里我还要介绍一个特别有意思的属性，`Window.getComputedStyle()`，为什么这么说呢？先来看一下他的文档：
{% alert info %}
Window.getComputedStyle() 方法给出应用活动样式表后的元素的所有CSS属性的值，并解析这些值可能包含的任何基本计算。
{% endalert %}

语法：
```
let style = window.getComputedStyle(element, [pseudoElt]);
```
参数：
- element：用于获取计算样式的Element
- pseudoElt：可选，指定一个要匹配的伪元素的字符串。必须对普通元素省略（或null）。

强大之处就在于：它返回的样式是一个实时的 CSSStyleDeclaration 对象，当元素的样式更改时，它会自动更新本身。

至于兼容性，请看下图：
![getComputedStyle](http://7xvi3w.com1.z0.glb.clouddn.com/getComputedStyle.png-blog)

#### 九、getPropertyValue
{% alert info %}
The CSSStyleDeclaration.getPropertyValue() method interface returns a DOMString containing the value of a specified CSS property.
{% endalert %}
语法：
```
var value = style.getPropertyValue(property);
```
示例：
```js
var declaration = document.styleSheets[0].cssRules[0].style;
var value = declaration.getPropertyValue('margin'); // "1px 2px"
```

额外扩展了一个属性和两个方法，无非想提供更多的实现思路，从而使我们的开发更加高效。这不是考点，考试不会考哦～

#### 十、插件推荐
在自己尝试实现 `drag` 之前很长一段时间，我都在使用一些成熟插件，这里推荐几个：
1. [HTML5 拖拽排序](http://farhadi.ir/projects/html5sortable/)
2. [codepen](https://codepen.io/m-e-conroy/pen/gwbqG)
3. [angular-drop](http://caitp.github.io/angular-drop/)
...

类似的插件网上一搜一大堆，前端大军造轮子的能力还是很强的，这不，我们今天又造了一个🙈～

#### 十一、参考
1. [w3school](http://www.w3school.com.cn/html5/html_5_draganddrop.asp)  
2. [Implement html5 drag in angularjs](https://stackoverflow.com/questions/42454576/implement-html5-drag-drop-in-angularjs)  
3. [Native drag and drop in AngularJS](https://parkji.co.uk/2013/08/11/native-drag-and-drop-in-angularjs.html)  
4. [move anywhere](https://stackoverflow.com/questions/7278409/html5-drag-and-drop-to-move-a-div-anywhere-on-the-screen)
5. [mozilla getPropertyValue](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleDeclaration/getPropertyValue)    
6. [mozilla getComputedStyle](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/getComputedStyle)     