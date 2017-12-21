---
title: Angularjs学习笔记12~服务 Service（二）创建服务
date: 2014-05-08 13:56:36
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
接着上篇说，现在我们已经可以熟练运用angular为我们提供的一些内置服务了。但是，人总是要突破创新的嘛，老是用“内建服务”多没意思啊。干脆我们来做一些更加刺激和有意义的事情，让我们的编程学习充满激情与挑战。
<!-- more -->
这节我们就来具体看看如何去创建服务。

#### 一、创建服务：
AngularJS有很多内置的服务，但是如果你要创建一个很棒的应用，你可能还是要写自己的服务。你可以通过在模块中注册一个服务工场函数，或者通过Modeul#factoryapi或者直接通过模块配置函数中的$provideapi来实现。

> 所有的服务都符合依赖注入的原则。它们用一个唯一的名字将自己注册进AngularJS的依赖注入系统（injector），并且声明需要提供给工场函数的依赖。它们的依赖在测试中可以是虚拟的，这使得它们能很好地被测试。

①注册服务：要注册服务，你首先要有一个包含该服务的模块。然后你就能通过模块的api或者使用模块配置函数中的$provide服务来注册你的服务了。下面的伪代码显示了这两种方法。

方式一：使用angular.Module api:
```js
var myModule = angular.module('myModule' , []);
myModule.factory('serviceId', function () {
    var shinyNewServiceInstance;
    //factory function body that constructs shinyNewServiceInstance
    return shinyNewServiceInstance;
});
```
方式二：使用$provide服务:
```js
angular.module('myModule', [], function ($provide) {
    $provide.factory( 'serviceId', function () {
        var shinyNewServiceInstance;
        //factory function body that constructs shinyNewServiceInstance
        return shinyNewServiceInstance;
    });
});
```

②设置依赖：服务不仅可以被依赖，还可以有自己的依赖。依赖可以在工场函数的参数中指定。参阅AngularJS的依赖注入系统，和使用依赖的数组表示法和$inject属性来让依赖表示精简化。

下面是一个很简单的服务的例子。这个服务依赖于$window服务（会被当成参数传递给工场函数），并且只是个函数。这个服务的任务是存储所有的通知；在第三个通知以后，服务会用window的alert来输出所有的通知。
```js
angular.module('myModule', [], function ($provide) {
    $provide.factory( 'notify', ['$window' , function (win) {
        var msgs = [];
        return function (msg) {
            msgs.push(msg);
            if (msgs.length == 3) {
                win.alert(msgs.join( "\n"));
                msgs = [];
            }
        };
    }]);
});
```
③实例化angularjs服务：所有服务都是延迟实例化的。这意味着所有的服务只有在需要时，或者被依赖时才会实例化。换句话说，AngularJS不会实例化服务，除非被要请求了或者被应用直接或间接依赖了。

④将服务注入到控制器：将服务用作控制器的依赖和将服务用作其他服务的依赖很类似。
```js
angular.module('MyServiceModule', [])
.factory('theService', function () {//创建theService服务
   return{
          test:function(){
               var arr=["wang","jia","tao"];
               return arr;
          }
});
//方式一：隐式注入
function myController(scope,theService) {
   theService.test();//输出"wang","jia","tao"
}
// 方式二：显式注入
myController.$inject = ['$scope', 'theService'];
```
{% alert info %}
注意：如你要压缩你的代码，你的变量名会被重命名，你就只能显示地指定依赖了。
{% endalert %}

补充：要注意的是所有AngularJS服务都是单例的。这意味着在每一个注入器中都只有一个需要的服务的实例。因为AngularJS极度讨厌全局的东西，所以是可以创建多个注入器的，并且每个住一起有自己的服务实例。但这种情况很少，除非在测试中，这样的特性才极度重要。

#### 二、总结
AngularJS服务是一种能执行一个常见操作的单例，比如$http服务是用来操作浏览器的XMLHttpRequest对象的。要使用AngularJS服务，你只需要在需要的地方（控制器，或其他服务）指出依赖就行了。AngularJS的依赖注入系统会帮你完成剩下的事情。它负责实例化，查找左右依赖，并且按照工场函数要求的样子传递依赖。

> AngularJS通过“构造器注入”来注入依赖（通过工场函数来传递依赖）。以为Javascript是动态类型语言，AngularJS无法通过静态类型来识别服务，所以你必须使用$inject属性来显式地指定依赖。

参考链接：
《Angularjs 开发指南》
[angularjs中文社区](http://www.angularjs.cn/A00L)
[angularjs services API](http://www.ngnice.com/docs/guide/services)
 









