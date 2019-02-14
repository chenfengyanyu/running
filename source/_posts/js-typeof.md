---
title: JS 基础｜搞懂 typeof 和 instanceof
date: 2019-01-17 23:21:53
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/typeof/logo.png
thumbnailImagePosition: left
tags: 
- js
- typeof
- instanceof
comments: false
metaAlignment: center
categories: 面试笔试
---
最近在做 Code Review 的时候，发现了一些小问题，查出结果之后发现竟然是因为 typeof 和 instanceof 引发的。
<!-- more -->
这属于 `JS` 的基础知识，正是由于太基础了，所以很容易被忽略，导致项目中随处可见的滥用。

{% alert success %}
为了巩固基础，我会通过实例来详细说明，让我们一起搞懂 typeof 和 instanceof。
{% endalert %}

#### 一、typeof
`typeof` 其实就是判断参数是什么类型的实例，就一个参数，用例：`typeof A`
返回值：
```
"number"、"string"、"boolean"、"object"、"function" 和 "undefined"
```
我们先来验证几个基本类型：
```js
typeof 123; // number
typeof 'jartto'; // string
typeof !!’0’; // boolean
typeof new Function(); // function
typeof name; // undefined
```
这里可以扩展一下，假如我们要判断某个变量是否存在：
```
if(!a) { console.log('error')}
```
这时候，控制台会报错：
```
Uncaught ReferenceError: a is not defined
```
所以我们可以使用 `typeof` 来判断：
```js
if(typeof a === 'undefined') { 
  console.log('error');
}
```
{% alert success %}
这样，就能避免代码异常，是一种比较严谨的处理方式。
{% endalert %}


需要注意：
`ES6` 中 `let` 和 `const` 会形成「暂时性死区」也意味着 `typeof` 不再是一个百分之百安全的操作。
```js
typeof x; // ReferenceError
let x;
```
上面代码中，变量 `x` 使用 `let` 命令声明，所以在声明之前，都属于 `x` 的「死区」，只要用到该变量就会报错。因此，`typeof` 运行时就会抛出一个 `ReferenceError`。

如果用 `typeof` 来判断引用类型，会有怎样的结果？
```js
let arr = [1,2,3];
let obj = {name: 'jartto'};
let obj1 = null;

typeof arr; // object
typeof obj; // object
typeof obj1; // object
```
如上所示，引用类型的数据，都返回了 `object`，我们无法做到精确判断。我们来总结一下：
1.对于基本类型，除 `null` 以外，均可以返回正确的结果。
2.对于引用类型，除 `function` 以外，一律返回 `object` 类型。
3.对于 `null` ，返回 `object` 类型。
4.对于 `function` 返回 `function` 类型。

{% alert info %}
这就需要用到 instanceof 来检测某个对象是不是另一个对象的实例。
{% endalert %}


#### 二、instanceof
`instanceof` 是用来判断 `A` 是否为 `B` 的实例，表达式为：`A instanceof B`，如果 `A` 是 `B` 的实例，则返回 `true`,否则返回 `false`。
```js
// 示例来自于：https://blog.csdn.net/liwenfei123/article/details/77978027
instanceof (A,B) = {
    var L = A.__proto__;
    var R = B.prototype;
    if(L === R) {
        //A的内部属性__proto__指向B的原型对象
        return true;
    }
    return false;
}
```
从上述过程可以看出，当 `A` 的 `__proto__` 指向 `B` 的 `prototype` 时，就认为 `A` 就是 `B` 的实例，我们来看几个例子：
```js
[] instanceof Array; //true
{} instanceof Object;//true
new Date() instanceof Date;//true
```
`JS` 中万物皆对象的思想：
```js
[] instanceof Object //true

function Person(){};
new Person() instanceof Person; // true
new Person instanceof Object; // true
```
再举个例子，我们通过一个简单的继承来说明：
```js
function Parent(){};
function Child(){};
function Other(){};

Child.prototype = new Parent();
let child = new Child();

child instanceof Child; // true
child instanceof Parent; // true
child instanceof Object; // true
child instanceof Other; // false
```
很简单，我们只需要理解下面这行代码就可以了：
```js
Parent.prototype.__proto__ === Object.prototype
```
#### 三、扩展
我们来分析一下 `[]`、`Array`、`Object` 三者之间的关系：

从 `instanceof` 能够判断出 `[].proto` 指向 `Array.prototype`，而 `Array.prototype.proto` 又指向了`Object.prototype`，最终 `Object.prototype.proto` 指向了 `null`，标志着原型链的结束。

因此，`[]`、`Array`、`Object` 就在内部形成了一条原型链：
![proto](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/typeof/proto.jpeg)
依次类推，类似的 `new Date()`、`new Parent()` 也会形成一条对应的原型链 。

{% alert success %}
instanceof 只能用来判断两个对象是否属于实例关系， 而不能判断一个对象实例具体属于哪种类型。
{% endalert %}

#### 四、巩固知识
Question 1：下面如何输出
```js
function fun() {}
console.log(typeof fun);
console.log(fun instanceof Function);
console.log(fun instanceof Object);
```
答：`function`, `true`, `true`

Question2：如何判断一个变量是否为数组？
```js
// 不要用 typeof 判断
typeof []; // object

// 这是es3的规定，但是这必须假定只有一个全局执行环境
[] instanceof Array; // true

// ES5的标准
Array.isArray([]); // true
```
答：通过 `instanceof` 判断，或者 `isArray` 来判断。

Question3：下面函数输出结果是什么？

```js
var name = 'World!';
(function () {
    if (typeof name === 'undefined') {
        var name = 'Jartto';
        console.log('Hi~ ' + name);
    } else {
        console.log('Hello ' + name);
    }
})();
```
答：这里需要注意变量声明提升，所以上面代码等效于：
```js
var name = 'World!';
(function () {
    var name;
    if (typeof name === 'undefined') {
        var name = 'Jartto';
        console.log('Hi~ ' + name);
    } else {
        console.log('Hello ' + name);
    }
})();
```
这时候，`typeof name` 就等于 `undefined`，所以输出结果为 `Hi~Jartto`。

#### 五、总结
突然发现我们总是忙碌于写业务，却忽略了基础的重要性。所以在不断写 `Bug` 和解决 `Bug` 的路上越走越远，却徒劳无获。

既然如此，那么我们就从当下开始，打好根基。我会陆续更新更多 `JS` 基础相关知识，大家共同学习。


