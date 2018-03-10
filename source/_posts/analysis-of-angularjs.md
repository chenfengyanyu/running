---
title: 剖析 Angularjs 语法
date: 2018-02-01 22:54:56
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/angular0.png
thumbnailImagePosition: left
tags: 
- angularjs
comments: false
metaAlignment: center
categories: 技术博文
---
前端的框架不计其数，学习的速度真是追不上更新速度。那么问题来了，如何才能以不变应万变呢？很多童鞋可能猜到了，对，就是看源码。既知其然又知其所以然。
<!-- more -->
#### 一、前言
写文章的时候，我去看了一下官网文档，已然更新至 `V5.2.3` 了。有意思的是，在上级的「英明决定」下，我们还在用着 `V1.3` 的版本。怀着对知识的敬畏以及渴望，这一次我决定弄懂所有的问题，做一次彻底了断吧。也许，以后再也用不到这个版本了。

{% alert success %}
再次感谢 `Will` 童鞋的分享，让我有了重新认识 `Angularjs` 的机会！
{% endalert %}

#### 二、明确框架和类库
被很多人问过：“框架和类库的区别是什么？”，可能大家有一些感官的认识，但是却又很难形容。那么我们不妨来逐个分析一下这两个问题：

1.什么是框架？
框架，是一套完整的解决方案，使用框架的时候，需要把你的代码放到框架合适的地方，框架会在合适的时机调用你的代码。

2.什么是类库？
库，本质上是一些函数的集合。每次调用函数，实现一个特定的功能，接着把控制权交给使用者。

3.主要区别
- You call Library, Framework calls you
- 核心点：谁起到主导作用（控制反转）
  - 框架中控制整个流程的是框架
  - 使用库，由开发人员决定如何调用库中提供的方法（辅助）
- 好莱坞原则：Don't call us, we'll call you.
- 框架的侵入性很高（从头到尾）

{% alert info %}
结论：Angularjs 是框架，React 是类库。
{% endalert %}

