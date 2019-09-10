---
title: Angularjs学习笔记05~模板 Template
date: 2014-04-20 11:01:56
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
模板作为单独一页笔记其实有些牵强，这个名字可能还会引入歧义。因为它作为指令中的一部分，同时在路由中也有相关的引用。纠结了半天，不过我还是提出来了，这里我不打算讲模板与其他服务、路由或者指令的联系，只从简单使用方面入手。换个角度来剖析模板，有个整体概念，一定会对后续深入学习有较大的帮助。
<!-- more -->
当然，我也是新手，可能对angularjs理解不够深入，如果哪里描述的有问题，我希望作为读者的你能够提出来，鄙人不胜感激。互联网方面的知识，贵在分享，大家共同进步才是真正的进步嘛。

#### 一、什么是模板？
关于模板，API中是这样给出定义的：
> In Angular, templates are written with HTML that contains Angular-specific elements and attributes. Angular combines the template with information from the model and controller to render the dynamic view that a user sees in the browser.

用我弱弱的英语翻译如下：在Angular中，用HTML写的模板包含了angular特殊元素和属性。angular把model和controller中的信息与模板结合起来，目的是去呈现用户在浏览器中看到的动态视图。

它是静态的DOM，包含HTML，CSS和AngularJS指定的元素和属性。AngularJS元素和属性让angular给模板DOM添加行为，或者变形，成为动态地DOM。

需要注意的是，我这里所说的模板可能与路由中的partials模板不是一个概念，这里特指html代码片段。下面我打算通过实例代码进行分析，这样应该会很容易理解。

#### 二、一个简单的实例
自定义一个文本闪烁my-Blink属性标签。其实在angular中，可以定义元素标签、注释标签、属性标签还有样式标签。为了少引入一些指令的相关信息，这里我就不做具体的说明了，后续肯定会详细说明。先来实现简单的效果，引入标签，实现字体闪烁的效果，代码如下：

html：
```html
<div ng-app="myApp" ng-controller="myCtrl">
   <div data-my-blink>ddd</ div>
   <div my-blink></ div>
</div>
```
js：
```js
var myApp = angular.module("myApp" , []);
myApp.directive('myBlink', function () {
    return {
        template: '<marquee scrollamount="100%">翻滚吧，牛宝宝！</marquee>'
    };
});
```
效果如下：
![angularjs](http://7xvi3w.com1.z0.glb.clouddn.com/blog_3358443B-A62E-4A86-9734-E10032A15E3D.png)

{% alert info %}
演示地址：http://jsfiddle.net/Au79V/15/
{% endalert %}


说明：代码很简单，我在module中定义了一个叫myBlink的指令，指令中的template指定了一段HTML代码。所以当我在页面中使用my-blink或者data-my-blink属性时，所在元素就会被js中的模板所替换，闪烁效果也就出来了。

细心的你可能发现了，这里
```html
<div data-my-blink>ddd</ div>
```
被模板直接替换掉了，我们想显示的ddd去哪里了？有时候模板这种整体的替换，并不是我们想要的效果，所以现在你需要去让ddd显示出来，很简单，我们做如下修改：
html：
```html
<div ng-app="myApp" ng-controller="myCtrl">
   <div data-my-blink> ddd</div >
   <div my-blink> 翻滚吧，牛宝宝！ </div>
</div>
```
js：
```js
var myApp = angular.module("myApp" , []);
myApp.directive('myBlink', function () {
    return {
        transclude: true ,
        template: '<marquee scrollamount="100%" ng-transclude></marquee>'
    };
});
```
效果如下：
![angularjs](http://7xvi3w.com1.z0.glb.clouddn.com/blog_A2F6585E-58C1-4189-AC93-945338CF70AF.png)
{% alert info %}
演示地址：http://jsfiddle.net/Au79V/16/
{% endalert %}

说明：这里你需要注意js中我新加的transclude:true属性和template中的ng-transclude属性，意思就是：“把原来元素中的内容移动到我们所提供的模板中”。建议先看看实例效果，理解起来就比较容易了。

参考文章：
书籍：《Angularjs 开发指南》、《用Angularjs开发下一代web应用》
Angularjs 文档：http://docs.angularjs.org/guide/templates
Angularjs指令：http://www.adobe.com/cn/devnet/html5/articles/angularjs-directives-and-the-computer-science-of-javascript.html






