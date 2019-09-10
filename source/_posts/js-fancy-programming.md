---
title: JS 花式编程
date: 2017-10-18 23:05:03
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/fancyjs0.png
thumbnailImagePosition: left
tags: 
- js
comments: false
metaAlignment: center
categories: 技术博文 
---
炫技可能是程序员的癖好，加之 JS 的神奇 Feature，所以各种编程中的奇技淫巧层出不穷。最近看了几篇挺火的文章，觉得挺有意思，特此收集。
<!-- more -->
#### 一、在 if 中使用逗号表达式
```js
if(let a = 1, let b = a) console.log(b);
```
{% alert success %}
Jartto: 值得一提的是，逗号表达式会执行每一部分，而结果是逗号后部分。
{% endalert %}

#### 二、有趣的 try catch
```
try {
    something
} catch (e) {
    window.location.href =
        "http://stackoverflow.com/search?q=[js]+" +
        e.message;
}
```

#### 三、生成随机字符串
```js
Math.random().toString(16).substring(2) // "5c31298e546e2"
Math.random().toString(36).substring(2) // "isx80x30s2"
```
{% alert success %}
Jartto: 我如果没记错，这是某企业的笔试题。
{% endalert %}

#### 四、`JS` 中 `void` 的使用
```js
let data = void 0; // undefined
```
{% alert success %}
Jartto: void在JavaScript中是一个操作符，对传入的操作不执行并且返回 undefined。
{% endalert %}

我们经常在 `href` 中写 `javascript:void(0)`，其实通过 `void` 来操作不执行。

#### 五、隐式取整
```js
let a = ~~2.33; // 2
let b = 2.33 | 0; // 2
let c = 2.33 >> 0; // 2
```

#### 六、金钱格式化 `1,234,567,890`
方式一：
```js
Let test1 = '1234567890';
Let format = test1.replace(/\B(?=(\d{3})+(?!\d))/g, ',');

console.log(format); // 1,234,567,890
```
方式二：
```js
function formatCash(str) {
  return str.split('').reverse().reduce((prev, next, index) => {
      return ((index % 3) ? next : (next + ',')) + prev;
  })
}
console.log(formatCash('1234567890')); // 1,234,567,890
```

方式三：
```js
Number(1234567890).toLocaleString();
```

#### 七、隐式转换字符串类型成数字
```js
let a = '1a';
console.log(+a); // NaN
```
等价于：
```js
let b = Number('1a');
console.log(b); // NaN
```

{% alert success %}
Jartto: 规范编程的话，请避免隐式转换，众所周知，隐式转换不符合语意化编程。
{% endalert %}

#### 八、隐式转换布尔类型
```js
let b = !! a;
```

#### 九、注意 `parseInt`
```js
parseInt(0.0000007) === 7 // true
```

#### 十、数组去重
方式一：
```js
[...new Set([1, "1", 2, 1, 1, 3])]
```
方式二：
```js
let a = [1, 1, "1", 2, 2, "2", 3, 3, "3"];
let b = Array.from(new Set(a));
console.log(b);
```
方式三：
```js
Array.prototype.unique3 = function(){
 var res = [];
 var json = {};
 for(var i = 0; i < this.length; i++){
  if(!json[this[i]]){
   res.push(this[i]);
   json[this[i]] = 1;
  }
 }
 return res;
}

var arr = [112,112,34,'你好',112,112,34,'你好','str','str1'];
alert(arr.unique3());
```

原理：
1. 创建一个新的数组存放结果
2. 创建一个空对象
3. for循环时，每次取出一个元素与对象进行对比，如果这个元素不重复，则把它存放到结果数组中，同时把这个元素的内容作为对象的一个属性，并赋值为1，存入到第2步建立的对象中。

{% alert info %}
至于如何对比，就是每次从原数组中取出一个元素，然后到对象中去访问这个属性，如果能访问到值，则说明重复。
{% endalert %}

#### 十一、用最短的代码实现一个长度为m(6)且值都n(8)的数组
```js
Array(6).fill(8);
```
或：
```js
[...'8'.repeat(6)];
```

#### 十二、`apply` 取一个数组的最大值和最小值
```js
let numbers = [5, 458 , 120 , -215 , 228 , 400 , 122205, -85411];
let maxInNumbers = Math.max.apply(Math, numbers);
let minInNumbers = Math.min.apply(Math, numbers);
```

#### 十三、将 `argruments` 对象转换成数组
方式一：
```js
let argArray1 = Array.prototype.slice.call(arguments);
```
方式二，`ES6` 写法：
```js
let argArray2 = Array.from(arguments);
```

#### 十四、交换两个数的值
方式一：
```js
let a = 1, b = 2;
[a, b] = [b, a];
```
方式二：
```js
let a = 1, b = 2, c;
c = a;
a = b;
b = c;
console.log(a, b);
```
{% alert success %}
Jartto: 方式二是学 C 语言时记忆最准的了🙈。
{% endalert %}

#### 十五、统计字符串中相同字符出现的次数（[参考](https://www.zhihu.com/question/46943112/answer/103585546)）
```js
let arr = 'abcdaabc';

let info = arr.split('').reduce((p, k) => (p[k]++ || (p[k] = 1), p), {});

console.log(info); //{ a: 3, b: 2, c: 2, d: 1 }
```

#### 十六、是否包含某元素
```js
if(~"asdf".lastIndexOf("a")){
  alert("exists");
}
```

{% alert info %}
~是移位操作，找不到下标返回-1，移位之后是0，恰好0==false；找到的话，至少返回0，移位后是1，也就是true
{% endalert %}

#### 十七、`includes` 和 `indexOf` 区别

#### 十八、将 n 维数组破开成一维（[参考](https://www.zhihu.com/question/46943112/answer/115665473)）
```js
let foo0 = [1, [2, 3], [4, 5, [6,7,[8]]], [9], 10]; var foo1 = foo0.join(',').split(','); 

console.log(foo1); //["1", "2", "3", "4", "5", "6", "7", "8", "9", "10"]
```

#### 十九、浮点取整
```js
2.3  >> 0
```
{% alert success %}
Jartto: 按位操作只能针对 `int` 类型，所以 `js` 在做按位操作时，会自动把 `float` 转换成 `int`。
{% endalert %}

#### 二十、随机颜色值
```js
Math.floor(Math.random() * (2 << 23)).toString(16);
```

#### 二十一、总结
恰巧看到了几篇文章，觉得自己从中学到了一些知识，于是逐一尝试并加上了自己的理解，总结而出。理解这些 JS 的奇技淫巧，也许并不能提高我们在实际中的编程能力，但起码不会成为你读一些小众插件源码的障碍，这就够了。

#### 二十二、参考
1. [这些 JavaScript 编程黑科技](https://github.com/jawil/blog/issues/24)
2. [JS Fuck](https://github.com/aemkei/jsfuck)
3. [短小却令人惊叹的 JavaScript 代码](https://www.zhihu.com/question/46943112)
4. [JS 数组去重](http://www.jb51.net/article/46154.htm)