更多细节的比较，可以[参考这里](https://juejin.im/entry/5a54b747518825734216c3df)

#### 三、双向绑定
`Angularjs` 是以数据双向绑定出名的一个框架，视图的变更会影响到模型，同理，模型的变更也能影响到视图。为什么会如此神奇，底层的实现原理到底是怎样的呢？下面我们来细节拆分，举例说明吧：
首先，先来一个 `html` 结构：
```html
<div>
  <button ng-click="week = '周几'">修改日期</button>
  <input ng-model="week" />
</div>
```
如上，我们设置了一个 `ng-click` 方法，以及一个 `ng-model` ，涉及到了 `week` 变量。经过 `$compile` 编译之后，实际是做了这样的处理：
```js
$scope.watch('week',function(el){
  el.innerHTML = $interpolate(el.innerHTML);
})
```

#### 四、关于 Scope
作用域是一个指向应用模型的对象。它是表达式的执行环境。作用域有层次结构，这个层次和相应的 `DOM` 几乎是一样的。作用域能监控表达式和传递事件。

{% alert success %}
我们可以把作用域和它的属性当做是用来渲染视图的数据。作用域是视图唯一相关联的变化来源。
{% endalert %}

作用域的特点：
- 作用域提供APIs（$watch）来观察模型的变化。
- 作用域提供APIs($apply)将任何模型的改变从“AngularJS领域(Angularrealm)”通过系统映射到视图上。
- 作用域能通过共享模型成员的方式嵌套到应用组件上。一个作用域从父作用域继承属性。
- 作用域提供表达式执行的上下文。比如说表达式：`{{username}}`

{% alert info %}
本身是无意义的，除非把它放到指定 username 属性的作用域中。
{% endalert %}

#### 五、强大的 $eval
在Angular中，有几种方式可以在作用域的上下文上执行代码，最简单的一种就是$eval。它使用一个函数作参数，所做的事情是立即执行这个传入的函数，并且把作用域自身当作参数传递给它，返回的是这个函数的返回值。$eval也可以有第二个参数，它所做的仅仅是把这个参数传递给这个函数。

$eval的实现很简单：
```js
Scope.prototype.$eval = function(expr, locals) {
  return expr(this, locals);
};
```

#### 六、依赖注入实现原理
下来看看依赖注入是如何定义的：
依赖注入，也就是 `Dependency injection`，是一种软件设计模式，用来处理代码的依赖关系。
开发指南中提到，用三种方法让函数获得它的需要依赖。
- 其一，它的依赖是能被创建的，一般用new操作符就行。
- 其二，能够通过全局变量查找依赖。
- 其三，依赖能在需要时被导入。

前两种方式都不是很好，因为它们需要对依赖硬编码，使得修改依赖的时候变得困难。特别是在测试的时候不好办，因为对某个部分进行孤立的测试常常需要模拟它的依赖。第三种方式是最好的，因为它不必在组件中去主动需找和获取依赖，而是由外界将依赖传入。

①推断依赖：这是最简单的处理依赖的方法，就是假设函数的参数名就是依赖的名字。
```js
function MyController($scope,greeter){}
```
给出一个注入器可以通过检查声明来获取函数名，从而知道需要的依赖的函数。在上面的例子中，`$scope` 和`greeter` 是需要注入到函数中的依赖。

②$inject标记：要允许压缩类库重命名函数参数，同时注入器又能正确处理依赖的话，函数需要使用$inject属性。这个属性是一个包含依赖的名称的数组。
```js
var MyController = function(renamed$scope, renamedGreeter) {}
MyController.$inject = ['$scope', 'greeter'];
```
注意 `$inject` 标记里的值和函数声明的参数是对应的。这种方式适合用于控制器的声明，因为控制器有了明确的声明标记。

③行内标记
```js
someModule.factory('greeter', ['$window', function(renamed$window) {...}]);
```

更多的细节，请参考我之前的这篇文章：[Angularjs学习笔记06~依赖注入 DI](http://jartto.wang/2014/04/24/angularjs-part-7/)

#### 七、$injector
- 是什么：根据名字获取组件的⽅方法。
- 地⽅：从缓存⾥里里找，没有的话实例例化一个应⽤，一般只有⼀个 $injector 实例。 
- 如何产生: bootstrap 里 createInjector

结合 $injector 使⽤ module ⾥的数据
```js
  // module.js
  angular.module('App')
    ...
    .constant('num1', 3)
    .constant('num2', 2)
  // app.js
  angular.module('App', []).controller('Controller', ['$scope', 'week', '$injector',    function($scope, week, $injector) {
  ￼    function sum (num1, num2) {
        return num1 + num2;
      }
      const num1 = $injector.get('num1');
      const num2 = $injector.get('num2');
      console.log(sum(num1, num2));
    }
  ]);
```

#### 八、无处不在的 Provider
```js
var invokeLater = function(service, method, arrayMethod, queue) {
  return function() {
    queue = queue || invokeQueue;
    queue[arrayMethod || 'push']([service, method, arguments]);
    return moduleInstance;
  };
};

var moduleInstance = {
  name: name,
  requires: requires,
  constant: invokeLater('$provide', 'constant', 'unshift'),
  provider: invokeLater('$provide', 'provider'),
  factory: invokeLater('$provide', 'factory'),
  value: invokeLater('$provide', 'value'),
  service: invokeLater('$provide', 'service'),
  decorator: invokeLater('$provide', 'decorator'),
  filter: invokeLater('$filterProvider', 'register'),
  directive: invokeLater('$compileProvider', 'directive'),
  controller: invokeLater('$controllerProvider', 'register'),
  component: invokeLater('$compileProvider', 'component'),

  config: invokeLater('$injector', 'invoke', 'push', configBlocks),
  run: function(fn) {
    moduleInstance._runBlocks.push(fn);
    return moduleInstance;
  },
  _invokeQueue: invokeQueue,
  _configBlocks: configBlocks,
  _runBlocks: []
};
```

#### 九、$apply 和 $digest
$apply 会使 ng 进入 $digest cycle, 并从 $rootScope 开始遍历(深度优先)检查数据变更。$digest 仅会检查该 scope 和它的子 scope，当你确定当前操作仅影响它们时，用 $digest 可以稍微提升性能。
```js
const $apply = exp => {
  $eval(exp);
  $rootScope.$digest();
}
```

$digest()触发条件：
1. DOM事件
2. ajax相应事件
3. $timeout, $interval
4. 手动执行执行$digest()或$apply()
5. 浏览器Location变更事件 ($location)

#### 十、脏值检测
我们摘录出脏值检测的核心代码，先来研究一下源代码：
```
function Scope() { 
  this.$$watchers = [];
}

Scope.prototype.$watch = function (watchFn, listenerFn) { 
  var watcher = {
    watchFn: watchFn,
    listenerFn: listenerFn
  };
  this.$$watchers.push(watcher);
};

Scope.prototype.$digest = function () {
  var self = this;
  var newValue, oldValue; _.forEach(this.$$watchers, function (watcher) {
    newValue = watcher.watchFn(self);
    oldValue = watcher.last;
    if (newValue !== oldValue) {
      watcher.last = newValue;
      watcher.listenerFn(newValue, oldValue, self);
    }
  }); 
};
```
脏值检测简单来说就是给每个需要绑定的元素加个 `watcher`，缓存下 `oldValue`，然后定时遍历所有的`watcher`，比较 `newValue` 和 `oldValue`，如果变化了做更新操作。

#### 十一、启动流程
![启动流程](http://7xvi3w.com1.z0.glb.clouddn.com/angular1.png)

#### 十二、参考资源
[build-your-own-angularjs](https://github.com/teropa/build-your-own-angularjs)