---
title: Angularjs学习笔记03~数据绑定 Data-Binding
date: 2014-04-01 09:34:17
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
这节我们主要讨论一下数据绑定，了解angularjs的双向数据绑定模型比传统的单向数据绑定优越在哪里？
<!-- more -->
#### 一、AngularJS中的数据绑定
先来说说什么是数据绑定吧！开发指南中是这样给出定义的:
> "AngularJS中的数据绑定就是模型与视图间的自动同步。这种实现方式让你能专心地处理你的模型。视图总是模型的投影。当模型改变，视图就会反映这种改变，反之亦然。"

那么，顾名思义双向绑定就是视图随模型的改变而改变，模型的变化也会使视图发生改变。如果一味的强调双向数据绑定，可能并不能准确的表述双向绑定的优越性，下面两幅图将“传统的数据绑定”即单向数据绑定与angularjs的“双向数据绑定”进行对比说明，这样我们就能很好的理解所谓的双向绑定了。这里的图也是摘自开发指南O(∩_∩)O

①传统的模板系统数据绑定：
就像下面这幅图所展示的，它们将Model和Template结合生成视图。这个结合过程产生的视图不是动态更新的。更糟的是，任何用户和视图的交互都不会反映到模型。而我们要做的就是写视图和模型双向的同步代码，这也就是我们通常用ajax来实现的局部动态刷新。

![databinding](http://7xvi3w.com1.z0.glb.clouddn.com/blog_angular_oneway.png)

②angularjs模板中数据绑定：
AngularJS模板的工作原理与传统模板单向数据绑定有以下两点不同：
第一，模板（附加了自定义属性等标记的未经编译的HTML）是由浏览器编译的；
第二，编译最后产生的是一个动态的视图。

为什么是动态视图呢？这里动态指的是视图的任何变化都会直接反应到模型，反之亦然。这使得模型总是应用状态的唯一标识，这大大简化了开发人员的编程工作。因为视图只是模型的投影，控制器完全和视图分离了，并且视图对它来说是透明的。这使得测试变得更简单，因为你不需要关心相关的DOM或者浏览器变化了。

![databinding](http://7xvi3w.com1.z0.glb.clouddn.com/blog_angular_twoway.png)

《用Angularjs开发下一代web应用》中是这样描述数据绑定的：
> 在jQuery中，我们会把HTML模板字符串和数据混合起来，然后把获得的结果插入Dom中我们所期望的位置，插入的方式是吧结果设置给一个占位符元素的innerHTML属性。

以上机制工作的相当不错，但是当你想要把最新的数据插入到UI中，或者根据用户输入来修改数据的时候，你就需要做很多及其繁琐的工作来保证数据的状态是正确的，并且UI和javascript属性要同时正确。

如果我们可以仅仅声明UI中的某个部分需要映射到某个javascript属性，然后让它们去同步，这种编程风格叫做数据绑定。

> 说明：不难看出，angularjs所做的双向数据绑定为我们确实带来了很大的方便，我们只需要去做一些逻辑处理，剩下的动态更新就全部交给angularjs来完成，这使得开发人员可以从繁重的更新dom环节中解脱出来。这样，提升工作效率的同时，代码量不知不觉也减少了，那我们何乐而不为呢？

#### 二、代码演示
语言永远都是最苍白无力的，既然这样，这里我就用实例来进行说明，先来一个简单的输入框双向绑定：
```html
<div ng-app="myApp" ng-controller="myCtrl"> 请输入姓名：
    <input type="text" ng-model="name" />
    </br >您好,{{name}}
</div>
```

```js
var myApp = angular.module("myApp" , []).
controller("myCtrl", function ($scope) {
    $scope.name = "wang";
});
```

说明：controller中对name进行了初始化，视图层中用ng-model来对name进行绑定，当input内容被改变，{{name}}中的值也跟随改变，这就是我们所说的数据双向绑定。

![angularjs](http://7xvi3w.com1.z0.glb.clouddn.com/blog_angular1.png)

{% alert info %}
演示地址：http://jsfiddle.net/Au79V/
{% endalert %}

《Angularjs入门教程》中用下拉列表对数据进行排序，这里我也借用这一方式，模拟双向数据绑定效果：
```html
<div ng-app="myApp" ng-controller="myCtrl">
    通过 {{orderBy }}排序:
    <select ng-model="orderBy">
        <option value="name">姓名 </option>
        <option value="age">年龄 </option>
    </select >
    <ul >
        <li ng-repeat="x in users |orderBy:orderBy"> 姓名：{{ x.name}}
            <p> 年龄：{{ x.age}}</p>
        </li>
    </ul >
</div>
```
```js
var myApp = angular.module("myApp" , []).
controller("myCtrl", function ($scope) {
    $scope.users = [{
        "name": "c张三" ,
        "age": 0
    }, {
        "name": "a李四" ,
        "age": 1
    }, {
        "name": "b王五" ,
        "age": 2
    }];

    $scope.orderBy = 'age';
});
```
效果如下：
![databinding](http://7xvi3w.com1.z0.glb.clouddn.com/blog_angular_sort.png)

{% alert info %}
演示地址：http://jsfiddle.net/Au79V/
{% endalert %}

说明：这个例子实现了通过调整下拉框的年龄和姓名，对列表进行排序，很明显这里也是双向数据绑定。如果你不了解ng-repeat，不用在意，后续我还会具体进行说明，这里你需要理解的是我们在controller中模拟了一个user对象，并初始化了三条假数据，然后在视图层展示出来。

参考文章：
《Angularjs 开发指南》
《Angularjs入门教程-双向绑定》：http://www.ituring.com.cn/article/15764#
《用Angularjs开发下一代web应用》







