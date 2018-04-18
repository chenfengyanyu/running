---
title: ES6 边学边用二
date: 2017-07-05 17:00:23
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/es6.png
thumbnailImagePosition: left
tags: 
- es6
comments: false
metaAlignment: center
categories: 技术博文
---
ES6 系列文章属于过文档系列，所以主要梳理 ES6 中的知识点。本文主要涉及到字符串、正则、数值扩展几方面知识。
<!-- more -->
#### 一、字符串操作
{% alert info %}
这里只列出常用的操作，更多请移步：[字符串扩展](http://es6.ruanyifeng.com/#docs/string)
{% endalert %}

1.`codePointAt` 方法是测试一个字符由两个字节还是由四个字节组成的最简单方法。
```js
function is32Bit(c) {
  return c.codePointAt(0) > 0xFFFF;
}

is32Bit("𠮷") // true
is32Bit("a") // false
```

2.`ES6` 为字符串添加了遍历器接口，使得字符串可以被 for...of 循环遍历。
```js
for (let codePoint of 'foo') {
  console.log(codePoint)
}
// "f"
// "o"
// "o"
```

3.includes(), startsWith(), endsWith()
- includes()：返回布尔值，表示是否找到了参数字符串。
- startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
- endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。

```js
let s = 'Hello world!';

s.startsWith('Hello') // true
s.endsWith('!') // true
s.includes('o') // true
```
这三个方法都支持第二个参数，表示开始搜索的位置。
```js
let s = 'Hello world!';

s.startsWith('world', 6) // true
s.endsWith('Hello', 5) // true
s.includes('Hello', 6) // false
```

4.`repeat` 方法返回一个新字符串，表示将原字符串重复 `n` 次。
```js
'x'.repeat(3) // "xxx"
'hello'.repeat(2) // "hellohello"
'na'.repeat(0) // ""
'na'.repeat(2.9) // "nana" 向下取整
'na'.repeat(Infinity) // RangeError
'na'.repeat(-1) // RangeError
'na'.repeat(NaN) // ""
'na'.repeat('3') // "nanana" 如果repeat的参数是字符串，则会先转换成数字。
```

5.padStart()，padEnd()
如果某个字符串不够指定长度，会在头部或尾部补全。`padStart()` 用于头部补全，`padEnd()` 用于尾部补全。
```js
'x'.padStart(5, 'ab') // 'ababx'
'x'.padStart(4, 'ab') // 'abax'

'x'.padEnd(5, 'ab') // 'xabab'
'x'.padEnd(4, 'ab') // 'xaba'
```
如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。
```js
'xxx'.padStart(2, 'ab') // 'xxx'
'xxx'.padEnd(2, 'ab') // 'xxx'
```
如果用来补全的字符串与原字符串，两者的长度之和超过了指定的最小长度，则会截去超出位数的补全字符串。
```js
'abc'.padStart(10, '0123456789')
// '0123456abc'
```
如果省略第二个参数，默认使用空格补全长度。
```js
'x'.padStart(4) // '   x'
'x'.padEnd(4) // 'x   '
```
`padStart` 的常见用途是为数值补全指定位数。下面代码生成 10 位的数值字符串。
```js
'1'.padStart(10, '0') // "0000000001"
'12'.padStart(10, '0') // "0000000012"
'123456'.padStart(10, '0') // "0000123456"
```
另一个用途是提示字符串格式。
```js
'12'.padStart(10, 'YYYY-MM-DD') // "YYYY-MM-12"
'09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
```

6.模板字符串
模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

#### 二、正则操作
1.点字符
点（.）字符在正则表达式中，含义是除了换行符以外的任意单个字符。对于码点大于 `0xFFFF` 的 `Unicode` 字符，点字符不能识别，必须加上 `u` 修饰符。
```js
var s = '𠮷';

/^.$/.test(s) // false
/^.$/u.test(s) // true
```

2.sticky 属性
与y修饰符相匹配，`ES6` 的正则对象多了 `sticky` 属性，表示是否设置了 y 修饰符。
```js
var r = /hello\d/y;
r.sticky // true
```

3.`flags` 属性，会返回正则表达式的修饰符
```js
// ES5 的 source 属性
// 返回正则表达式的正文
/abc/ig.source
// "abc"

// ES6 的 flags 属性
// 返回正则表达式的修饰符
/abc/ig.flags
// 'gi'
```

4.正则表达式使用圆括号进行组匹配
```js
const RE_DATE = /(\d{4})-(\d{2})-(\d{2})/;

const matchObj = RE_DATE.exec('1999-12-31');
const year = matchObj[1]; // 1999
const month = matchObj[2]; // 12
const day = matchObj[3]; // 31
```

5.具名组匹配
{% alert success %}
ES2018 引入了具名组匹配（Named Capture Groups），允许为每一个组匹配指定一个名字，既便于阅读代码，又便于引用。
{% endalert %}
```js
const RE_DATE = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/;

const matchObj = RE_DATE.exec('1999-12-31');
const year = matchObj.groups.year; // 1999
const month = matchObj.groups.month; // 12
const day = matchObj.groups.day; // 31
```
上面代码中，“具名组匹配”在圆括号内部，模式的头部添加“问号 + 尖括号 + 组名”（?<year>），然后就可以在 `exec` 方法返回结果的 `groups` 属性上引用该组名。同时，数字序号（matchObj[1]）依然有效。

6.解构赋值和替换
有了具名组匹配以后，可以使用解构赋值直接从匹配结果上为变量赋值。
```js
let {groups: {one, two}} = /^(?<one>.*):(?<two>.*)$/u.exec('foo:bar');
one  // foo
two  // bar
```
字符串替换时，使用$<组名>引用具名组。
```js
let re = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/u;

'2015-01-02'.replace(re, '$<day>/$<month>/$<year>')
// '02/01/2015'
```

7.matchAll
目前有一个提案，增加了 `String.prototype.matchAll` 方法，可以一次性取出所有匹配。不过，它返回的是一个遍历器（Iterator），而不是数组。
```js
const string = 'test1test2test3';

// g 修饰符加不加都可以
const regex = /t(e)(st(\d?))/g;

for (const match of string.matchAll(regex)) {
  console.log(match);
}
// ["test1", "e", "st1", "1", index: 0, input: "test1test2test3"]
// ["test2", "e", "st2", "2", index: 5, input: "test1test2test3"]
// ["test3", "e", "st3", "3", index: 10, input: "test1test2test3"]
```
上面代码中，由于 `string.matchAll(regex)` 返回的是遍历器，所以可以用 `for...of` 循环取出。相对于返回数组，返回遍历器的好处在于，如果匹配结果是一个很大的数组，那么遍历器比较节省资源。

遍历器转为数组是非常简单的，使用 `...` 运算符和 `Array.from` 方法就可以了。
```js
// 转为数组方法一
[...string.matchAll(regex)]

// 转为数组方法二
Array.from(string.matchAll(regex));
```

#### 三、数值操作
{% alert success %}
ES6 提供了二进制和八进制数值的新的写法，分别用前缀0b（或0B）和0o（或0O）表示。
{% endalert %}

1.从 `ES5` 开始，在严格模式之中，八进制就不再允许使用前缀 `0` 表示，`ES6` 进一步明确，要使用前缀 `0o` 表示。
```js
// 非严格模式
(function(){
  console.log(0o11 === 011);
})() // true

// 严格模式
(function(){
  'use strict';
  console.log(0o11 === 011);
})() // Uncaught SyntaxError: Octal literals are not allowed in strict mode.
```
如果要将 `0b` 和 `0o` 前缀的字符串数值转为十进制，要使用 `Number` 方法。

2.Number.isFinite()
`Number.isFinite()` 用来检查一个数值是否为有限的（`finite`），即不是 `Infinity`。
```js
Number.isFinite(15); // true
Number.isFinite(0.8); // true
Number.isFinite(NaN); // false
Number.isFinite(Infinity); // false
Number.isFinite(-Infinity); // false
Number.isFinite('foo'); // false
Number.isFinite('15'); // false
Number.isFinite(true); // false
```
{% alert warning %}
注意，如果参数类型不是数值，Number.isFinite 一律返回 false。
{% endalert %}

3.`Number.isNaN()` 用来检查一个值是否为 `NaN`
```js
Number.isNaN(NaN) // true
Number.isNaN(15) // false
Number.isNaN('15') // false
Number.isNaN(true) // false
Number.isNaN(9/NaN) // true
Number.isNaN('true' / 0) // true
Number.isNaN('true' / 'true') // true
```
{% alert warning %}
如果参数类型不是 NaN，Number.isNaN 一律返回 false。
{% endalert %}

4.Number.parseInt(), Number.parseFloat()
ES6 将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变。
```js
// ES5的写法
parseInt('12.34') // 12
parseFloat('123.45#') // 123.45

// ES6的写法
Number.parseInt('12.34') // 12
Number.parseFloat('123.45#') // 123.45
```

{% alert success %}
这样做的目的，是逐步减少全局性方法，使得语言逐步模块化。
{% endalert %}

```js
Number.parseInt === parseInt // true
Number.parseFloat === parseFloat // true
```

5.Number.isInteger() 用来判断一个数值是否为整数
```js
Number.isInteger(25) // true
Number.isInteger(25.1) // false
```
`JavaScript` 内部，整数和浮点数采用的是同样的储存方法，所以 `25` 和 `25.0` 被视为同一个值。
```js
Number.isInteger(25) // true
Number.isInteger(25.0) // true
```
如果参数不是数值，`Number.isInteger` 返回 `false`。
```js
Number.isInteger() // false
Number.isInteger(null) // false
Number.isInteger('15') // false
Number.isInteger(true) // false
```
这里需要注意一个问题：
```js
Number.isInteger(3.0000000000000002) // true
```
上面代码中，`Number.isInteger` 的参数明明不是整数，但是会返回 `true`。原因就是这个小数的精度达到了小数点后 `16`个十进制位，转成二进制位超过了 `53` 个二进制位，导致最后的那个 `2` 被丢弃了。

6.安全整数和 Number.isSafeInteger()
`JavaScript` 能够准确表示的整数范围在 `-2^53` 到 `2^53` 之间（不含两个端点），超过这个范围，无法精确表示这个值。
```js
Math.pow(2, 53) // 9007199254740992

9007199254740992  // 9007199254740992
9007199254740993  // 9007199254740992

Math.pow(2, 53) === Math.pow(2, 53) + 1
// true
```

#### 四、Math 扩展
1.`Math.trunc` 方法用于去除一个数的小数部分，返回整数部分。
```js
Math.trunc(4.1) // 4
Math.trunc(4.9) // 4
Math.trunc(-4.1) // -4
Math.trunc(-4.9) // -4
Math.trunc(-0.1234) // -0
```
对于非数值，`Math.trunc` 内部使用 `Number` 方法将其先转为数值。
```js
Math.trunc('123.456') // 123
Math.trunc(true) //1
Math.trunc(false) // 0
Math.trunc(null) // 0
```
对于空值和无法截取整数的值，返回 `NaN`。
```js
Math.trunc(NaN);      // NaN
Math.trunc('foo');    // NaN
Math.trunc();         // NaN
Math.trunc(undefined) // NaN
```
{% alert info %}
对于没有部署这个方法的环境，可以用下面的代码模拟。
{% endalert %}

```js
Math.trunc = Math.trunc || function(x) {
  return x < 0 ? Math.ceil(x) : Math.floor(x);
};
```

2.Math.sign()
`Math.sign` 方法用来判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。
- 参数为正数，返回+1；
- 参数为负数，返回-1；
- 参数为 0，返回0；
- 参数为-0，返回-0;
- 其他值，返回NaN。
```js
Math.sign(-5) // -1
Math.sign(5) // +1
Math.sign(0) // +0
Math.sign(-0) // -0
Math.sign(NaN) // NaN
```

3.Math.cbrt()
`Math.cbrt` 方法用于计算一个数的立方根。
