---
title: Angularjs学习笔记08~指令 Directive（一）初探
date: 2014-04-28 10:34:24
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
喜欢angular很久了，可是什么原因让我对angular如此爱不释手呢。对，是指令，一个可以让你把元素、类名、属性，甚至于注释，变成自定义功能的html扩展方法。html完全由你控制，而不是之前的去遵守html预定义标签的约定。
<!-- more -->
{% alert success %}
与其说编程，不如说是创造。一种随心所欲的编程方式，一种满满的幸福感，一种程序员的追求。我想这是每个程序员都想去做的事，我们会去遵守一些约定，但是更多的是去创造，去提升，去突破！
{% endalert %}


说的有点玄乎了，关于指令，我打算分三部分去总结：
其一，指令 Directive（一）初探
其二，指令 Directive（二）参数定义
其三，指令 Directive（三）自定义验证实例

当然，今天我们先来初探一把，看看官方指令是如何定义、指令的伪代码模板，以及指令的意义。走起！

一、什么是指令
啰嗦了一大堆，无非是自己一些粗浅的理解。我们来看看《用Angularjs开发下一代web应用》中是如何来解释指令的：

> angular最强大的功能之一就是，你可以把模板编写成HTML的形式。之所以可以做到这一点，是因为angular引入了一款强大的DOM转换引擎，可以用它来扩展html的语法。

angular中已经添加了一些新的属性，这些属性不属于html规范。例如，
- 我们引入了双花括号用来实现数据绑定；
- 引入了ng-controller用来指定每个控制器负责监视视图中的哪一部分；
- 引入了ng-model，用来把输入数据绑定到模型中的一部分属性上；

我们把这些叫做html扩展指令。

#### 二、创建指令的基本伪代码模板：
```js
var myModule = angular.module("myModule" , []);
myModule.directive('namespaceDirectiveName', function factory(injectables) {
    var directiveDefinitionObject = {
        restrict: string,
        priority: number,
        template: string,
        templateUrl: string,
        replace: bool,
        transclude: bool,
        scope: {},
        controller: function controllerConstructor(
            $scope,
            $element,
            $attrs,
            $transclude
        ) { },
        require: string,
        link: function postLink(scope, iElement, iAttrs) { },
        compile: function compile(tElement, tAttrs, transclude) {
            return {
                pre: function preLink(scope, iElement, iAttrs, controller) { },
                post: function postLink(scope, iElement, iAttrs, controller) { }
            }
        }
    };
    return directiveDefinitionObject;
});
```

#### 三、有何意义？
{% alert info %}
对于一些新的事物，我们总会关心两个问题：其一，它能做什么；其二，它有什么意义？相信上面已经很清楚的回答了第一个问题，下面我们来看看angular指令的意义。
{% endalert %}


angular内置了很多指令，可以帮助你为你的应用定义视图。这些指令可以把常见的视图定义成模板，它们可以设置应用的运行方式，或者用来创建可复用的组件。同时，你并不会受限于angular内置的指令。你可以编写自己的指令用来扩展html模板的功能，从而实现你梦想中的任何东西。

参考书籍：
《用AngularJs开发下一代Web应用》
《AngularJs开发指南》











