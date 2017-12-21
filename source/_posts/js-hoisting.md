---
title: js之声明提升
date: 2016-07-12 17:28:58
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/hoist_3D04E4BD-0DEB-40EE-AD1F-EAC8B6B163A1.png
thumbnailImagePosition: left
tags: 
- js
comments: true
metaAlignment: center
categories: 面试笔试
---
在遭遇了一系列笔试面试的狂轰乱炸后，由于基础不夯实，所以在应对一些问题的时候就有些捉襟见肘。好吧，那就行动起来，是时候展现真正的技术了！
<!-- more -->
####  一、概述
js声明提升包含两部分：变量声明提升和函数声明提升。
####  二、变量声明提升
{% alert success %}
js的变量声明具有hoisting机制，js引擎在执行的时候，会把所有变量的声明都提升到当前作用域的最前面。
{% endalert %}
光说概念，不举例子就是赤裸裸的耍流氓。所以，让我来举例说明：
```js
//例一
var a = 1;
function foo() { 
    console.log(a);
    var a = 2;
    console.log(a);
};
foo();//undefined,2
```
这是变量声明提升一个最经典的🌰，在function内部，变量a的声明被提升了，也就等价于下面的写法：
```js
//例二
var a = 1;
function foo() { 
    var a;
    console.log(a);
    a = 2;
    console.log(a);
};
foo();//undefined,2
```
改变之后的代码是不是看起来清爽多了，这个恐怕大家很容易就回答正确了吧。趁热打铁，我们来看这样一个例子：
```js
//例三
var a = 1;
function foo() { 
    if (!a) {
       var a = 2; 
    } 
    console.log(a);
};
foo();//2
```
输出结果为2，因为js中没有块级作用域的概念，if中的a变量声明被提升了，我们来做一个简单的调整，如下：
```js
//例四
var a = 1;
function foo() { 
	var a;
    if (!a) {
       a = 2; 
    } 
    console.log(a);
};
foo();//2
```
好，我们来稍微升级一下：
```js
//例五
var a = 1;
function foo() { 
    if (!a) {
       let a = 2; 
    } 
    console.log(a);
};
foo();//1
```
当我们把if中的a声明为let时，可以看到结果输出了1。let是es6中的新概念，主要是块内部的作用域，所以if之外就不存在了，所以就没有声明提升这一说。
{% alert info %}
var声明的变量作用域为包围它的函数，而let声明的变量作用域仅在它所在的块中。
{% endalert %}
我们接着来看例六，其实和例五大同小异，if中的闭包形成了块级作用域，所以换汤不换药，你懂的😳。
```js
//例六
var a = 1;
function foo() {
    if (!a) {
        (function() {       
            var a = 2;      
        }());               
    };
    console.log(a);
};
foo();//1
```
最后，再送大家一道笔试题哦，看看你能不能做出来～
```js
//原题：
(function(){
    a = 5;
    alert(window.a);//undefined
    var a = 10;
    alert(a);//10
})();

//不卖关子了，等效变更后：
var a=undefined;  
(function(){
    var a;    //局部变量a，默认值是undefined
    a = 5;    //因和局部变量名相同，因此全局变量a被覆盖，此处变成了给局部变量a赋值
    //alert(a);  弹出5
    //a只是变量，而不是属性 alert(a in window)返回false
    alert(window.a);  
    a = 10;
    alert(a);
})();
```
[参考链接](https://segmentfault.com/q/1010000002886791)
####  三、函数声明提升
先来区分一下函数表达式和函数声明：
```js
//函数表达式
var test1 = function() {
    //todo
}

//函数声明
function test2() {
    //todo
}
```
先来说一下js高程中关于函数声明提升给出的权威解释：
{% alert success %}
实际上，解析器在向执行环境中加载数据时，对函数声明和函数表达式并非一视同仁。解析器会率先读取函数声明，并使其在执行任何代码之前可用（可以访问）； 
{% endalert %}
我来用最生动的语言描述一下：
```js
//函数表达式，则必须等到解析器执行到它所在的代码行，才会真正被执行。
function test() {
    sayHello();

    var sayHello = function() {
       console.log('hello');
    }
}
test();//sayHello is not a function

//函数声明（Function Declaration），函数声明会连通命名和函数体一起被提升至作用域顶部。
function test() {
    sayHello();

    function sayHello() {
       console.log('hello');
    }
}
test();//hello
```

####  四、扩展
变量声明提升并非Hoisting的全部。在JavaScript中，有四种方式可以让命名进入到作用域中（按优先级）：

- 语言定义的命名：
    - 比如this或者arguments，它们在所有作用域内都有效且优先级最高，所以在任何地方你都不能把变量命名为 this 之类的，这样是没有意义的
- 形式参数：
    - 函数定义时声明的形式参数会作为变量被 hoisting 至该函数的作用域内。所以形式参数是本地的，不是外部的或者全局的。当然你可以在执行函数的时候把外部变量传进来，但是传进来之后就是本地的了
- 函数声明：
    - 函数体内部还可以声明函数，不过它们也都是本地的了
- 变量声明：
    - 这个优先级其实还是最低的，不过它们也都是最常用的

如上面的提升优先级，当我们的变量名和函数名相同的时候，函数名会覆盖变量名，例子如下：
```js
(function(){
 	var test;
    console.log(typeof test); //function
     
    function test(){}
 
    test = "jartto";
    console.log(typeof test); //string
})();
```
运行上面函数，分别打出了function，string。这里可能会有一些疑惑，既然函数会优先于变量，那么这里的第二个console不应该还是function吗？

我们来看这样一个定义：
{% alert success %}
js从来不会告诉你是否多次声明了同一个变量；遇到这种情况，它只会对后续的声明视而不见（不过，它会执行后续声明中的变量初始化）
{% endalert %}
嗯，意思很明显了，关于第二console，人家是初始化哦，这是会被执行的。

好了，就这么多了，回头见哦～



参考：
《Javascript高级程序设计》
http://openwares.net/js/javascript_declaration_hoisting.html
https://segmentfault.com/a/1190000000348228