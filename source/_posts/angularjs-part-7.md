---
title: Angularjs学习笔记06~依赖注入 DI
date: 2014-04-24 12:15:27
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
从上一篇angularjs笔记到现在大概快有一个月了吧，期间有些小变故。譬如换了份工作，适应新工作、学习新技术等，所以影响到了笔记的整理。好吧，我承认，这不是个好借口。没有任何原因，只是因为自己懒惰了。不过，行动着哪怕缓缓移动，也会比不动强百倍。所以，我还会继续前进，继续努力。
<!-- more -->
顺便说点题外话，最近又开始学习nodejs了。下意识的将nodejs和angularjs进行了一些比较，得出了一个自认为很有道理的结论（个人观点，纯属娱乐）：

angularjs是后台人员学习前端开发的一个跳板，因为它简化了前端大量而又繁琐的dom操作，开发人员只需要处理逻辑，所以后台人员应该会比较喜欢；
而nodejs呢，是用js写的服务器端语言，对于那些想成为全栈工程师的前端开发人员来说，这又是向后端进军的一个跳板。因为js可以从前到后贯穿于整个项目了，所以吸引力不言而喻。

感情抒发完毕，下面直奔主题吧！

#### 一、何谓依赖注入？
依赖注入，也就是Dependency injection，是一种软件设计模式，用来处理代码的依赖关系。
开发指南中提到，用三种方法让函数获得它的需要依赖。其一，它的依赖是能被创建的，一般用new操作符就行。其二，能够通过全局变量查找依赖。其三，依赖能在需要时被导入。
前两种方式都不是很好，因为它们需要对依赖硬编码，使得修改依赖的时候变得困难。特别是在测试的时候不好办，因为对某个部分进行孤立的测试常常需要模拟它的依赖。第三种方式是最好的，因为它不必在组件中去主动需找和获取依赖，而是由外界将依赖传入。

#### 二、三种依赖方式
注入器需要应用提供一些标记来表示自己需要的依赖。在关于AngularJS的某些API文档中你会看到函数都是被注入器调用的。注入器需要知道函数需要什么依赖。下面有三个等效的表示的自己需要的依赖的方法。这些方法可以互相替换，并且是等效的。

①推断依赖
这是最简单的处理依赖的方法，就是假设函数的参数名就是依赖的名字。
```js
function MyController($scope,greeter){}
```
给出一个注入器可以通过检查声明来获取函数名，从而知道需要的依赖的函数。在上面的例子中，$scope和greeter是需要注入到函数中的依赖。

②$inject标记
要允许压缩类库重命名函数参数，同时注入器又能正确处理依赖的话，函数需要使用$inject属性。这个属性是一个包含依赖的名称的数组。
```js
var MyController = function(renamed$scope, renamedGreeter) {}
MyController.$inject = ['$scope', 'greeter'];
```
注意$inject标记里的值和函数声明的参数是对应的。这种方式适合用于控制器的声明，因为控制器有了明确的声明标记。

③行内标记
```
someModule.factory('greeter', ['$window', function(renamed$window) {...}]);
```

#### 三、代码示例
好吧，上面描述确实有些抽象。这些概念我也总结不出来，无非就是将《开发指南》和《Mastering Web Application Delelopment with AngularJs》中对依赖注入的描述语句搬过来，下面通过一个简单示例来说明问题，这样可能会清楚一些。

```
function HelloController($scope,$location){
     $scope.greeting={text:'Hello'};
     //这里可以使用$location对象来做一些很酷的事情
}
```

这里我们想把数据绑定到用户浏览器中的URL地址上，可以使用$location对象，只要把$location对象放到我们的构造函数中就可以了。
这种神奇效果是通过angularjs的依赖注入机制实现的。依赖注入让我们遵守这样一种开发风格：
我们的类只是简单的获取它们所需要的东西，而不需要创建那些它们所依赖的东西。

#### 四、关于依赖注入
angularjs的依赖注入其实是个很深的领域，鄙人功力较浅，尚不能很好的理解。不过，网上有不少大神对此有过整理，感兴趣或者想要进阶的童鞋请点这里：
①理解angularjs中的依赖注入
http://www.html-js.com/article/1980
②依赖注入
http://www.cnblogs.com/lcllao/archive/2012/09/23/2699401.html
③服务与依赖注入
http://www.cnblogs.com/lcllao/archive/2012/09/23/2699401.html

参考文章：
AngularJS开发指南：http://www.angularjs.cn/A00z

书籍：
《Mastering Web Application Delelopment with AngularJs》
《用AngularJs开发下一代Web应用》







