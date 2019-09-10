---
title: jquery之cookie详解
date: 2016-07-02 18:07:37
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/cookie_0B2EC51C.png
thumbnailImagePosition: left
tags: 
- jquery
- cookie
comments: true
metaAlignment: center
categories: 技术博文
---
前两天搞定了一个拖拽存储的h5控件，写了一个总结，其中用到了cookie存储。结果有同事问我关于cookie的问题，我竟然说不出所以然来。纯粹是指哪打哪，用哪写哪，根本就没有完全搞明白。
<!--more-->
想起了一句歌词：“他说他会打野，真的只会打野”，熟悉LOL的同学应该不会陌生😄

想想也是，这样的学习根本无效率可言。所以花些时间把cookie搞明白，也把jquery cookie 函数参数列表列举出来，同时补充些新学到的东西吧，算是共同学习吧。

#### 一、cookie是什么？
①关于cookie；
cookie，有时也用其复数形式cookies，指某些网站为了辨别用户身份、进行session跟踪而储存在用户本地终端上的数据（通常经过加密）。
cookie是由服务器端生成，发送给User-Agent（一般是浏览器），浏览器会将Cookie的key/value保存到某个目录下的文本文件内，下次请求同一网站时就发送该cookie给服务器（前提是浏览器设置为启用cookie）。

②cookie能做什么；
服务器可以利用cookies包含信息的任意性来筛选并经常性维护这些信息，以判断在HTTP传输中的状态。
{% alert info %}
cookies最典型的应用是判定注册用户是否已经登录网站，用户可能会得到提示，是否在下一次进入此网站时保留用户信息以便简化登录手续，这些都是cookies的功用。另一个重要应用场合是“购物车”之类处理。用户可能会在一段时间内在同一家网站的不同页面中选择不同的商品，这些信息都会写入cookies，以便在最后付款时提取信息。
{% endalert %}
③cookie很完美吗？有利也有弊，cookie也有一些众所周知的缺点。
其一，cookie只能存储少量数据，，一般来说，设置大约4KB的数据，这意味着它不能接受文件或邮件那样的大数据；

其二，只要有请求涉及cookie，cookie就要在服务器和浏览器之间来回传送。也就是说，cookie数据在网络上是可见的，这些数据在不加密的情况下会有安全隐患。同时，无论加载哪个相关的URL，cookie中的数据都会消耗网络带宽。

#### 二、jquery cookie插件如何使用？
应用jquery cookie插件需要引入jquery.cookie.js,当然jquery.js也必不可少。简单的用法类似这样，存值：
```js
$.cookie('flag','true',{path:'/',expires:1});
```
取值：
```js
$.cookie('flag');
```
[下载地址](http://plugins.jquery.com/cookie/)

#### 三、cookie函数参数列表
下面是对cookie的一些操作：
```js
$.cookie('key');                   // 获取cookie key的值
$.cookie('key', 'value');     // 设置cookie，属性key=value
$.cookie('key', 'value', { expires: num });  // 设置过期时间为num，单位为天，负数的话，效果等同删除该cookie
$.cookie('key', null);         // 删除 cookie
$.cookie('key', 'value', {expires: num, path: '/', domain: domain, secure: true}); //新建一个cookie 包括有效期、存储路径、域名、安全等
```

#### 四、cookie与Html5 Web Storage API
①区别
正因为cookie是如此的实用、方便，类似cookie的HTML5 Web Storage应运而生，比起单纯的cookie来说，HTML5 Web Storage拥有更多的优点。
许多情况下，即使不使用网络或远程服务器也能达到同样的目的，开发者可以将数据存储在JavaScript对象中，对象在页面加载时保存，并且容易获取。通过sessionStorage或localStorage，在打开新窗口或新标签以及重新启动浏览器时，开发人员可以选择是否激活这些数据。此外，使用Web Storage API可以保存高达数兆字节的大数据。
在HTML5的各项特性中，Web Storage的浏览器支持度是非常好的，而且HTML5 Web Storage已经逐渐成为Web应用中最安全的API之一了。

②检测是否支持Web Storage
通过window.sessionStorage和window.localStorage可以检测浏览器是否支持Web Storage。

③Web Storage API设置与取值
设置数据：
```js
window.sessionStorage.setItem('Key','Value');
```
获取数据：
```js
window.sessionStorage.getItem('Key');
```
当然Web Storage API还有很多函数和其他特性，这里就不一一列举了。

#### 五、一个demo
写了一个简单的应用，主要是检测IE7浏览器，如果是的话，弹出一个提示框。提示框有关闭按钮，当用户点击该按钮后，一段时间内将不再提示，具体代码如下：
```js
var flag = false;
$(function(){
	if(navigator.appVersion.match(/7./i) == '7.'){
		sildOut();
	}
});

function sildOut(){
	var temp = $.cookie('flag');
	if(!temp){
		$('.outside').show(1500);
	}
}

function closeDiv(){
	$('.outside').hide(1000);
	$.cookie('flag','true',{path:'/',expires:0.5});
}
```
参考文章：
①http://baike.baidu.com/link?url=zxrX-UXaMVK5BL3sCdmsTQVN7Ys0rTKWOOkgdy-ur1k2zPybEmMBOKnp-W_5qbnw3ulrDmesXtn3QgAZtnbE3i1OeZ7W1W8Ml_psgK-1C2e

②http://www.nowamagic.net/jquery/jquery_Jquerycookie.php

③HTML5 高级程序设计










