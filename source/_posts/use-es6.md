---
title: ES6 边学边用一
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

`const` 实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。

但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指针，`const`
只能保证这个指针是固定的，至于它指向的数据结构是不是可变的，就完全不能控制了。

{% alert info %}
其实我们可以这么理解：const 保存的是一个房间号，房间里面的东西变不变就不管了。
{% endalert %}

因此，将一个对象声明为常量必须非常小心。

```js
const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
```
上面代码中，常量 `foo` 储存的是一个地址，这个地址指向一个对象。不可变的只是这个地址，即不能把 `foo` 指向另一个地址，但对象本身是可变的，所以依然可以为其添加新属性。

#### 三、关于全局变量
{% alert success %}
从 ES6 开始，全局变量将逐步与顶层对象的属性脱钩。
{% endalert %}
```js
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined
```

#### 四、解构
{% alert info %}
本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。
{% endalert %}

解构需要注意以下几个问题：
1.如果等号的右边不是数组（或者严格地说，不是可遍历的结构），那么将会报错。
```js
// 报错
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```

2.解构赋值允许指定默认值。
```js
let [foo = true] = [];
foo // true

let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
```

3.`ES6` 内部使用严格相等运算符（===），判断一个位置是否有值。所以，只有当一个数组成员严格等于 `undefined`，默认值才会生效。
```js
let [x = 1] = [undefined];
x // 1

let [x = 1] = [null];
x // null
```
上面代码中，如果一个数组成员是 `null`，默认值就不会生效，因为 `null` 不严格等于 `undefined`。

4.如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。
```js
function f() {
  console.log('aaa');
}

let [x = f()] = [1];
x // 1
```
{% alert warning %}
上面代码中，因为x能取到值，所以函数f根本不会执行。
{% endalert %}

5.默认值可以引用解构赋值的其他变量，但该变量必须已经声明。
```js
let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2
let [x = y, y = 1] = [];     // ReferenceError: y is not defined
```

6.解构不仅可以用于数组，还可以用于对象。
```js
let { foo, bar } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"
```
对象的解构与数组有一个重要的不同：
- 数组的元素是按次序排列的，变量的取值由它的位置决定；
- 而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。

7.如果变量名与属性名不一致，必须写成下面这样。
{% alert success %}
应用场景类似于解构的时候修改属性名称。
{% endalert %}
```js
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```

8.字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。
```js
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
```
类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值：
```js
let {length : len} = 'hello';
len // 5
```

9.函数的参数也可以使用解构赋值。
```js
function add([x, y]){
  return x + y;
}

add([1, 2]); // 3
```

#### 五、解构的用途
1.交换变量的值
```js
let x = 1;
let y = 2;

[x, y] = [y, x];
```

2.从函数返回多个值
函数只能返回一个值，如果要返回多个值，只能将它们放在数组或对象里返回。有了解构赋值，取出这些值就非常方便。
```js
// 返回一个数组

function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();

// 返回一个对象

function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```

3.函数参数的定义
```js
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```

4.提取 `JSON` 数据
```js
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```

5.遍历 `Map` 结构
```js
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world
```
如果只想获取键名，或者只想获取键值，可以写成下面这样。
```js
// 获取键名
for (let [key] of map) {
  // ...
}

// 获取键值
for (let [,value] of map) {
  // ...
}
```

6.加载模块时，往往需要指定输入哪些方法，解构赋值使得输入语句非常清晰。
```js
const { SourceMapConsumer, SourceNode } = require("source-map");
// 或者
import { Tooltip, Alert, Breadcrumb, Select, message, Row, Col, DatePicker, Form, Button, Input, Spin } from 'antd';
```