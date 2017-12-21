---
title: CORS解决方案
date: 2016-06-27 15:03:14
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/cors_07E8A26B-7B79-44FE-891E-22AB563CD157.png
thumbnailImagePosition: left
tags: 
- cors
comments: true
metaAlignment: center
categories: 技术博文
---
同源策略（Same origin policy）是一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。可以说Web是构建在同源策略基础之的，浏览器只是针对同源策略的一种实现。
<!-- more -->
{% alert success %}
主机名+协议+端口三者相同，被认为是同源。
{% endalert %}

#### 一、问题重现
![cors](http://7xvi3w.com1.z0.glb.clouddn.com/cors_FD8DA08A-E4CC-45CB-9E76-9D4EF37AEEB9.png)
![cors](http://7xvi3w.com1.z0.glb.clouddn.com/cors_1FBAD133-35C5-4AC4-8329-B3B433A062BA.png)

一般出现以上错误，基本可以认定，网站出现了同源策略问题，也就是出现了未经允许的跨域请求。那么如何解决此类问题呢，下面我来一一阐述。


#### 二、解决方案
①客户端和服务器设置
这里以iis服务器为例，[更多请移步](http://enable-cors.org/server.html)
虽然都是iis服务器，但是版本的不同，设置也不尽相同。
对于iis6：
打开iis管理器，选择需要设置CORS的站点，右键属性；
选择HTTP Headers 页签；
点击添加按钮，输入命令，点击确定；
```
Access-Control-Allow-Origin：*
```
对于iis7：
新建文件命名：web.config
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
<system.webServer>
   <httpProtocol>
     <customHeaders>
       <add name="Access-Control-Allow-Origin" value="*" />
     </customHeaders>
   </httpProtocol>
</system.webServer>
</configuration>
```

②JSONP
{% alert success %}
JSONP(JSON with Padding)是JSON的一种“使用模式”，可用于解决主流浏览器的跨域数据访问的问题。通俗的讲，JSONP就是动态的去加载js文件，为其src属性指定一个跨域URL。
{% endalert %}

```js
<script type="text/javascript">
    function showPrice(data){
        alert("Symbol:" + data.symbol + ", Price:" + data.price);
    }
    var url = "ticker.js"; //Outer JS URL
    var script = document.createElement('script');
    script.setAttribute('src', url);
    //load javascript
    document.getElementsByTagName('head')[0].appendChild(script);
</script>
```
ticker.js中
```js
var data = {symbol:'IBM', price:100};
showPrice(data);
```
{% alert info %}
缺点：JSONP是get形式的，承载的信息量有限。
{% endalert %}

#### 三、本地测试如何进行
当然，如果我们仅仅是为了进行本地测试，那么大可不必如此麻烦，看看如下的设置吧。
首先，我们先来看windows系统下的设置：桌面创建Chrome，右键属性，在目标栏空格后输入 
```
--disable-web-security
```
![cors](http://7xvi3w.com1.z0.glb.clouddn.com/cors_CFE74C5D-058B-468C-9D0A-8AD9931214B9.png)
打开Chrome浏览器，出现以下警告，则配置成功。下面就可以进行轻松愉快的本地开发测试啦！
![cors](http://7xvi3w.com1.z0.glb.clouddn.com/cors_Image.png)

{% alert info %}
注意：此处打开的浏览器一定是你配置好属性的快捷方式，否则可能无效哦！
{% endalert %}

Mac下就简单多了，我们需要输入以下命令：
```sh
#老版本：
open -a "Google Chrome" --args --disable-web-security
#新版本：
open -a "Google Chrome" --args --disable-web-security --user-data-dir
```
{% alert info %}
很多人发现Mac版本的CORS不起作用了，可能的原因就是你的chrome浏览器升级了，需要新的命令来禁用CORS。
{% endalert %}

#### 四、补充
CORS与JSONP相比，更为先进、方便和可靠。
① JSONP只能实现GET请求，而CORS支持所有类型的HTTP请求。
②使用CORS，开发者可以使用普通的XMLHttpRequest发起请求和获得数据，比起JSONP有更好的错误处理。
③JSONP主要被老的浏览器支持，它们往往不支持CORS，而绝大多数现代浏览器都已经支持了CORS。

对一个简单的请求，没有自定义头部，要么使用GET，要么使用POST，它的主体是text/plain,请求用一个名叫Orgin的额外的头部发送。Origin头部包含请求页面的头部（协议，域名，端口），这样服务器可以很容易的决定它是否应该提供响应。

服务器端对于CORS的支持，主要就是通过设置Access-Control-Allow-Origin来进行的。
```
Header set Access-Control-Allow-Origin *
```
为了防止XSS攻击我们的服务器， 我们可以限制域，比如
```
Access-Control-Allow-Origin: http://jartto.wang/
```
很多服务都已经提供了CORS支持，比如 AWS 支持跨域资源分享功能CORS，向S3上传不需要代理。

#### 五、CORS相关资源推荐
[1.官方网站](http://enable-cors.org/index.html)
[2.CORS(Cross-Origin Resource Sharing) 跨域资源共享](http://my.oschina.net/dongwq/blog/137147)
[3.跨域请求解决方法(JSONP, CORS)](http://blog.csdn.net/cloudcraft/article/details/8986798)
[4.ajax post&跨域解决方案](http://www.360doc.com/content/14/0106/11/15643_343005039.shtml)
[5.html5安全：CORS（跨域资源共享）简介](http://blog.csdn.net/hfahe/article/details/7730944)

参考：
百度百科：http://baike.baidu.com/cors
Enable CORS：http://enable-cors.org/index.html
跨域请求解决方法：http://blog.csdn.net/cloudcraft/article/details/8986798
javascript 之 跨域技术 CORS：http://www.xuebuyuan.com/1744068.html
http://stackoverflow.com/questions/35432749/disable-web-security-in-chrome-48-and-49












