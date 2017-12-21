---
title: js能够保证object属性的输出顺序吗？
date: 2016-10-25 17:11:02
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/20161208_order.png
thumbnailImagePosition: left
tags: 
- js
- object
comments: true
metaAlignment: center
categories: 技术博文
---
碰到了一个很有意思的问题，js中对象属性的输出是有序的吗？如果有的话，那又遵循怎样的规则呢？
<!-- more -->
这里我们来做一个简单的探讨，工作之余来一点趣味性的问题。虽不能醍醐灌顶，但可以巩固所学知识，也是极好的～🙈

#### 一、奇怪现象
我们来看这样一个对象，来输出他的属性名称，也就是key值：
```js
var data = {'1':'aaa','2':'bbb','3':'ccc','测试':'000'};
Object.keys(data) ;
```
控制台输出：
```js
["1", "2", "3", "测试"]
```
嗯，没啥问题。那调整一下最后一项的位置，再来试试：
```js
var data = {'测试':'000','1':'aaa','2':'bbb','3':'ccc'};
Object.keys(data);
```
控制台输出：
```js
["1", "2", "3", "测试"]
```
奇怪，输出的顺序被调整了，这是为什么呢？我们继续，换一下属性名称，再试一次：
```js
var data = {'a':'000','1':'aaa','2':'bbb','3':'ccc'};
Object.keys(data);
```
控制台输出：
```js
["1", "2", "3", "a"]
```
依结果来看，貌似有点小规律，我们不妨猜一下。

#### 二、大胆猜测
我们来看结果：
```js
["1", "2", "3", "测试"]
["1", "2", "3", "a"]
```
会不会是按照ASC码的大小顺序来输出的呢？
```js
'1'<'2'<'3'<'测试'
'1'<'2'<'3'<'a'
```
好像漏掉了一种情况（字母和汉字的属性名称同时存在），我们试一下：
```js
var data = {'a':'000','3':'ccc','1':'aaa','测试':'bbb',};
Object.keys(data);
```
控制台输出：
```js
["1", "3", "a", "测试"]
```
调整属性'a'和'测试'的顺序呢？
```js
var data = {'测试':'bbb','3':'ccc','a':'000','1':'aaa'};
Object.keys(data);
```
控制台输出：
```js
["1", "3", "测试", "a"]
```
好了，到此为止，我们可以得出结论了。
{% alert danger %}
结论：对象的遍历输出并不是按照属性的ASC码升序排序的。
{% endalert %}
#### 三、初见端倪
查阅了一些文档后，得出了以下有效结论：
1.An object is a member of the type Object. It is an unordered collection of properties each of which contains a primitive value, object, or function. A function stored in a property of an object is called a method.

2.Chrome Opera 的 JavaScript 解析引擎遵循的是新版 ECMA-262 第五版规范。因此，使用 for-in 语句遍历对象属性时遍历书序并非属性构建顺序。而 IE6 IE7 IE8 Firefox Safari 的 JavaScript 解析引擎遵循的是较老的 ECMA-262 第三版规范，属性遍历顺序由属性构建的顺序决定。

#### 四、真相大白
Chrome Opera 中使用 for-in 语句遍历对象属性时会遵循一个规律：
{% alert success %}
它们会先提取所有 key 的 parseFloat 值为非负整数的属性，然后根据数字顺序对属性排序首先遍历出来，然后按照对象定义的顺序遍历余下的所有属性。
{% endalert %}
其它浏览器则完全按照对象定义的顺序遍历属性。

这和我们上面例子中的数据结果是吻合的，嗯，这就是我想要的结果！

#### 五、结案总结
{% alert info %}
如果想顺序遍历一组数据，请使用数组并使用 for 语句遍历。 
{% endalert %}
for-in语句无法保证遍历顺序，应尽量避免编写依赖对象属性顺序的代码。如果想按照定义的次序遍历对象属性，请参考[这里](http://w3help.org/zh-cn/causes/SJ9011)针对各浏览器编写特殊代码。

由于对象的输出是无序的，但是数组却是有序的，所以为了保证顺序，搞成数组再输出。嗯，就是这样！

#### 六、参考文档
1.ASCII参照表
http://baike.baidu.com/link?url=biyz3JjFtGX7OAA48C
2.js对象输出顺序
http://stackoverflow.com/questions/5525795/does-javascript-guarantee-object-property-order
3.遍历出的属性顺序与对象定义时不同
http://w3help.org/zh-cn/causes/SJ9011
4.控制js中的对象顺序输出
https://segmentfault.com/q/1010000000765467