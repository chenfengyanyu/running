---
title: Angularjs学习笔记04~过滤器 Filter
date: 2014-04-14 15:31:15
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
最近越来越懒了，说好的每天抽一点时间来总结笔记，可是却完全没有执行，后续还有好多笔记，想想都觉得头疼。之前已经把目录整理好了，回头发现，目录是全了，可是之前学习的笔记太过于零散，无法形成一套完整的笔记。
<!-- more -->
所以，边写博客边总结吧，也算是对之前的学习来一次大回顾。哎，看书虽易，理解不易，且学且总结吧！不瞎扯了，下面直接进入正题。

这节笔记主要是来总结angularjs中过滤器（filter）是如何使用的。其实，之前的笔记中已经有filter的使用示例，但是并没有作为重点。这里我又把问题抛出来，给个痛快的解决（参考了不少资料，应该是很全面了，至少我自己这么觉得O(∩_∩)O）。

#### 一、过滤器
AngularJS过滤器用来格式化输出数据。过滤器不但能格式化数据，还能修改DOM。过滤器可用在任何api或者ng.$rootScoe.Scope的执行过程中，不过一般用来格式化绑定在模板中的表达式。
这是一种最常见的模式，也是最简单的，直接用管道符连接起来就行。
```js
{{ expression | filter }}
```
过滤器一般在处理过程中将数据转变成新的格式。它能使用链式风格，还能接受附加参数。我们可以这样使用链式风格：
```js
{{ expression | filter1 | filter2 }}
```
当然也可以使用“:”来传递额外的参数给过滤器，比如，将数字123格式化成带有2位小数的形式：
```
123 | number:2
```

#### 二、过滤器的两种形式
上面大概描述了过滤器的几种常见格式，可是如何去使用过滤器来格式化我们自己的数据呢？这里，我姑且先把过滤器分为两类吧。

第一种，内置过滤器：
也就是Angularjs内置的过滤器，例如currency、date、number、uppercase等。如你所想，内置过滤器用起来也相当简单，下面给出几个简单的实例，代码如下：
```html
<div ng-app>
    <div > 12.9 用过滤器currency结果为： {{12.9|currency }} </div >
    <div > 1396606957717 用过滤器date结果为： {{1396606957717|date:"yyyy-MM-dd hh:mm:ss"}} </ div>
    <div > 12.9 用过滤器number结果为： {{12.9| number:0 }} </div >
    <div > {name="wang"} 用过滤器json结果为： {{{name:"wang"}| json}}</div>
    <div>wang 用过滤器uppercase结果为：{{"wang"| uppercase}}</div>
    <div>WANG 用过滤器lowercase结果为：{{"WANG"| lowercase}}</div>
</div>
```
效果如下：
![angularjs](http://7xvi3w.com1.z0.glb.clouddn.com/blog_CB60244D-79F6-4B3C-A341-D7E2E24B5382.png)

{% alert info %}
演示地址：http://jsfiddle.net/Au79V/13/
{% endalert %}

第二种，自定义过滤器：
当然，多数情况下angularjs的内置过滤器就可以满足我们的使用了。但是你需要定制或者一些特别的过滤器，那么也不用担心，我们可以写自己的过滤器。哈哈，规则完全由你定，听起来是不是感觉很过瘾。下面我引用一下开发指南中的自定义过滤器，来说明问题，首先来看看代码：
index.html:
```html
<! doctype html >
< html ng-app ="MyReverseModule">
< head>
    <script src ="http://code.angularjs.org/angular-1.0.2.min.js"></ script>
    <script src="script.js"></ script >
</ head>
< body>
    <div ng-controller="Ctrl">
        < input ng-model ="greeting" type ="greeting"><br >
        No filter: {{greeting }} <br >
        Reverse: {{greeting|reverse }} <br >
        Reverse + uppercase: {{greeting|reverse:true }} <br >
    </div >
</ body>
</ html>
```

script.js:
```js
angular.module( 'MyReverseModule', []).
    filter( 'reverse', function () {
        return function (input, uppercase) {
            var out = "" ;
            for (var i = 0; i < input.length; i++) {
                out = input.charAt(i) + out;
            }
            // conditional based on optional argument
            if (uppercase) {
                out = out.toUpperCase();
            }
            return out;
        }
    });
function Ctrl($scope) {
    $scope.greeting = 'hello' ;
}
```
效果如下：
![angularjs](http://7xvi3w.com1.z0.glb.clouddn.com/blog_5DFA9C34-BCE6-4588-8A0B-D257CD4F8D86.png)

{% alert info %}
演示地址：http://jsfiddle.net/Au79V/14/
{% endalert %}

说明：这里实现了一个将字符串逆向输出的过滤器reverse，要扩展filter，首先我们需要在module中创建它，就像这样

```js
angular.module('moduleName',[]).filter('filterName',function(){这里是你扩展的方法});
```
这样我们就定义好自己的filter了，下面我来像这样调用它
```
{{"字符串"|reverse}}。
```
通过两步就轻松的扩展了一个自定义过滤器，是不是很简单呢？

到这里，过滤器的简单用法已经阐述完了。当然，复杂的使用场景这里我就不说了（关键是我也不懂）。网上有很多大牛，晋级的话，他们是最好的老师！

参考文章：
《Angularjs 开发指南》
《用Angularjs开发下一代web应用》








