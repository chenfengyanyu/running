---
title: Angularjs学习笔记02~控制器Controller
date: 2014-03-29 23:26:33
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
在AngularJS中，控制器是一个Javascript函数（类型/类），用来增强除了根作用域以外的作用域实例的。当你或者AngularJS本身通过scope.$new俩创建一个新的子作用域对象时，有一个选项能让你将它当做参数传递给控制器。这能使AngularjS将控制器和这个作用域联系起来，增强作用域的行为。
<!-- more -->
#### 一、控制器用于？
①设置好作用域对象的初始状态；
原文是这样描述的:“AngularJS将对作用域对象调用控制器的构造函数，以此来设置作用域的初始状态”，其实也很好理解，我们在controller中像这样初始化一个变量：$scope.title="标题";
在视图中就可以用双括号语法{{title}}或者ng-bind=“title”来显示controller中初始化的值。

②给作用域对象增加行为；
AngularJS作用域对象的行为是由作用域的方法来表示的。这些方法是可以在模板或者说视图中调用的。这些方法和应用模型交互，并且能改变模型。也就是说在controller中定义一些方法，他们可以通过ng-click、ng-change等操作来触发。

#### 二、控制器如何正确使用？

- 一个控制器不应该做太多工作。
- 它应该只包含单个视图的业务逻辑。
- 保持控制器职责单一的最常见做法是将那些不属于控制器的工作抽离到服务中，然后通过依赖注入在控制器中使用这些服务。


controller的两种使用方式：

①以方法形式：
```html
<div  ng-controller="myCtrl">
    <button ng-click="changeText()"> {{text}}</button >
</div>
```

```javascript
function myCtrl($scope) {
    $scope.text = '未点击';
    $scope.changeText = function () {
        $scope.text = '已点击';
    }
}
```

> 说明：上面这种方式展示了用方法来调用controller，当点击该按钮时，上面的“未点击”转化为“已点击”。可以看到myCtrl与正常的js方法十分类似，但是括号中多了一个$scope参数，这就是前面提到过的作用域对象。

这里还需要注意的是，视图层div中的ng-controller="myCtrl"千万不要忘记写了，否则，作用域中的text是无法解析出来的。

②module形式：
```html
<div ng-app="myApp" ng-controller="myCtrl">
    <button ng-click="changeText()"> {{text}}</button >
</div>
```

```javascript
var myApp = angular.module("myApp" , []).
controller("myCtrl", function ($scope) {
    $scope.text = '未点击';
    $scope.changeText = function () {
        $scope.text = '已点击';
    }
});
```

说明：这里将controller放在了module中，代码与上面大同小异，注意视图中div中添加的ng-app="myApp"，其实就是angularjs的操作边界。

#### 三、演示地址：
http://jsfiddle.net/Au79V/
{% alert info %}
说明：例子比较简单，这里只是为了展示controller的使用形式，高手可以直接跳过~
{% endalert %}
参考文章：
《Angularjs 开发指南》














