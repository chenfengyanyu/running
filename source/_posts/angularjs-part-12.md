---
title: Angularjs学习笔记11~服务 Service（一）内建服务
date: 2014-05-05 16:16:45
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
经历了前三篇“指令”的摧残之后，我们还得接受更严格的考验。这节我们再来看angular另一个重要知识块service。这里我打算用两篇笔记去阐述，主要分为“内建服务”和“创建服务（自定义服务）”。
<!-- more -->

当然，angular服务是相当复杂的，我理解比较浅，所以只能从边缘部分做一些简单的介绍。好多书籍不都是从入门到精通嘛，我们先来个入门，精通的事就靠你的个人兴趣了。很多高手都反映，angular的学习曲线很陡，不易掌握。正因为如此，多看书，多实践才是突破的唯一途径。吐槽：好像跑偏了哎，突破跟service有毛关系啊。。。

这节，angular“内建服务”的主场，让我们直奔主题。

#### 一、Service
说到angular中的service，有人这样解释：在一个应用中，负责提供一个接口，把特定函数需要的方法放在一起。[angular开发指南](http://www.angularjs.cn/A00L)中是这么说的，
> “服务是一种由服务器端带到客户端的特性，它由来已久。AngularJS应用中的服务是一些用依赖注入捆绑在一起的可替换的对象。服务是最常和依赖注入一起用的，它也是AngularJS中的关键特性”。

其实，大概意思都差不多。除此之外，开发指南中关于services还有另外两条说明：
①angular只在应用组件依赖它的时候去初始化service；
②每一个组件依赖于一个service，该service引用自service工厂函数产生的单实例；

{% alert info %}
如果你觉得上面的话读起来怪怪的，请不要在意。个人建议，你最好去官网查看API，体验原汁原味的英文，那才是其乐无穷啊。原文被我翻译过来，貌似带了一些陕西方言的赶脚。好了，不开玩笑了，接着往下走。
{% endalert %}

#### 二、内建服务
AngularJS 提供了一组常用操作的服务。和其他的内建变量或者标识符一样，内建服务名称也总是以"$"开头。另外你也可以创建你自己的服务，我会在下节笔记中详细介绍。这里我们来聚焦内建服务，首先我们来看看angularjs中有哪些常用的内建服务以及他们的含义。

①$http：angular的核心服务，通过浏览器的XMLHttpRequest对象或者JSONP，为远程HTTP服务器的通信提供便利。
②$location：对地址栏中的URL的一系列操作
③$log：安全的将信息写入浏览器控制台，也就是控制台输出
④$timeout：angular包装了window.setTimeout，我更习惯叫它定时器
⑤$q：异步请求使用，promise/deferred模板
⑥$routeProvider：路由
。。。。。。

当然angular的内建服务还是很多的，除此之外还有$animate、$compile、$document、$interval、$parse、$filter、$window等。这里我就不一一介绍了，如果你比较感兴趣的话，[请点击这里](http://www.ngnice.com/docs/api/ng/service)

#### 三、如何使用？
要使用AngularJS服务，你只要在需要的地方（控制器，或其他服务）指出依赖就行了。AngularJS的依赖注入系统会帮你完成剩下的事情。它负责实例化，查找左右依赖，并且按照工场函数要求的样子传递依赖。

一个简单的例子可能如下所示：
```js
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
如上述代码所示，这个服务依赖于$window服务（会被当成参数传递给工场函数），并且只是个函数。这个服务的任务是存储所有的通知；在第三个通知以后，服务会用window的alert来输出所有的通知。

#### 四、将服务注入到控制器中
将服务用作控制器的依赖和将服务用作其他服务的依赖很类似。还是用同一段代码来展示，请注意其中的细微差别：
```js
angular.module('MyServiceModule', []).
factory('notify', ['$window', function(win) {
    var msgs = [];
    return function(msg) {
      msgs.push(msg);
      if (msgs.length == 3) {
        win.alert(msgs.join("\n"));
        msgs = [];
      }
    };
  }]);
function myController(scope, notifyService) {//参数名允许不一致，但不建议
  scope.callNotify = function(msg) {
    notifyService(msg);
  };
}
myController.$inject = ['$scope','notify'];//使用$inject属性来显式地指定依赖
```
为了说明问题，我又请来了祖师爷（开发指南）。开发指南中对该段代码加粗部分的解释如下：

> “因为Javascript是一种动态语言，依赖注入系统无法通过静态类型来知道应该注入什么样的服务（静态类型语言就可以）。所以，你应该$inject的属性来指定服务的名字，这个属性是一个包含这需要注入的服务的名字字符串的数组。名字要和服务注册到系统时的名字匹配。服务的名称的顺序也很重要：当执行工场函数时传递的参数是依照数组里的顺序的。但是工场函数中参数的名字不重要，但最好还是和服务本身的名字一样。”

ps：总算接近尾声了，累死了！service也属于angular中的重点知识，所以查了不少资料，但是感觉描述的不够清楚。我功力尚浅，只能粗浅的理解，难免有不当之处，请多多谅解。当然，有问题的话，欢迎拍砖~

参考链接：
《Angularjs 开发指南》
angularjs中文社区：
http://www.angularjs.cn/A00L
angularjs services API：
http://www.ngnice.com/docs/guide/services







