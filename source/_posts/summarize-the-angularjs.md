---
title: Angularjs知识分享
date: 2015-06-29 17:11:23
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
在这个信息快速更替的时代，昨天还被人捧上了天，今天就会被某些人贬的一文不值。跟技术挂钩的话，你可能猜到我要说的就是Angularjs，对，一个我至今仍然非常喜欢的框架。
<!-- more -->
当然很多人喜欢把他和Reactjs做比较，我只能呵呵了，不同的东西，应用于不同的场景，物尽其用，人尽其能罢了。为啥要比个你死我活，一定要包容万物嘛，此处省略俩字。

言归正传，此时发表这篇文章貌似有点过气了，谁说不是呢。要不是那个挨千刀的点点博客网崩盘了，我的博客也不至于沦落至此。哎，雄关漫道真如铁，而今迈步从头越，从头越～

下面开始进入正题，纪念我那一次好几十人的前端培训。

#### 一、AngularJS是什么
AngularJS主要用于构建单页Web应用。它通过增加开发人员和常见Web应用开发任务之间的抽象级别，使构建交互式的现代Web应用变得更加简单。

![Angularjs读法](http://7xvi3w.com1.z0.glb.clouddn.com/blog_793F7E26-4F0B-48F5-A6ED-4DDE8D95CF07.png)

在其他JavaScript框架中，我们被迫从自定义的JavaScript对象中进行扩展，并从外到内操作DOM。以jQuery为例，为了在DOM中插入一个按钮元素，我们必须知道要把元素放到何处，并在合适的位置插入它。

尽管这个过程并不复杂，但是它要求开发者对整个DOM结构都有所了解，并强迫我们在JavaScript代码中加入复杂的控制逻辑，用以操作外部DOM。

而AngularJS则通过原生的Model-View-Controller（MVC，模型-视图-控制器）功能增强了HTML。结果表明，这个选择可以快捷和愉悦的构建出令人印象深刻并且极富表现力的客户端应用。

#### 二、示例对比（高效，简洁，重构）
![Angularjs动态图片展示](http://7xvi3w.com1.z0.glb.clouddn.com/blog_1.gif)
传统方式：
http://jsfiddle.net/Au79V/36/
```html
<div>
Your name:
<input type="text"  placeholder="World">
<hr>
Hello <span id="changeVal">World</span> !
</div>
```

```javascript
var input = $('input'),
    span = $('#changeVal');
input.on('keyup',function(){
  span.text(input.val() ? input.val() : 'World');
});
```

AngularJS方式：
http://jsfiddle.net/Au79V/34/

```html
<div ng-app>
Your name:
<input type="text" ng-model="yourname" placeholder="World">
<hr>
Hello {{yourname || 'World'}} !
</div>
```

#### 三、基本原理
> Scope作用域：AngularJS核心基础，是一个指向应用模型的对象。它是表达式的执行环境。作用域有层次结构，这个层次和相应的DOM几乎是一样的。作用域能监控表达式和传递事件。
在应用将视图渲染并呈献给用户之前，视图中的模板会和作用域进行连接，然后应用会对DOM进行设置以便将属性变化通知给AngularJS。

作用域的特点：
- 作用域提供APIs（$watch）来观察模型的变化。
- 作用域提供APIs($apply)将任何模型的改变从"AngularJS领域(Angularrealm)"通过系统映射到视图上。
- 作用域能通过共享模型成员的方式嵌套到应用组件上。一个作用域从父作用域继承属性。
- 作用域提供表达式执行的上下文。比如说表达式 {{username}} 本身是无意义的，除非把它放到指定username属性的作用域中。

![作用域](http://7xvi3w.com1.z0.glb.clouddn.com/blgo_0C20BA8C-39FD-41B4-9131-9BC276F10420.png)
> 双向绑定：AngularJS通过创建实时模板来代替视图，而不是将数据合并进模板之后更新DOM。任何一个独立视图组件中的值都是动态替换的。

①传统的模板系统数据绑定：
就像下面这幅图所展示的，它们将Model和Template结合生成视图。这个结合过程产生的视图不是动态更新的。更糟的是，任何用户和视图的交互都不会反映到模型。而我们要做的就是写视图和模型双向的同步代码，这也就是我们通常用ajax来实现的局部动态刷新。

![单向数据绑定](http://7xvi3w.com1.z0.glb.clouddn.com/blgo_F12A4C16-5B28-4A54-8B61-817F52128657.png)
②angularjs模板中数据绑定：
AngularJS模板的工作原理与传统模板单向数据绑定有以下两点不同：
第一，模板（附加了自定义属性等标记的未经编译的HTML）是由浏览器编译的；
第二，编译最后产生的是一个动态的视图。

为什么是动态视图呢？这里动态指的是视图的任何变化都会直接反应到模型，反之亦然。这使得模型总是应用状态的唯一标识，这大大简化了开发人员的编程工作。因为视图只是模型的投影，控制器完全和视图分离了，并且视图对它来说是透明的。这使得测试变得更简单，因为你不需要关心相关的DOM或者浏览器变化了。

![双向数据绑定](http://7xvi3w.com1.z0.glb.clouddn.com/blgo_A0D5DB52-36D7-48EF-86A5-AC9FDE292AC6.png)

> 脏值检测：AngularJS会记录数据模型所包含的数据在任何特定时间点的值，而不是原始值。当AngularJS认为某个值可能发生变化时，它会运行自己的事件循环来检查这个值是否变“脏”。如果该值从上次事件循环运行之后发生了变化，则该值被认为是“脏”值。这也是AngularJS可以跟踪和响应应用变化的方式。

这个事件循环会调用$digest( ) 循环。

这个过程被称为脏值检测（dirty checking），游戏开发中大量使用该技术。脏值检测是检查数据模型变化的有效手段。当有潜在的变化存在时，AngularJS会在事件循环时执行脏检测来保证数据的一致性。

如果使用KnockoutJS这种通过在数据模型上绑定事件监听器来监听数据变化的框架，这个过程会变得更复杂且低效。处理事件合并、依赖跟踪和大量的事件触发是非常复杂的，而且会在性能方面导致额外的问题。

> 依赖注入：依赖注入，也就是Dependency injection，是一种软件设计模式，用来处理代码的依赖关系。
开发指南中提到，用三种方法让函数获得它的需要依赖。

其一，它的依赖是能被创建的，一般用new操作符就行。
其二，能够通过全局变量查找依赖。
其三，依赖能在需要时被导入。

前两种方式都不是很好，因为它们需要对依赖硬编码，使得修改依赖的时候变得困难。特别是在测试的时候不好办，因为对某个部分进行孤立的测试常常需要模拟它的依赖。第三种方式是最好的，因为它不必在组件中去主动需找和获取依赖，而是由外界将依赖传入。

```javascript
function HelloController($scope,$location){
    $scope.greeting={text:'Hello'};
    //这里可以使用$location对象来做一些很酷的事情
}
```

这里我们想把数据绑定到用户浏览器中的URL地址上，可以使用$location对象，只要把$location对象放到我们的构造函数中就可以了。
这种神奇效果是通过angularjs的依赖注入机制实现的。依赖注入让我们遵守这样一种开发风格：
我们的类只是简单的获取它们所需要的东西，而不需要创建那些它们所依赖的东西。

> 路由：AngularJS中应用的路由通过$routeProvider来声明，它是$route服务的提供者。这项服务使得控制器、视图模板与当前浏览器的URL可以轻易集成。应用这个特性我们就可以实现深链接，它允许我们使用浏览器的历史(回退或者前进导航)和书签。

app.js:
```javascript
var myApp= angular.module('myApp', [ ]);
myApp.config(function($routeProvider) {

$routeProvider.
when('url',
{

controller:aController,

templateUrl:'/path/to/tempate'

}).
when(...other mappings for your app...).
…
otherwise(...what to do if nothing else matches...);

)};
```

html:
```html
<body ng-app="myApp">
    <div ng-view></div>
</body>
```

> 指令：Angular最强大的功能之一就是，你可以把模板编写成HTML的形式。之所以可以做到这一点，是因为angular引入了一款强大的DOM转换引擎，可以用它来扩展html的语法。
angular中已经添加了一些新的属性，这些属性不属于html规范。例如，
我们引入了双花括号用来实现数据绑定；
引入了ng-controller用来指定每个控制器负责监视视图中的哪一部分；
引入了ng-model，用来把输入数据绑定到模型中的一部分属性上；
我们把这些叫做html扩展指令。

```javascript
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
                                                                               $transclude) { },
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
angular内置了很多指令，可以帮助你为你的应用定义视图。这些指令可以把常见的视图定义成模板，它们可以设置应用的运行方式，或者用来创建可复用的组件。同时，你并不会受限于angular内置的指令。你可以编写自己的指令用来扩展html模板的功能，从而实现你梦想中的任何东西。

> 服务：在一个应用中，负责提供一个接口，把特定函数需要的方法放在一起。angular开发指南（http://www.angularjs.cn/A00L）中是这么说的，“服务是一种由服务器端带到客户端的特性，它由来已久。AngularJS应用中的服务是一些用依赖注入捆绑在一起的可替换的对象。服务是最常和依赖注入一起用的，它也是AngularJS中的关键特性”。

AngularJS 提供了一组常用操作的服务。和其他的内建变量或者标识符一样，内建服务名称也总是以"$"开头。另外你也可以创建你自己的服务，我会在下节笔记中详细介绍。这里我们来聚焦内建服务，首先我们来看看angularjs中有哪些常用的内建服务以及他们的含义。

①$http：angular的核心服务，通过浏览器的XMLHttpRequest对象或者JSONP，为远程HTTP服务器的通信提供便利。
②$location：对地址栏中的URL的一系列操作
③$log：安全的将信息写入浏览器控制台，也就是控制台输出
④$timeout：angular包装了window.setTimeout，我更习惯叫它定时器
⑤$q：异步请求使用，promise/deferred模板
⑥$routeProvider：路由

一个简单的例子可能如下所示：
```javascript
angular.module('myServiceModule', []).
 controller('MyController', ['$scope','notify', function ($scope, notify) {
   $scope.callNotify = function(msg) {
     notify(msg);
   };
 }]).
factory('notify', ['$window', function(win) {//定义服务名称
   var msgs = [];
   return function(msg) {//定义方法
     msgs.push(msg);
     if (msgs.length == 3) {
       win.alert(msgs.join("\n"));
       msgs = [];
     }
   };
 }]);
 ```

#### 四、绑定示例，演示
示例一：
http://jsfiddle.net/Au79V/7/
![Angularjs动态图片展示](http://7xvi3w.com1.z0.glb.clouddn.com/blog_1.gif)

示例二：
http://jsfiddle.net/Au79V/12/
![过滤器演示](http://7xvi3w.com1.z0.glb.clouddn.com/blog_65919517-DA4D-4CA3-A7DC-51FE54145C54.png)

示例三：
http://jsfiddle.net/Au79V/16/
![指令](http://7xvi3w.com1.z0.glb.clouddn.com/blog_F938A44E-AAA0-4827-88AC-DDA5288725ED.png)

示例四：
表单验证

示例五：
完整的CRUD

#### 五、优缺点一览
- 优点：
①模板功能强大丰富，并且是声明式的，自带了丰富的Angular指令；
②是一个比较完善的前端MV*框架，包含模板，数据双向绑定，路由，模块化，服务，依赖注入等所有功能；
③自定义Directive，需要深入了解Directive的一些特性，简单的封装比较容易。
④ ng模块的依赖注入，能够很容易的写出可复用的代码，实际开发中UI变化很大，但是js的代码基本上很少改动。
⑤ Angular支持单元测试和端到端测试。

- 缺点：
①适合处理简单的CRUD，复杂的应用场景将会很难下手；
②内容网站，需要SEO的。angularjs不利于此类网站；
③交互频繁的网站，angularjs效率将会大大降低，详情请查看angularjs事件循环；
④兼容的话，1.3版本已经放弃IE8了。

#### 六、相关资源
Angularjs在线文档：http://docs.angularjs.org/guide/services
angularjs入门教程：http://www.ituring.com.cn/article/13472
中文版开发指南：http://angular.duapp.com/guide/controller
angularjs中文社区：http://www.angularjs.cn/
angularjs焦点图特效：http://dfsq.github.io/ngView-animation-effects/app/#/page/1
angular ui：http://angular-ui.github.io/bootstrap/
angularjs 最强资源整合：http://www.iteye.com/news/28651-AngularJS-Google-resource
满满的全是angular：https://github.com/jmcunningham/AngularJS-Learning
angular树插件：http://jsfiddle.net/vL4TJ/44/
angular文本可编辑插件：http://vitalets.github.io/angular-xeditable/
angular另一套ui框架：http://mgcrea.github.io/angular-strap/

















