---
title: Angularjs学习笔记01~作用域Scope
date: 2014-03-28 22:06:01
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
要学习angularjs，首先我们要了解它的作用域，也就是angularjs中的scope。
<!-- more -->
#### 一、angulajs简介：
AngularJS是纯客户端技术，完全用Javascript编写的。它使用的是网页开发的常规技术(HTML,CSS,Javascript)，目的是让网页应用开发更快更容易。AngularJS简化应用开发的一个重要方法是，将一个些通用的低级开发操作包装起来提供给开发者。AngularJS会自动处理好这些低级操作。它们包括：
- DOM操作
- 设置好事件的监听和通知
- 输入验证
- 因为AngularJS会处理大部分这些操作，所以开发者就能更多的专注在应用逻辑上，更少地编写那些重
- 复性的、易错的、低级的代码。
- 在AngularJS简化开发的同时，它也为客户端带来了一些精巧的技术，它们包括：
- 数据、逻辑、视图的分离
- 数据和视图间的自动绑定
- 通用服务(用可替换的对象实现的通用的应用操作)
- 依赖注入（主要用于聚合服务）
- 可扩展的HTML编译器(完全由JavaScript编写)
- 易于测试
- 客户端对这些技术的需求其实已经存在很久了。

#### 二、先睹为快
先来一个小例子吧：
```html
<!doctype html>
<html ng-app>
    <head >
        <script src="http://code.angularjs.org/angular-1.0.1.min.js"></script>
    </head >
    <body >
        Your name: <input type="text" ng-model="yourname" placeholder="World">
        <hr>
        Hello {{yourname || 'World'}} !
    </body >
</html>
```
![angularjs](http://7xvi3w.com1.z0.glb.clouddn.com/blog_37AB7A5F-A7A7-4A74-A119-1FC04287883B.png)

演示地址：http://jsfiddle.net/Au79V/

说明：就这些代码，不用再做什么，你会发现，底下的文本“Hello World！”已经实现和input的输入而变化，这就是angularjs中的数据双向绑定。
双大括号作为angularjs中的绑定符号，ng-app告诉浏览器，在dom渲染完毕后，告之angularjs，让它来填充这些双大括号中的数据。而ng-model就是双向绑定的标签。

#### 三、作用域
作用域是一个指向应用模型的对象。它是表达式的执行环境。作用域有层次结构，这个层次和相应的DOM几乎是一样的。作用域能监控表达式和传递事件。

作用域的特点：

- 作用域提供APIs（$watch）来观察模型的变化。
- 作用域提供APIs($apply)将任何模型的改变从“AngularJS领域(Angularrealm)”通过系统映射到视图上。
- 作用域能通过共享模型成员的方式嵌套到应用组件上。一个作用域从父作用域继承属性。
- 作用域提供表达式执行的上下文。比如说表达式：
```
{{username}} 
```
本身是无意义的，除非把它放到指定username属性的作用域中。

index.html：
```html
<!doctype html>
<html ng-app>
    <head >
        <script src="http://code.angularjs.org/angular-1.0.2.min.js"></script>
        <script src="script.js"></script >
    </head >
    <body >
        <div ng-controller="MyController">
            Your name:
            <input type="text" ng-model="username">
            <button ng-click='sayHello()'>greet </button>
            <hr>
            {{greeting}}
        </div>
    </body >
</html>
```
script.js：
```javascript
function MyController($scope) {
    $scope.username = 'World';
    $scope.sayHello = function () {
        $scope.greeting = 'Hello ' + $scope.username + '!' ;
    };
}
```
上例中MyController将值World赋给了作用域中的username。然后作用域将这个赋值的操作通知给input，然后input就会被渲染成预填充了值得样子。这展示控制器如何将数据写入到作用域。

同样的，控制器能将行为添加到作用域，正如你看到的sayHello方法，这个方法是在用户点击“greet”按钮时被调用的。这展示了当属性绑定到HTMLinput元素上时，它会自动更新。

{% alert info %}
你可以把作用域和它的属性当做是用来渲染视图的数据。
作用域是视图唯一相关联的变化来源。
{% endalert %}


参考文章：
《Angularjs 开发指南》
《Angularjs 入门教程》








