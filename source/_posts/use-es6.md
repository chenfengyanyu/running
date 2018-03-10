---
title: ES6 边学边用
date: 2017-06-09 17:33:21
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/es6.png
thumbnailImagePosition: left
tags: 
- es6
comments: false
metaAlignment: center
categories: 技术博文
---
技术不断的发展，ES6 技术已逐渐成熟。如何才能跟上时代的步伐让自己立于不败之地，除了学习，别无他法。基础永远都是第一位，那么你还在等什么？
<!-- more -->
#### 一、let
1.let 存在块级作用域；
```js
{
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
```

2.不存在变量提升；
```js
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```

3.暂时性死区
只要块级作用域内存在 `let` 命令，它所声明的变量就“绑定”（`binding`）这个区域，不再受外部的影响。
```js
var tmp = 123;

if (true) {
  tmp = 'jartto'; // ReferenceError
  let tmp;
}
```

上面代码中，存在全局变量 `tmp`，但是块级作用域内 `let` 又声明了一个局部变量 `tmp` ，导致后者绑定这个块级作用域，所以在 `let` 声明变量前，对 `tmp` 赋值会报错。

{% alert info %}
ES6 明确规定，如果区块中存在 let 和 const 命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。
{% endalert %}

如何理解暂时性死区（temporal dead zone，简称 TDZ）呢，我们来看下面一段代码：
```js
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```
有些“死区”比较隐蔽，不太容易发现。

```js
function bar(x = y, y = 2) {
  return [x, y];
}

bar(); // 报错
```
{% alert success %}
总之，暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。
{% endalert %}

4.不允许重复声明；
5.函数声明
- 允许在块级作用域内声明函数。
- 函数声明类似于 var，即会提升到全局作用域或函数作用域的头部。
- 同时，函数声明还会提升到所在的块级作用域的头部。

#### 二、const
- `const` 声明一个只读的常量。一旦声明，常量的值就不能改变。
- `const` 一旦声明变量，就必须立即初始化，不能留到以后赋值。
- `const` 的作用域与 `let` 命令相同：只在声明所在的块级作用域内有效。
- `const` 命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。
- `const` 声明的常量，也与let一样不可重复声明。

