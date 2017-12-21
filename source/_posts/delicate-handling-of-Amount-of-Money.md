---
title: 使用js巧妙处理金额数字
date: 2017-01-23 12:29:06
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/nodejs.png
thumbnailImagePosition: left
tags: 
- js
- number
comments: true
metaAlignment: center
categories: 技术博文
---
写了好几年程序，只是考虑着如何实现，如何快速完成。最近越发喜欢“优雅”，“巧妙”这样的字眼，所以写程序也慢慢向此靠拢，实在是受益颇多。
<!-- more -->
实际编程中，我们肯定时常遇到处理金额数字这样的需求，易读的金额数字将会提升你的用户体验。这里我整理了几种不错的方式，总结一下，方便日后使用。

#### 一、含小数位控制
```js
function formatMoney(s, type) {  
    if (/[^0-9\.]/.test(s))  
        return "0";  
    if (s == null || s == "")  
        return "0";  
    s = s.toString().replace(/^(\d*)$/, "$1.");  
    s = (s + "00").replace(/(\d*\.\d\d)\d*/, "$1");  
    s = s.replace(".", ",");  
    var re = /(\d)(\d{3},)/;  
    while (re.test(s))  
        s = s.replace(re, "$1,$2");  
    s = s.replace(/,(\d\d)$/, ".$1");  
    if (type == 0) {// 不带小数位(默认是有小数位)
        var a = s.split(".");  
        if (a[1] == "00") {  
            s = a[0];  
        }  
    }  
    return s;  
}  
formatMoney('13222222',3)
```
#### 二、字符串正则处理
```js
function format_number(n){  
   var b=parseInt(n).toString();  
   var len=b.length;  
   if(len<=3){return b;}  
   var r=len%3;  
   return r>0?b.slice(0,r)+","+b.slice(r,len).match(/\d{3}/g).join(","):b.slice(r,len).match(/\d{3}/g).join(",");  
 }  
 format_number('4524324234');
 ```
 #### 三、正则表达式
 ```js
var num='12345678';
var str = num.replace(/\d(?=(?:\d{3})+\b)/g,'$&,');
console.log(str); //"12,345,678"

//扩展Number方法
Number.prototype.toCurrencyString=function(){
  return this.toFixed(2).replace(/\d(?=(?:\d{3})+\b)/g,'$&,'); 
}
```
#### 四、toLocaleString
{% alert info %}
toLocaleString 牵扯到一些本地化的数字格式的变化，这个需要注意一下。
{% endalert %}
```js
var number = 123456.789;

// 德语使用逗号作为小数分隔符，周期为千位
alert(number.toLocaleString("de-DE"));
// → 123.456,789

// 在大多数阿拉伯语国家的阿拉伯语使用真实的阿拉伯数字
alert(number.toLocaleString("ar-EG"));
// → ١٢٣٤٥٦٫٧٨٩

// 印度使用 千/十万/千万 作为分隔位置
alert(number.toLocaleString("en-IN"));
// → 1,23,456.789

// nu扩展密钥请求编号系统, e.g. 中文十进制
alert(number.toLocaleString("zh-Hans-CN-u-nu-hanidec"));
// → 一二三,四五六.七八九

// 当请求可能不被支持的语言（例如巴厘语）时，
// 加入后备语言（在这种情况下为印尼语）
alert(number.toLocaleString(["ban", "id"]));
// → 123.456,789
```
兼容问题一览：
![toLocaleString](http://7xvi3w.com1.z0.glb.clouddn.com/localString2.png)
![toLocaleString](http://7xvi3w.com1.z0.glb.clouddn.com/localString1.png)

排除掉浏览器兼容问题，个人觉得这是最巧妙的实现方式，简单到都不用写代码🙈
```js
Number(123456).toLocaleString(); //"123,456"
```

#### 五、总结
本来打算自己写个方法，后来发现了好多不错的轮子。索性摘录过来，以备后续使用，感谢轮子的主人，阿门！


参考：
1.[js 金额加逗号](https://segmentfault.com/q/1010000006071359)
2.[MDN Number](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)
3.[js数字格式化](http://blog.csdn.net/evangel_z/article/details/12839657)
4.[数字加逗号](http://blog.csdn.net/fyq891014/article/details/41869807)
