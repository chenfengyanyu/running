---
title: js之强大的正则表达式
date: 2016-07-03 10:48:38
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/regexp_1E7489A1-CE11-4278-A7DA-EAD874DC3C3F.png
thumbnailImagePosition: left
tags: 
- js
- 正则
comments: true
metaAlignment: center
categories: 技术博文
---
刚学js那会，头一次接触正则就是在表单验证提交数据的时候。当然，正则表达式不仅仅局限于此，那么这几年我又学到了哪些？咱们从头屡屡，算是阶段性总结吧！
<!--more-->
#### 一、关于正则表达式
这里引用一下李炎恢老师对正则的定义：
正则表达式 (regular expression)是一个描述字符模式的对象。 ECMAScript的RegExp类
表示正则表达式，而 String 和 RegExp 都定义了使用正则表达式进行强大的模式匹配和文本 检索与替换的函数。

正则表达式主要用来验证客户端的输入数据。用户填写完表单单击按钮之后，表单就会 被发送到服务器，在服务器端通常会用PHP、ASP.NET等服务器脚本对其进行进一步处理。 因为客户端验证，可以节约大量的服务器端的系统资源,并且提供更好的用户体验。

#### 二、创建方式
方式一：使用new的方式
```js
var box = new RegExp('box'); //第一个参数字符串
var box = new RegExp('box', 'ig'); //第二个参数可选模式修饰符
```
方式二：使用字面量方式
```js
var box = /box/; //直接用两个反斜杠
var box = /box/ig; //在第二个斜杠后面加上模式修饰符
```
模式修饰符的可选参数:
- i:忽略大小写
- g:全局匹配
- m:多行匹配

#### 三、细数正则表达式的一些规则
我们先来看看RegExp对象的方法：
- test：在字符串中测试模式匹配,返回 true 或 false
- exec：在字符串中执行匹配搜索,返回结果数组
- match(pattern)：返回 pattern 中的子串或 null
- replace(pattern, replacement)：用 replacement 替换 pattern
- search(pattern)：返回字符串中 pattern 开始位置
- split(pattern)：返回字符串按指定 pattern 拆分的数组

我们来看一个简单的示例：
```js
var pattern = /box/i;
var str = 'This is a Box!'; 
alert(pattern.test(str));
```
当然，我们可以简写一下：
```js
alert(/box/i.test('This is a Box!'));
```
{% alert info %}
至于其他的方法，如exec，match，replace用法都大同小异，你需要做的就是：记住他们的规则以及返回值。
{% endalert %}

正则强大之处在于它的匹配，我们来看看匹配规则：
```
.:匹配除换行符外的任意字符
[a-z0-9]:匹配括号中的字符集中的任意字符
[^a-z0-9]:匹配任意不在括号中的字符集中的字符
\d:匹配数字
\D:匹配非数字,同 [^0-9]相同
\w:匹配字母和数字及 _
\W:匹配非字母和数字及 _
\0:匹配 null 字符
\b:匹配空格字符
\f:匹配进纸字符
\n:匹配换行符
\r:匹配回车字符
\t:匹配制表符
\s:匹配空白字符、空格、制表符和换行符
\S:匹配非空白字符

^:行首匹配
$:行尾匹配
\A:只有匹配字符串开始处
\b:匹配单词边界,词在 []内时无效
\B:匹配非单词边界
\G:匹配当前搜索的开始位置
\Z:匹配字符串结束处或行尾
\z:只匹配字符串结束处

x?:匹配 0 个或 1 个 x
x*:匹配 0 个或任意多个 x
x+:匹配至少一个 x
(xyz)+:匹配至少一个 (xyz)
x{m,n}:匹配最少 m 个、最多 n 个 x

this|where|logo:匹配 this 或 where 或 logo 中任意一个
(string):用于反向引用的分组
\1 或$1:匹配第一个分组中的内容
\2 或$2:匹配第二个分组中的内容
\3 或$3:匹配第三个分组中的内容
```
熟记这些规则，我们就可以随心所欲的完成任意匹配了，举个🌰：
```js
/*使用字符类匹配 */
var pattern = /g[a-zA-Z_]*gle/; 
var str = 'google'; 
alert(pattern.test(str));//true
```
是不是很简单呢，赶快去做一些有创造性的事情吧！

