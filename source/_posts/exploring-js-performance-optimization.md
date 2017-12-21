---
title: 探索js性能优化
date: 2016-08-05 10:42:11
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/op_AF5ECC8F-95C7-48D8-A710-A5C1062FCE76.png
thumbnailImagePosition: left
tags: 
- js
- 优化
comments: true
metaAlignment: center
categories: 技术博文
---
谈起前端优化，总是不自觉的联想到网站优化，而很少想到－js性能优化。你为js做过多少，仅仅是“压缩，精简，混淆”吗？
<!-- more -->
这里要特别感谢@思卓童鞋，在我猝不及防的情况下，给我抛出了这个问题。是时候该总结整理一下了，“知道一些”和“很了解”中间差的可不是一千两千（抱歉，最近一直都在面试，满脑子都是💰）。

这节我们来探索一下js性能优化，请坐稳扶好。

#### 一、避免全局查找
在一个函数中会用到全局对象存储为局部变量来减少全局查找，因为访问局部变量的速度要比访问全局变量的速度更快些
```js
function search() {
	//当我要使用当前页面地址和主机域名
	alert(window.location.href + window.location.host);
}
//最好的方式是如下这样  先用一个简单变量保存起来
function search() {
	var location = window.location;
	alert(location.href + location.host);
}
```
#### 二、定时器
如果针对的是不断运行的代码，不应该使用setTimeout，而应该是用setInterval，因为setTimeout每一次都会初始化一个定时器，而setInterval只会在开始的时候初始化一个定时器
```js
var timeoutTimes = 0;
function timeout() {
	timeoutTimes++;
	if (timeoutTimes < 10) {
		setTimeout(timeout, 10);
	}
}
timeout();
//可以替换为：
var intervalTimes = 0;
function interval() {
	intervalTimes++;
	if (intervalTimes >= 10) {
		clearInterval(interv);
	}
}
var interv = setInterval(interval, 10);
```
#### 三、字符串连接
如果要连接多个字符串，应该少使用+=，如
```js
s+=a;
s+=b;
s+=c;
```
应该写成s+=a + b + c；

而如果是收集字符串，比如多次对同一个字符串进行+=操作的话，最好使用一个缓存，使用JavaScript数组来收集，最后使用join方法连接起来
```js
var buf = [];
for (var i = 0; i < 100; i++) {
    buf.push(i.toString());
}
var all = buf.join('');
```
#### 四、避免with语句
和函数类似 ，with语句会创建自己的作用域，因此会增加其中执行的代码的作用域链的长度，由于额外的作用域链的查找，在with语句中执行的代码肯定会比外面执行的代码要慢，在能不使用with语句的时候尽量不要使用with语句。
```js
with (a.b.c.d) {
           property1 = 1;
           property2 = 2;
       }
       //可以替换为：
       var obj = a.b.c.d;
       obj.property1 = 1;
       obj.property2 = 2;
```

数字转换成字符串
一般最好用'' + 1来将数字转换成字符串，虽然看起来比较丑一点，但事实上这个效率是最高的，性能上来说：
('' +) > String() > .toString() > new String()
浮点数转换成整型
很多人喜欢使用parseInt()，其实parseInt()是用于将字符串转换成数字，而不是浮点数和整型之间的转换，我们应该使用Math.floor()或者Math.round()
```js
var myVar = '3.14159',
str = '' + myVar, //  to string  
i_int = ~ ~myVar,  //  to integer  
f_float = 1 * myVar,  //  to float  
b_bool = !!myVar,  /*  to boolean - any string with length 
                        and any number except 0 are true */
array = [myVar];  //  to array  
```
如果定义了toString()方法来进行类型转换的话，推荐显式调用toString()，因为内部的操作在尝试所有可能性之后，会尝试对象的toString()方法尝试能否转化为String，所以直接调用这个方法效率会更高

多个类型声明
在JavaScript中所有变量都可以使用单个var语句来声明，这样就是组合在一起的语句，以减少整个脚本的执行时间，就如上面代码一样，上面代码格式也挺规范，让人一看就明了。

插入迭代器
如var name=values[i]; i++;前面两条语句可以写成var name=values[i++]

使用直接量
```js
var aTest = new Array(); //替换为
        var aTest = [];
        var aTest = new Object; //替换为
        var aTest = {};
        var reg = new RegExp(); //替换为
        var reg = /../;
        //如果要创建具有一些特性的一般对象，也可以使用字面量，如下：
        var oFruit = new O;
        oFruit.color = 'red';
        oFruit.name = 'apple';
        //前面的代码可用对象字面量来改写成这样：
        var oFruit = { color: 'red', name: 'apple' };
```

使用DocumentFragment优化多次append
一旦需要更新DOM,请考虑使用文档碎片来构建DOM结构，然后再将其添加到现存的文档中。
```js
for (var i = 0; i < 1000; i++) {
            var el = document.createElement('p');
            el.innerHTML = i;
            document.body.appendChild(el);
        }
        //可以替换为：
        var frag = document.createDocumentFragment();
        for (var i = 0; i < 1000; i++) {
            var el = document.createElement('p');
            el.innerHTML = i;
            frag.appendChild(el);
        }
        document.body.appendChild(frag);
```

使用一次innerHTML赋值代替构建dom元素
对于大的DOM更改，使用innerHTML要比使用标准的DOM方法创建同样的DOM结构快得多。
```js
var frag = document.createDocumentFragment();
for (var i = 0; i < 1000; i++) {
    var el = document.createElement('p');
    el.innerHTML = i;
    frag.appendChild(el);
}
document.body.appendChild(frag);
//可以替换为：
var html = [];
for (var i = 0; i < 1000; i++) {
    html.push('<p>' + i + '</p>');
}
document.body.innerHTML = html.join('');
```




















参考文章：
http://www.it165.net/pro/html/201503/35336.html





































