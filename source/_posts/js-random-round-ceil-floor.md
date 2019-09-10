---
title: js中Math之random,round,ceil,floor用法总结
date: 2015-04-30 15:18:05
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/animal_20140609142256.png
thumbnailImagePosition: left
tags: 
- js
comments: true
metaAlignment: center
categories: 技术博文
---
js的知识点总是边学边忘，这不，时间长不写，Math中这个几个方法又开始混淆了。翻阅文档一个个又查了一遍，赶紧记个笔记，算是巩固一下基础知识吧！文章太基础了，高手直接跳过，谢谢~
<!--more-->
1.random
返回介于 0 ~ 1 之间的一个随机数。
```javascript
var arr=[];
for(var i=0;i<5;i++){
  var r=Math.random()*10;
  arr.push(r);
}
console.log(arr);//[4.921736601237535, 0.6815536329616378, 9.11504234974638, 2.9633945899038325, 3.7655477568488047]
```
2.round
把一个数字舍入为最接近的整数。
```javascript
console.log(Math.round(0.60))//1
console.log(Math.round(0.40))//0
console.log(Math.round(5))//5
console.log(Math.round(5.1))//5
console.log(Math.round(-5.1))//-5
console.log(Math.round(-5.9))//-6
```

3.ceil
ceil() 方法执行的是向上取整计算，它返回的是大于或等于函数参数，并且与之最接近的整数。
```javascript
console.log(Math.ceil(0.60))//1
console.log(Math.ceil(0.40))//1
console.log(Math.ceil(5))//5
console.log(Math.ceil(5.1))//6
console.log(Math.ceil(-5.1))//-5
console.log(Math.ceil(-5.9))//-5
```

4.floor
floor() 方法执行的是向下取整计算，它返回的是小于或等于函数参数，并且与之最接近的整数。
```javascript
console.log(Math.floor(0.60))//0
console.log(Math.floor(0.40))//0
console.log(Math.floor(5))//5
console.log(Math.floor(5.1))//5
console.log(Math.floor(-5.1))//-6
console.log(Math.floor(-5.9))//-6
```
当然，Math中的方法非常多，可是我又不想接着写了，先这样吧～

参考链接：
w3school:http://www.w3school.com.cn/jsref/jsref_obj_math.asp







