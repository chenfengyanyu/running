---
title: js之如何访问函数内部变量
date: 2014-08-20 15:09:07
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/animal_dog.jpg
thumbnailImagePosition: left
tags: 
- js
- 闭包
comments: true
metaAlignment: center
categories: 技术博文
---
我们先来抛出一个问题，众所周知，js函数内部的局部变量，外界是访问不到的。那么js中是如何访问函数内部的局部变量，下面来个小探讨。
<!-- more -->
#### 一、起因：
```js
function one( ) {
    var b = 1;
    alert(b);
}
function two( ) {
    alert(b);
}
two();
```
如上面的代码所示，two方法无法调用到one方法中的b。

#### 二、解决方案：
```js
var a = 123;
function one() {
    var b=1;
    alert(b);
}
function two(){
    alert(a);
    alert(b);
}
two();
```

①使用全局变量；
这是最简单直观的方法了。在函数外部设置全局变量，这样方法内部都可以无限制访问啦。
```js
var a = 123;
function one( ) {
    alert(a);
}
one( );
```
注意：全局变量很容易就理解了，优点也十分明显。譬如：减少变量个数、减少实参和形参之间传递的时间等等。可是我们不得不提到使用全局变量的缺点：

- 首先，全局变量保存在静态存储区，程序开始运行时为其分配内存，程序结束时释放该内存。与局部变量的动态分配。动态释放相比，生存期比较长，因此过多的全局变量会占用较多的内存单元；

- 其次，全局变量破坏了函数的封装性能。函数象一个黑匣子，一般是通过函数参数和返回值进行输入输出，函数内部实现相对独立。但函数中如果使用了全局变量，那么函 数体内的语句就可以绕过函数参数和返回值进行存取，这种情况破坏了函数的独立性，使函数对全局变量产生依赖。同时，也降低了该函数的可移植性。

- 最后，全局变量使函数的代码可读性降低。由于多个函数都可能使用全局变量，函数执行时全局变量的值可能随时发生变化，对于程序的查错和调试都非常不利。

②使用闭包；
先来说说闭包的概念：
{% alert info %}
有权访问另一个函数作用域中变量的函数。创建方式，就是在一个函数内部创建另一个函数，通过另一个函数访问这个函数的局部变量。
{% endalert %}
下面我来举例说明吧。
```js
function one() {
    var a = 1;
    return function () {
        return a;
    };
}
//方式一，匿名函数调用
alert(one( ));         //返回匿名方法function()
alert(one( )( ));      //返回1
//另一种方式,较为直观
var b = one();
alert(b());             //返回1
```
{% alert danger %}
注意：使用闭包会把局部变量驻留在内存中，避免了使用全局变量。由于闭包作用域返回的局部变量资源不会被立刻销毁回收，所以可能会占用更多的内存。
{% endalert %}

参考文章：
http://www.cnblogs.com/xianbing/archive/2012/02/27/2369369.html 