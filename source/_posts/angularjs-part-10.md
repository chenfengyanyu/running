---
title: Angularjs学习笔记09~指令 Directive（二）参数定义
date: 2014-04-28 14:45:53
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
前一篇笔记中对指令简要的进行了介绍，同时也给出了一个指令的伪模板。如果初次接触，你会发现该模板中满是各种符号以及一些看不懂的属性。当然，不用担心，这也正是这篇笔记存在的意义。
<!-- more -->
工欲善其事必先利其器，要熟练而又正确的使用指令，我们需要先去了解一些指令中常用参数的定义与使用方法。还是分下面这三部分来说吧
其一，指令 Directive（一）初探
其二，指令 Directive（二）参数定义
其三，指令 Directive（三）自定义验证实例

接着上一篇笔记，今天我来剖析剖析第二部分：参数定义

#### 一、指令参数详解
①restrict:描述了指令在模板中的使用方式，包括：元素、属性、css样式类、注释或者以上几种方式的任意组合；
![directive](http://7xvi3w.com1.z0.glb.clouddn.com/angular_80439AA2-75E8-4720-87A0-E1EAEBA02284.png)

②priority:设置指令在模板中的执行顺序，顺序是相对于元素上的其他指令而言的。
如果你需要在单个DOM元素上使用多个指令，同时对指令生效的顺序有要求，那么你可以使用priority（优先级）属性来定义他们生效的顺序，数值越高会越先运行。如果不指定，priority属性的默认值为0。

③template:以字符串的形式编写一个内联模板。如果以URL的方式提供模板，此属性会被忽略；

④templateUrl:描述加载模板所要使用的URL。如果使用字符串的形式提供内联的模板，此属性会被忽略；

⑤replace:如果此配置项为true，则替换指令所在的元素。如果为false或者不指定，则把当前指令追加到所在的元素内部；

⑥transclude:把指令元素中原来的子节点移动到一个新模板内部

⑦scope:为当前指令创建一个新的作用域，而不是使之继承父作用域

![directive](http://7xvi3w.com1.z0.glb.clouddn.com/angular_D7E2F1D9-BE71-4230-B985-A5636F61316D.png)

绑定策略：
![directive](http://7xvi3w.com1.z0.glb.clouddn.com/angular_06947513-8A95-4F4A-B610-B8D3C12868CC.png)

@或@attr：
将本地作用域成员成员和DOM属性绑定。绑定结果总是一个字符串，因为DOM的属性就是字符串。如果DOM属性的名字没有被指定，那么就和本地属性名一样。比如说 
```
<widget my-attr="hello {{name}}"> 
```
和作用域对象: 
```
{ localName:'@myAttr' } 
```
当 name 值改变的时候，作用域中的LocalName也会改变。这个 name 是从父作用域中读来的（而不是组件作用域）。

=或=expression(表达式)：
在本地作用域属性和父作用域属性间建立一个双向的绑定。如果没有指定父作用域属性名称，那就和本地名称一样。比如 
```
<widget my-attr="parentModel"> 
```
和作用域对象: 
```
{ localModel:'=myAttr' } 
```
本地属性 localModel 会反映父作用域中 parentModel 的值。localModel和parentModel的任一方改变都会影响对方。

&或&attr：
提供了一种能在父作用域下执行表达式的方法。如果没有指定父作用域属性名称，那就和本地名称一样。比如 
```
<widget my-attr="count = count + value"> 
```
和作用域对象： 
```
{ localFn:'increment()' }
```
 本地作用域成员 localFn 会指向一个 increment 表达式的函数包装。通常你可以通过这个表达式从本地作用域给父作用域传值，操作方法是将本地变量名和值得对应关系传给这个表达式的包装函数。比如说，这个表达式是``` 
 increment(amount) 
 ```
 那么你就可以用调用 
 ```
 localFn({amount:22}) 
 ```
 的方式指定amount的值。

⑧controller:创建一个控制器，它会暴露一个API，利用这个API可以在多个指令之间进行通信；
$scope-当前元素关联的作用域。
$element-当前元素
$attrs-当前元素的属性对象。
$transclude-模板链接功能前绑定到正确的模板作用域： function(cloneLinkingFn)

⑨require:要求必须存在另一个指令，当前指令才能正确运行；

![directive](http://7xvi3w.com1.z0.glb.clouddn.com/angular_6DCF7297-5E12-4F7A-BA36-8392D24CF5BA.png)

⑩link和compile:对于link，使用编程方式修改最终生成的DOM元素实例，添加事件监听器，并设置数据绑定；对于compile，在使用ng-repeat时，用编程的方式修改DOM模板，从而实现同一个指令的跨越多个实例的特性。compile函数也可以返回一个link函数，可以用它来修改产生的元素实例。

{% alert danger %}
最近发现我的博文被一部分人转载了，当然，这样我并不介意。可是，原文链接，麻烦标记出来，请不要据为己有。毕竟我是一字一字码出来的，请尊重一下我的劳动成果。
{% endalert %}

参考书籍：
《用AngularJs开发下一代Web应用》
《AngularJs开发指南》