#### 四、常用正则积累
验证手机号：
```js
var myreg = /^(((13[0-9]{1})|(15[0-9]{1})|(18[0-9]{1}))+\d{8})$/;
if(!myreg.test($("#phone").val()))
{
    alert('请输入有效的手机号码！');
    return false;
}
```
用正则怎样去除重复的字符串，只保留未重复的。
如：var str= "aaabbbcccddde";
结果为 abcde。
```js
function removeRepeat(str){
	var reg = /(.)(?=.*\1)/g;
	var result = str.replace(reg, "");
	console.log(result);
}
removeRepeat("aaabbbcccddde");//输出abcde
removeRepeat("aaabbbcccdded");//输出abced
```
匹配小数点后面两位：
```js
var s="1，2，234，234.264234，1234.1234";
//不要四舍五入的：
console.log(s.replace(/(\.\d)\d+/ig,"$1") )//1，2，234，234.2，1234.1
//要四舍五入的：
console.log(s.replace(/\d+\.\d+/ig,a))//1，2，234，234.3，1234.1

//此处乱入一个修复小数点后一位的方法：
function a(v){
	return ((v*1).toFixed(1));
}
a(12455.4545)//"12455.5"
```
是否为url：
```js
function isURL(str){
    return!!str.match(/(((^https?:(?:\/\/)?)(?:[-;:&=\+\$,\w]+@)?[A-Za-z0-9.-]+|(?:www.|[-;:&=\+\$,\w]+@)[A-Za-z0-9.-]+)((?:\/[\+~%\/.\w-_]*)?\??(?:[-\+=&;%@.\w_]*)#?(?:[\w]*))?)$/g);
}
 ```
匹配img中的src：
```js
//思路分两步：作者（yanue）.
//1，匹配出图片img标签（即匹配出所有图片），过滤其他不需要的字符
//2.从匹配出来的结果（img标签中）循环匹配出图片地址（即src属性）
var str = "this is test string <img src=\"http:www.yanue.net/test.jpg\" width='50' > 123 and the end <img src=\"所有地址也能匹配.jpg\" /> 33! <img src=\"/uploads/attached/image/20120426/20120426225658_92565.png\" alt=\"\" />"
//匹配图片（g表示匹配所有结果i表示区分大小写）
var imgReg = /<img.*?(?:>|\/>)/gi;
//匹配src属性
var srcReg = /src=[\'\"]?([^\'\"]*)[\'\"]?/i;
var arr = str.match(imgReg);
alert('所有已成功匹配图片的数组：'+arr);
for (var i = 0; i < arr.length; i++) {
    var src = arr[i].match(srcReg);
    //获取图片地址
    if(src[1]){
       alert('已匹配的图片地址'+(i+1)+'：'+src[1]);
    }
    //当然你也可以替换src属性
    if (src[0]) {
        var t = src[0].replace(/src/i, "href");
        //alert(t);
    }
}
```
js获取字符串长度（中文算2个字,英文一个）：
```js
function getStrLength(str) {
    var cArr = str.match(/[^\x00-\xff]/ig);
    return str.length + (cArr == null ? 0 : cArr.length);
}
```
使用正则将金额用逗号隔开：
```js
function fmoney(s, n) {  
    n = n > 0 && n <= 20 ? n : 2;  
    s = parseFloat((s + "").replace(/[^\d\.-]/g, "")).toFixed(n) + "";  
    var l = s.split(".")[0].split("").reverse(), r = s.split(".")[1];  
    t = "";  
    for (i = 0; i < l.length; i++) {  
        t += l[i] + ((i + 1) % 3 == 0 && (i + 1) != l.length ? "," : "");  
    }  
    return t.split("").reverse().join("") + "." + r;  
}  
fmoney(123456.9832,3)//123,456.983
```
还有h5中的用法：
```html
<input type ="text" required ="required" autofocus ="autofocus" pattern ="[a-zA-Z]{6,18}" placeholder ="请输入姓名"/>
<input type ="email" placeholder ="123456@qq.com" />//HTLM5邮箱验证
```

洋洋洒洒码了半天，临近尾声突然想起了一句话：“在最初的几年，你知道的不足以让你明白，你了解的是多么少！”所以，奋斗吧，少年！

参考：
{% alert info %}
正则表达式均来自网上，以下是涉及到的正则表达式引用链接。
{% endalert %}
http://www.daimajiayuan.com/sitejs-18604-1.html
http://blog.sina.com.cn/s/blog_65c2ec5e0101j133.html
http://www.soulteary.com/2014/12/05/better-url-regexp-in-js.html
http://yanue.net/post-11.html
http://blog.csdn.net/evangel_z/article/details/12839657





