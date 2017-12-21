---
title: 掌握 JS Stack Trace
date: 2017-12-09 21:44:13
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/jstrace0.png
thumbnailImagePosition: left
tags: 
- js
- stack
- trace
comments: false
metaAlignment: center
categories: 技术博文 
---
在使用 `Roadhog` 编译过程中，我的终端崩掉了，怀着好奇的心理，我找到了 `JS Stack Trace` 这样的提示，怎么理解呢，这节我们来探究一下。
<!-- more -->
本文并不会告诉你“终端崩溃了，如何解决？”，我只会围绕 “JS Stack Trace” 异常来分析，我们可能碰到了什么问题，以及需要了解的相关技术栈。

#### 一、队列（Queue）与栈（Stack）
最早是在数据结构的线性表中接触到队列（Queue）与栈（Stack），我们先来回顾一下概念：
- 栈的插入和删除操作只允许在表的尾端进行（在栈中成为“栈顶”），满足 “FIFO：First In Last Out”；
- 队列只允许在表尾插入数据元素，在表头删除数据元素,满足 “First In First Out”。

{% alert info %}
这里我们不去纠结[栈与队列的异同](http://blog.csdn.net/bbc955625132551/article/details/72773285)，而只去用概念去理解 JS 中 Stack。
{% endalert %}


#### 二、Stack
我们先来看这样一段[代码](https://segmentfault.com/a/1190000008413860)，试想一下输出结果，以及它是如何调用的：
```js
function c() {
    console.log('c');
}

function b() {
    console.log('b');
    c();
}

function a() {
    console.log('a');
    b();
}

a();
```
对上面的代码做一下简短的分析：
- 当调用 a 的时候，它会被压到栈顶；
- 然后，当 b 在 a 中被调用的时候，它会被继续压入栈顶，当 c 在 b 中被调用的时候，也一样；
- 在运行 c 的时候，栈中包含了 a，b，c，并且其顺序也是 a，b，c；
- 当 c 调用完毕时，它会被从栈顶移出，随后控制流回到 b。当 b 执行完毕后也会从栈顶移出，控制流交还到 a。最后，当 a 执行完毕后也会从栈中移出。

{% alert success %}
每当有一个函数调用，就会将其压入栈顶。在调用结束的时候再将其从栈顶移出。
{% endalert %}

补充一点： 我们可以用 `console.trace()` 将 `Stack Trace` 打印到控制台。

#### 三、Error
当 `Error` 发生的时候，通常会抛出一个 `Error` 对象。`Error` 对象也可以被看做一个 `Error` 原型，用户可以扩展其含义，以创建自己的 `Error` 对象。

`Error.prototype` 对象通常包含下面属性：
- constructor - 一个错误实例原型的构造函数
- message - 错误信息
- name - 错误名称

{% alert success %}
一个 `Error` 的堆栈追踪包含了从其构造函数开始的所有堆栈帧。
{% endalert %}

#### 四、Stack Trace
好了，介绍了一些基础点，这里重新回到我们的重点：Stack Trace - 堆栈追踪。

`Error.captueStackTrace` 函数的第一个参数是一个 `object` 对象，第二个参数是一个可选的 `function`。捕获堆栈跟踪所做的是要捕获当前堆栈的路径（这是显而易见的），并且在 `object` 对象上创建一个 `stack` 属性来存储它。如果提供了第二个 `function` 参数，那么这个被传递的函数将会被看成是本次堆栈调用的终点，本次堆栈跟踪只会展示到这个函数被调用之前。

我们来用几个例子来更清晰的解释下。我们将捕获当前堆栈路径并且将其存储到一个普通 `object` 对象中。
```js
const myObj = {};

function c() {
}

function b() {
    // 这里存储当前的堆栈路径，保存到myObj中
    Error.captureStackTrace(myObj);
    c();
}

function a() {
    b();
}

// 首先调用这些函数
a();

// 这里，我们看一下堆栈路径往 myObj.stack 中存储了什么
console.log(myObj.stack);

// 这里将会打印如下堆栈信息到控制台
//    at b (repl:3:7) <-- Since it was called inside B, the B call is the last entry in the stack
//    at a (repl:2:1)
//    at repl:1:1 <-- Node internals below this line
//    at realRunInThisContextScript (vm.js:22:35)
//    at sigintHandlersWrap (vm.js:98:12)
//    at ContextifyScript.Script.runInThisContext (vm.js:24:12)
//    at REPLServer.defaultEval (repl.js:313:29)
//    at bound (domain.js:280:14)
//    at REPLServer.runBound [as eval] (domain.js:293:12)
//    at REPLServer.onLine (repl.js:513:10)
```
我们从上面的例子中可以看到，我们首先调用了 a（ a 被压入栈），然后从 a 的内部调用了 b（ b 被压入栈，并且在 a 的上面）。在 b 中，我们捕获到了当前堆栈路径并且将其存储在了 myObj 中。这就是为什么打印在控制台上的只有 a 和 b，而且是下面 a 上面 b。

深入了解请查看：[深入理解 JavaScript Errors 和 Stack Traces](https://segmentfault.com/a/1190000008413860)

#### 五、作用域链
提到作用域链，不得不说说 `JS` 中的作用域：
{% alert info %}
作用域就是变量和函数的可访问范围，控制着变量和函数的可见性与生命周期，在 `JS` 中变量的作用域有全局作用域和局部作用域。
{% endalert %}

很好的一个[例子](http://www.cnblogs.com/dolphinX/p/3280876.html)：
```js
var a=3; //全局变量
function fn(b){ //局部变量
    c=2; //全局变量
    var d=5; //局部变量
    function subFn(){
      var e=d; //父函数的局部变量对子函数可见
      for(var i=0;i<3;i++){
          console.write(i);
      }
      console.log(i);//3, 在for循环内声明，循环外function内仍然可见，没有块作用域
    }
}
console.log(c); //在function内声明但不带var修饰，仍然是全局变量
```
{% alert success %}
`JS` 并没有块及的作用域，只有函数级作用域：变量在声明它们的函数体及其子函数内是可见的。
{% endalert %}

执行环境（execution context）定义了变量或函数有权访问的其它数据，决定了它们的各自行为。每个执行环境都有一个与之关联的变量对象（variable object, VO），执行环境中定义的所有变量和函数都会保存在这个对象中，解析器在处理数据的时候就会访问这个内部对象。

全局执行环境是最外层的一个执行环境，在 `web` 浏览器中全局执行环境是 `window` 对象，因此所有全局变量和函数都是作为 `window` 对象的属性和放大创建的。

每个函数都有自己的执行环境，当执行流进入一个函数的时候，函数的环境会被推入一个函数栈中，而在函数执行完毕后执行环境出栈并被销毁，保存在其中的所有变量和函数定义随之销毁，控制权返回到之前的执行环境中，全局的执行环境在应用程序退出（浏览器关闭）才会被销毁。

理解了上面这些内容，我们就不难理解作用域链了：
{% alert info %}
当代码在一个环境中执行时，会创建变量对象的一个作用域链（scope chain）来保证对执行环境有权访问的变量和函数的有序访问。
{% endalert %}
作用域第一个对象始终是当前执行代码所在环境的变量对象（VO）。

此处摘录自：[JavaScript 作用域链](http://www.cnblogs.com/dolphinX/p/3280876.html)

#### 六、JS 执行机制
从上面我们可以看出，每当有一个函数调用，就会将其压入栈顶，在调用结束的时候再将其从栈顶移出。那这些方法是如何执行的呢，我们就需要了解一下 [JavaScript 执行机制](https://juejin.im/post/59e85eebf265da430d571f89)，此处用原文中的图来做一下说明：
![JS 执行机制](http://7xvi3w.com1.z0.glb.clouddn.com/jstrance1.png)
简单分析一下上图中的流程：
- 同步和异步任务分别进入不同的执行"场所"，同步的进入主线程，异步的进入 `Event Table` 并注册函数。
- 当指定的事情完成时，`Event Table` 会将这个函数移入 `Event Queue`。
- 主线程内的任务执行完毕为空，会去 `Event Queue` 读取对应的函数，进入主线程执行。
- 上述过程会不断重复，也就是常说的 `Event Loop`(事件循环)。

举个例子说明一下：
```js
$.ajax({
  url: 'www.jartto.wang',
  data: {test: 'jarro'},
  success: () => {
      console.log('异步请求');
  }
})
console.log('主线程同步输出');
```
- `ajax` 进入 `Event Table`，注册回调函数 `success`。
- 执行 `console.log`('代码执行结束')。
- `ajax` 事件完成，回调函数 `success` 进入 `Event Queue`。
- 主线程从 `Event Queue` 读取回调函数 `success` 并执行。

上面的例子很简单，我们继续深入。

#### 七、垃圾回收
1.我们先来看看垃圾回收的意义：
由于字符串、对象和数组没有固定大小，所有当他们的大小已知时，才能对他们进行动态的存储分配。`JS` 程序每次创建字符串、数组或对象时，解释器都必须分配内存来存储那个实体。只要像这样动态地分配了内存，最终都要释放这些内存以便他们能够被再用，否则，`JS` 的解释器将会消耗完系统中所有可用的内存，造成系统崩溃。

2.`JavaScript` 垃圾回收的机制：
{% alert success %}
找出不再使用的变量，然后释放掉其占用的内存。由于这个过程不是实时的，而且开销比较大，所以垃圾回收器会按照固定的时间间隔周期性的执行。
{% endalert %}

那么垃圾回收器是怎么工作的，常见的方式有两种，如下：
- 标记清除
这是 `Javascript` 中最常用的垃圾回收方式。当变量进入执行环境是，就标记这个变量为“进入环境”。从逻辑上讲，永远不能释放进入环境的变量所占用的内存，因为只要执行流进入相应的环境，就可能会用到他们。当变量离开环境时，则将其标记为“离开环境”。
垃圾收集器在运行的时候会给存储在内存中的所有变量都加上标记。然后，它会去掉环境中的变量以及被环境中的变量引用的标记。而在此之后再被加上标记的变量将被视为准备删除的变量，原因是环境中的变量已经无法访问到这些变量了。最后，垃圾收集器完成内存清除工作，销毁那些带标记的值，并回收他们所占用的内存空间。

- 引用计数
另一种不太常见的垃圾回收策略是引用计数。引用计数的含义是跟踪记录每个值被引用的次数。当声明了一个变量并将一个引用类型赋值给该变量时，则这个值的引用次数就是1。相反，如果包含对这个值引用的变量又取得了另外一个值，则这个值的引用次数就减1。当这个引用次数变成0时，则说明没有办法再访问这个值了，因而就可以将其所占的内存空间给收回来。这样，垃圾收集器下次再运行时，它就会释放那些引用次数为0的值所占的内存。

“引用计数”也是不错的垃圾回收方式，为什么很少有浏览器采用，还会带来内存泄露问题呢？
{% alert danger %}
因为“引用计数”方式没办法解决循环引用问题。
{% endalert %}

这里举一个例子：
```js
function test(){
    var a = {};
    var b = {};
    a.prop = b;
    b.prop = a;
}
```
这样 `a` 和 `b` 的引用次数都是 2，即使在 `test()` 执行完成后，两个对象都已经离开环境，在标记清除的策略下是没有问题的，离开环境的就被清除，但是在引用计数策略下不行，因为这两个对象的引用次数仍然是 2，不会变成 0，所以其占用空间不会被清理。
{% alert info %}
如果这个函数被多次调用，这样就会不断地有空间不会被回收，造成内存泄露。
{% endalert %}

3.什么时候触发垃圾回收？
垃圾回收器周期性运行，如果分配的内存非常多，那么回收工作也会很困难，确定垃圾回收时间间隔就变成了一个值得思考的问题。

我们可以手动去触发垃圾回收，防止内存泄漏：
```js
a.prop = null;
b.prop = null;
```

如果这里写的不清楚，可以看看下面这两篇文章：
- [JavaScript 垃圾回收机制](https://www.cnblogs.com/zhwl/p/4664604.html)
- [JavaScript 垃圾回收](https://www.cnblogs.com/dolphinX/p/3348468.html)

#### 八、内存泄漏
上面反复提到了内存泄漏，什么会导致内存泄漏，我们又能做什么来避免这种情况呢？
1.什么是内存泄漏？
- 程序的运行需要内存。只要程序提出要求，操作系统或者运行时（runtime）就必须供给内存。
- 对于持续运行的服务进程（daemon），必须及时释放不再用到的内存。否则，内存占用越来越高，轻则影响系统性能，重则导致进程崩溃。
- 不再用到的内存，没有及时释放，就叫做内存泄漏（memory leak）。

2.如何识别内存泄漏？
[经验法则](https://www.toptal.com/nodejs/debugging-memory-leaks-node-js-applications)是，如果连续五次垃圾回收之后，内存占用一次比一次大，就有内存泄漏。这就要求实时查看内存占用。

3.如何避免？
最好能有一种方法，在新建引用的时候就声明，哪些引用必须手动清除，哪些引用可以忽略不计，当其他引用消失以后，垃圾回收机制就可以释放内存。这样就能大大减轻程序员的负担，你只要清除主要引用就可以了。

`ES6` 考虑到了这一点，推出了两种新的数据结构：`WeakSet` 和 `WeakMap`。它们对于值的引用都是不计入垃圾回收机制的，所以名字里面才会有一个 `"Weak"`，表示这是弱引用。

{% alert info %}
弱引用，不会被计入垃圾回收机制。
{% endalert %}

更多内容请移步阮一峰老师文章：[JavaScript 内存泄漏教程](http://www.ruanyifeng.com/blog/2017/04/memory-leak.html)。

