---
title: 我所理解的call和apply
date: 2016-06-28 13:41:09
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/js_1E6D47CF-ABFF-4CF3-8922-B78C283792A3.png
thumbnailImagePosition: left
tags: 
- js
comments: true
metaAlignment: center
categories: 面试笔试
---
对于prototype 属性，它是保存所有实例方法的真正所在，也就是原型。而prototype 下有两个方法：apply()和call()，每个函数都包含这两个非继承而来的方法。
<!-- more -->
#### 一、先来看看示例
{% alert info %}
这两个方法的用途都在特定的作用域中调用函数，实际上等于设置函数体内this对象的值。
{% endalert %}
例一：
```js
function box(num1,num2){
    return num1+num2;
}

function sum(num1,num2){
	//apply()和call()可以冒充另外一个函数，this表示window作用域，[ ]表示传递的参数
	return box.apply(this,[num1,num2]);                                       
}
alert(sum(10,10));

function sum(num1,num2){
	//arguments可以当数组传递
	return box.apply(this,arguments);               
}
alert(sum(15,10));
```
例二：
```js
//prototype下面的方法call()
var color ="红色的";
var box = {
     color:"蓝色的"
}

function sayColor(){
    alert(this.color);
}
sayColor();

//用call是实现对象冒充，冒充box，冒充window下
sayColor.call(window);//红色，冒充window
sayColor.call(this);//红色，this就是window
sayColor.call(box);//冒充box，作用域就在box对象里面，所以color就是蓝色的
```
这个例子是之前作用域理解的例子修改而成，我们可以发现当我们使用call(box)方法的
时候，sayColor()方法的运行环境已经变成了box对象里了。
{% alert info %}
call()方法于apply()方法相同，他们的区别仅仅在于接收参数的方式不同。对于call()方法而言，第一个参数是作用域，没有变化，变化只是其余的参数都是直接传递给函数的。
{% endalert %}
```js
function box(num1, num2) {
	return num1 + num2;
}

function callBox(num1, num2) {
	return box.call(this, num1, num2);
}
alert(callBox(10,10));
```

{% alert success %}
事实上，传递参数并不是apply()和call()方法真正的用武之地；它们经常使用的地方是能够扩展函数赖以运行的作用域。
使用call()或者apply()来扩充作用域的最大好处，就是对象不需要与方法发生任何耦合
关系(耦合，就是互相关联的意思，扩展和维护会发生连锁反应)。
{% endalert %}

#### 二、关于call
call方法调用一个对象的一个方法，以另一个对象替换当前对象。
call([thisObj[,arg1[, arg2[, [,.argN]]]]])
参数
- thisObj
可选项。将被用作当前对象的对象。
- arg1, arg2, , argN
可选项。将被传递方法参数序列。

{% alert info %}
call 方法可以用来代替另一个对象调用一个方法。call方法可将一个函数的对象上下文从初始的上下文改变为由 thisObj 指定的新对象。
如果没有提供 thisObj 参数，那么 Global 对象被用作 thisObj。
{% endalert %}
示例三：
```js
function add(a,b)
{
	alert(a+b);
}
function sub(a,b)
{
	alert(a-b);
}
add.call(sub,3,1);
```
这个例子中的意思就是用 add 来替换 sub，add.call(sub,3,1) == add(3,1) ，所以运行结果为：alert(4);

示例四：
```js
function Class1()
{
	this.name = "class1";
	this.showNam = function()
	{
		alert(this.name);
	}
}

function Class2()
{
	this.name = "class2";
}
var c1 = new Class1();
var c2 = new Class2();
c1.showNam.call(c2);
```
{% alert info %}
注意，call 的意思是把 c1 的方法放到c2上执行，原来c2是没有showNam() 方法，现在是把c1 的showNam()方法放到 c2 上来执行，所以this.name 应该是 class2，执行的结果就是：alert（"class2"）;
{% endalert %}
示例五：实现继承
```js
function Class1()
{
	this.showTxt = function(txt)
	{
		alert(txt);
	}
}
function Class2()
{
	Class1.call(this);
}
var c2 = new Class2();
c2.showTxt("cc");
```
这样 Class2 就继承Class1了，Class1.call(this)的意思就是使用Class1对象代替this对象，那么 Class2中不就有Class1的所有属性和方法了吗，c2对象就能够直接调用Class1的方法以及属性了，执行结果就是：alert（“cc”）;

示例六：实现多重继承
```js
function Class10()
{
	this.showSub = function(a,b)
	{
		alert(a-b);
	}
}

function Class11()
{
	this.showAdd = function(a,b)
	{
		alert(a+b);
	}
}

function Class2()
{
	Class10.call(this);
	Class11.call(this);
}
```
很简单，使用两个call就实现多重继承了。当然，js的继承还有其他方法，例如使用原型链，这个不属于本文的范畴，只是在此说明call的用法。
{% alert info %}
区别在于 call 的第二个参数可以是任意类型，而apply的第二个参数必须是数组
{% endalert %}

#### 三、apply
对于apply和call两者在作用上是相同的，但两者在参数上有区别的。
对于第一个参数意义都一样，但对第二个参数：
apply传入的是一个参数数组，也就是将多个参数组合成为一个数组传入，而call则作为call的参数传入（从第二个参数开始）。
如:
```js
func.call(func1,var1,var2,var3)
```
对应的apply写法为：
```js
func.apply(func1,[var1,var2,var3])
```
示例七：
```js
alert(Math.max(5,8)) //8
alert(Math.max(5,7,9,3,1,6)) //9

var arr=[5,7,9,1]
alert(Math.max.apply(null,arr));
```

参考：
1.Javascript 中的 call 和 apply使用介绍
http://www.jb51.net/article/29734.htm
2.JavaScript学习点滴 call、apply的区别
http://www.jb51.net/article/25080.htm