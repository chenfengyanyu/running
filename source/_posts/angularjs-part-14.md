---
title: Angularjs学习笔记13~路由 Route
date: 2014-05-11 16:47:17
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
最近在用ionic和angular构建一款web app，学习demo的同时，发现了很多路由的案例。由于自身基础的不扎实，导致开发过程中困难重重，简直是一步一个坎。索性暂时放下项目，抽些时间回过头来深入而又系统的整理一下angular路由相关知识。瞬间感觉自己从战斗力不到5的渣渣，成长到了战斗力破10的小渣。
<!-- more -->
哈哈，开个玩笑，下面进入正题吧，这节我们来说说angular中的路由，也就是route。鄙人功力尚浅，咱们说道说道基础就行了，说深了岂不是给自己挖坑（咳咳~）

#### 一、浅谈路由
> AngularJS中应用的路由通过$routeProvider来声明，它是$route服务的提供者。这项服务使得控制器、视图模板与当前浏览器的URL可以轻易集成。应用这个特性我们就可以实现深链接，它允许我们使用浏览器的历史(回退或者前进导航)和书签。

虽然从技术上来说 AJAX 应用确实是单页面应用（即在第一个请求时加载一个 HTML 页面，后面只刷新 DOM 中的局部区域），但是很多时候，出于各种原因，我们需要为用户展示或者隐藏一些子页面视图。

我们可以利用 Angular 的 $route 服务来管理这种场景。可以使用路由服务这样定义 ：对于浏览器所指向的特定 URL，Angular 将会加载并显示一个模板，并实例化一个控制器来为模板提供内容。

#### 二、伪代码剖析
简单来说，在一个实际应用中，我们一般通过调用 $routeProvider 服务上的函数来创建路由，然后把需要创建的路由当成一个配置块传给这些函数。下面用伪代码来阐释一下路由的创建过程 ：
app.js:
```js
var myApp= angular.module('myApp', ['ngRoute']);
myApp.config(function($routeProvider) {
$routeProvider.
when('url',
{
controller:aController,
templateUrl:'/path/to/tempate'
}).
when(...other mappings for your app...).
…
otherwise({redirectTo:'/'});
)};
```
html:
```html
<body ng-app="myApp">
     <div ng-view></div>
</body>
```

{% alert info %}
伪代码其实很简单，我只描述出了路由的基本骨架,以及应用方式。不过，我觉得还是有必要做一些解释和一些基本注意事项。
{% endalert %}

①路由需要依赖$routeProvider服务，这个千万不要忘了；
②基本语法是：when..when..otherwise，当when中没有匹配到任何项的时候，路由跳到otherwise中；
③如果你决定用路由，记得引入angular-route.js文件；
④一旦应用了路由，html中就不要写ng-controller了，直接在when中定义即可。

在ionic中应用路由其实也是大同小异,就不过多解释了。我们来看看一些细微的差别(代码中粗体部分)：ui-route
```js
angular.module('myApp', ['ionic'])
.config(function($stateProvider, $urlRouterProvider) {
  $stateProvider
  .state('intro', {
    url: '/',
    templateUrl: 'intro.html',
    controller: 'IntroCtrl'
  })
  .state('main', {
    url: '/main',
    templateUrl: 'main.html',
    controller: 'MainCtrl'
  });

  $urlRouterProvider.otherwise("/");
})
```
#### 三、代码示例
用上面相关路由的语法，我写了一个简单的示例，下面我来逐步剖析：
①文件目录：
![route](http://7xvi3w.com1.z0.glb.clouddn.com/angular_87F621EC-D87A-48DE-A57F-736352B8F3CA.png)

{% alert info %}
说明：用路由的话，需要引入angular-route.js，上面说过，这里就不多说了。我把子页面放到了templates文件夹下，只是为了方便管理。index.html是默认打开页面，main.js是主js文件。剩下的angular.js和bootstrap.css意思很明了。
{% endalert %}

②index.html
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
     <link href="bootstrap.css" rel="stylesheet" type="text/css" />
     <script type='text/javascript' src='angular.js'></script>
     <script type='text/javascript' src='angular-route.js'></script>
     <script type='text/javascript' src='main.js'></script>
</head>
<body ng-app="myModule">
     <div ng-view></div>
</body>
</html>
```
{% alert info %}
说明：这是默认起始页，代码很简单。head中引入了css和js文件body中ng-view来加载子页面视图。需要注意的是这里并没有写ng-controller，那controller写哪里去呢？不要着急，后面我们慢慢说。
{% endalert %}

③main.js
```js
var myApp=angular.module('myModule',['ngRoute']);
myApp.config(function($routeProvider){
     $routeProvider.
     when('/page1',
     {
          controller:'page1Ctrl',
          templateUrl:'templates/page1.html'
     }).
     when('/page2',
     {
          controller:'page2Ctrl',
          templateUrl:'templates/page2.html'
     }).
     when('/home',
     {
          controller:'page1Ctrl',
          templateUrl:'templates/home.html'
     }).
     otherwise({redirectTo: '/home'});
})
.controller('page1Ctrl',function($scope){
     $scope.testValue="page1,page1Ctrl";
})
.controller('page2Ctrl',function($scope){
     $scope.testValue="page2,page2Ctrl";
});
```
{% alert info %}
说明：这里是核心js，我做了两件事情。其一，为子页面分配路由；其二，为子页面定义controller。注意代码中加粗部分，依赖注入项ngRoute不能忘，用controller:'page2Ctrl'来为子页面分配控制区域。到这里，基本就大功告成了，剩下的子页面，我们来做一些简单处理（仅仅是为了演示）。
{% endalert %}

④home.html，page1.html，page2.html
```html
//home.html
<a href="#/page1"  class="btn btn-default btn-lg">
  <span class="glyphicon glyphicon-star"></span> To the Page1
</a>
<a href="#/page2" class="btn btn-default btn-lg">
  <span class="glyphicon glyphicon-star"></span> To the Page2
</a>

//page1.html
<h1>{{testValue}}</h1>

//page2.html:
<h1>{{testValue}}</h1>
```
{% alert info %}
说明：home页面，我放置了两个按钮，用于导航。page1和page2页面存放了一个变量testValue，这里请不要混淆，因为他们是在不同的controller中，所以显示的值是不一样的。之所以这么写，当然还是为了做演示说明。
{% endalert %}

效果演示：
![route](http://7xvi3w.com1.z0.glb.clouddn.com/angular_111.gif)

参考文章：
[入门教程：路由与多视图](http://www.ituring.com.cn/article/15767)
《用AngularJs开发下一代Web应用》