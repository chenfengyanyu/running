---
title: javascript中双叹号(!!)作用
date: 2015-10-18 11:13:35
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/animal_11.jpg
thumbnailImagePosition: left
tags: 
- js
comments: true
metaAlignment: center
categories: 技术博文
---
经常看到这样的例子：
```javascript
var a；
var b=!!a;
```
<!--more-->
a默认是undefined。!a是true，!!a则是false，所以b的值是false，而不再是undefined，也非其它值，主要是为后续判断提供便利。

> !!一般用来将后面的表达式强制转换为布尔类型的数据（boolean），也就是只能是true或者false;
因为javascript是弱类型的语言（变量没有固定的数据类型）所以有时需要强制转换为相应的类型，类似的如:

```javascript
- a=parseInt(“1234″)
- a=”1234″-0 //转换为数字
- b=1234+”” //转换为字符串
- c=someObject.toString() //将对象转换为字符串
```
其中第1种、第4种为显式转换，2、3为隐式转换
布尔型的转换，javascript约定规则为

- false、undefinded、null、0、”” 为 false
- true、1、”somestring”、[Object] 为 true

> 对null与undefined等其他用隐式转换的值，用!操作符时都会产生true的结果，所以用两个感叹号的作用就在于将这些值转换为“等价”的布尔值；

再来看看：
```javascript
var foo;
alert(!foo);//undifined情况下，一个感叹号返回的是true;
alert(!goo);//null情况下，一个感叹号返回的也是true;
var o={flag:true};
var test=!!o.flag;//等效于var test=o.flag||false;
alert(test);
```
这段代码，演示了在undifined和null时，用一个感叹号返回的都是true,用两个感叹号返回的就是false,所以两个感叹号的作用就在于，如果明确设置了变量的值（非null/undifined/0/”“等值),结果就会根据变量的实际值来返回，如果没有设置，结果就会返回false。

参考：
http://www.css88.com/archives/4545