---
title: 细说typeof与instanceof
date: 2015-03-25 13:35:25
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/animal_20140412180238.png
thumbnailImagePosition: left
tags: 
- js
comments: true
metaAlignment: center
categories: 技术博文
---
好久没写博客了，不是没得写，只是好多东西真心不好写。不过还好，零零散散都记在了笔记本上，稍后整理整理就可以搬出来了。嗯~这个~虽然有些不齿，可是我一个小人物，谁又会在意呢？当然，我也不需要在意谁！
<!--more-->
突然想到了自己最初写博客的初衷，我喜欢总结，更喜欢分享。也许十年后再次回顾，定然会被自己当初这一点小坚持所打动。而我只是想在自己前进的过程中添加一些成长的印记，也许微不足道，也许一文不值。但是，我坚持下来了，我做到了，我问心无愧。

好了，到这里打住，我们进入正文。心血来潮，想把js一些基础过一遍。只看不记忘得快，所以，边回顾边总结就成了最好的复习方式。下面我们来看看js中typeof和instanceof是如何使用的？

#### 一、基本概念
- typeof:用来检测变量的数据类型，可能返回的值如下：
undefined、boolean、string、number、object、function

- instanceof:判断一个变量是否是某个对象的实例，返回true或者false。
> 注意：instanceof 检测一个对象A是不是另一个对象B的实例的原理是，查看对象B的prototype指向的对象是否在对象A的[[prototype]]链上。如果在，则返回true,如果不在则返回false。

#### 二、示例
①typeof示例如下：
```javascript
var aa = undefined;
alert(typeof aa); //undefined

var aa = true;
alert(typeof aa);//boolean

var aa = 'Jartto';
alert(typeof aa); //string

var aa = NaN;
alert(typeof aa); //number

var aa = null;
var bb = [];
var cc = {};
alert(typeof aa); //object
alert(typeof bb); //object
alert(typeof cc); //object

var aa = function(){};
alert(typeof aa); //function
```

②instanceof
```javascript
var a=new Array();
alert(a instanceof Array);//true

var a=[];
alert(a instanceof String);//false

var a=[];
alert(a instanceof Object);//true

var a=function(){};
alert(a instanceof Function);//true

function test(){};
var a=new test();
alert(a instanceof test);//true

function Animal(){}
function People(){}
People.prototype = new Animal();
var people = new People();
console.log(people instanceof People)//true
console.log(people instanceof Animal)//true
```

上面的代码中是判断了一层继承关系中的父类，在多层继承关系中，instanceof 运算符同样适用。

参考：
instanceof用法：http://www.studyofnet.com/news/175.html
typeof：http://blog.sina.com.cn/s/blog_532751d90100iv1r.html













