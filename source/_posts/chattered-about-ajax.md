---
title: ajax杂谈
date: 2015-09-01 09:32:42
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_3525370C-2C33-4F8B-BD7E-6A9C286F48BF.png
thumbnailImagePosition: left
tags: 
- ajax
comments: true
metaAlignment: center
categories: 面试笔试
---
最近媳妇换工作一直在面试，ajax的相关问题已经占到了70%。作为一个在前端混了3年的老鸟，也经不住各种公司的狂轰乱炸，好吧，那就拿起笔来总结一下。
<!-- more -->
如果你是新手，认真的看完，面试笔试可能会有所帮助。如果你是老手，那么夯实一下基础，也是极好的～

#### 一、ajax是什么？
> AJAX全称为“AsynchronousJavaScript and XML”（异步JavaScript和XML），是一种创建交互式网页应用的网页开发技术。 

Ajax本质上是一个浏览器端的技术。      

- 不是一种新技术，是如下几种技术的组合应用：
- 基于web标准（standards-basedpresentation）XHTML+CSS的表示；
- 使用 DOM（DocumentObject Model）进行动态显示及交互；
- 使用 XML 和 XSLT 进行数据交换及相关操作；
- 使用 XMLHttpRequest 进行异步数据查询、检索；
- 使用 JavaScript将所有的东西绑定在一起。

#### 二、ajax的交互模型
- 如下图：总共分为6个步骤
![ajax交互模型](http://7xvi3w.com1.z0.glb.clouddn.com/blog_79E424FE-BC37-4822-8D36-5F94CD47127E.png)

- 传统的交互模式：
传统的JavaScript编程中，如果想得到服务器端数据库或文件上的信息，或者发送客户端信息到服务器，需要建立一个HTML表单，即Form，然后用GET方法或者POST方法发送数据到服务器端。用户点击Submit按钮来发送或者接收数据信息，然后等待服务器端响应请求，之后就是无操作阶段，直到服务器端数据返回，页面重新加载。由于服务器每次都会返回一个新的页面，所以传统的web应用反应速度极为缓慢且用户体验较差。
![ajax交互模型](http://7xvi3w.com1.z0.glb.clouddn.com/blog_D178FAFC-F34B-4051-9B61-F20A33A8ED3B.png)

- ajax交互模式：
AJAX应用技术则通过HTTP Request发送请求到WEB服务器并接受WEb服务器返回的数据，显示在WEB页面，此过程页面不会重新加载，展示给用户的还是同一个页面。用户体验较为流畅。
![ajax交互模型](http://7xvi3w.com1.z0.glb.clouddn.com/blog_BA2701DA-B51D-4E18-9696-142A5BF0DF9D.png)

#### 三、状态值与状态码

> AJAX状态值是指，运行AJAX所经历过的几种状态，无论访问是否成功都将响应的步骤，可以理解成为AJAX运行步骤。如：正在发送，正在响应等，由AJAX对象与服务器交互时所得；使用“ajax.readyState”获得。（由数字1~4单位数字组成）          

> AJAX状态码是指，无论AJAX访问是否成功，由HTTP协议根据所提交的信息，服务器所返回的HTTP头信息代码，该信息使用“ajax.status”所获得；（由数字1XX,2XX三位数字组成，详细查看RFC）

这就是我们在使用AJAX时为什么采用下面的方式判断所获得的信息是否正确的原因。

```javascript
if(ajax.readyState == 4 && ajax.status == 200) { 
	putData(ajax.responseText);
}
```
#### 四、status
status体现的是服务器对请求的反馈
- 1**：请求收到，继续处理
- 2**：操作成功收到，分析、接受
- 3**：完成此请求必须进一步处理
- 4**：请求包含一个错误语法或不能完成
- 5**：服务器执行一个完全有效请求失败
- 100——客户必须继续发出请求
- 101——客户要求服务器根据请求转换HTTP协议版本
- 200——交易成功
- 201——提示知道新文件的URL
- 202——接受和处理、但处理未完成
- 203——返回信息不确定或不完整
- 204——请求收到，但返回信息为空
- 205——服务器完成了请求，用户代理必须复位当前已经浏览过的文件
- 206——服务器已经完成了部分用户的GET请求
- 300——请求的资源可在多处得到
- 301——删除请求数据
- 302——在其他地址发现了请求数据
- 303——建议客户访问其他URL或访问方式
- 304——客户端已经执行了GET，但文件未变化
- 305——请求的资源必须从服务器指定的地址得到
- 306——前一版本HTTP中使用的代码，现行版本中不再使用
- 307——申明请求的资源临时性删除
- 400——错误请求，如语法错误
- 401——请求授权失败
- 402——保留有效ChargeTo头响应
- 403——请求不允许
- 404——没有发现文件、查询或URl
- 405——用户在Request-Line字段定义的方法不允许
- 406——根据用户发送的Accept拖，请求资源不可访问
- 407——类似401，用户必须首先在代理服务器上得到授权
- 408——客户端没有在用户指定的饿时间内完成请求
- 409——对当前资源状态，请求不能完成
- 410——服务器上不再有此资源且无进一步的参考地址
- 411——服务器拒绝用户定义的Content-Length属性请求
- 412——一个或多个请求头字段在当前请求中错误
- 413——请求的资源大于服务器允许的大小
- 414——请求的资源URL长于服务器允许的长度
- 415——请求资源不支持请求项目格式
- 416——请求中包含Range请求头字段，在当前请求资源范围内没有range指示值，请求也不包含If-Range请求头字段
- 417——服务器不满足请求Expect头字段指定的期望值，如果是代理服务器，可能是下一级服务器不能满足请求
- 500——服务器产生内部错误
- 501——服务器不支持请求的函数
- 502——服务器暂时不可用，有时是为了防止发生系统过载
- 503——服务器过载或暂停维修
- 504——关口过载，服务器使用另一个关口或服务来响应用户，等待时间设定值较长
- 505——服务器不支持或拒绝支请求头中指定的HTTP版本

#### 五、readyStatus
客户端与客户的交互状态过程
> 在AJAX实际运行当中，对于访问XMLHttpRequest（XHR）时并不是一次完成的，而是分别经历了多种状态后取得的结果，对于这种状态在AJAX中共有5种，分别是。

0 - (未初始化)还没有调用send()方法
1 - (载入)已调用send()方法，正在发送请求
2 - (载入完成)send()方法执行完成，
3 - (交互)正在解析响应内容
4 - (完成)响应内容解析完成，可以在客户端调用了

对于上面的状态，其中“0”状态是在定义后自动具有的状态值，而对于成功访问的状态（得到信息）我们大多数采用“4”进行判断。

#### 六、XMLHttpRequest对象的常用方法和属性：
①open("method","URL")建立对服务器的调用。第一个参数是HTTP请求，方式是GET、POST等。第二个参数是请求页面的URL。
②send( )方法，发送具体请求；
③abort( )方法，停止当前请求；
④readyState属性，包含五个状态，0为请求未初始化，1为正在加载，2为已加载，3为交互中，4为完成；
⑤responseText属性，此为服务器的响应，表示为一个串。
⑥responseXML属性，此为服务器的响应，表示为XML。
⑦status属性，服务器的HTTP状态码，200对应的是OK，400错误是页面为找到等等。

#### 七、一个AJAX的简单实例
```javascript
var xmlhttp;
    function loadXMLDoc(url, cfunc){
          if(window.XMLHttpRequest){
            xmlhttp = new XMLHttpRequest();//非IE内核浏览器
          }
          else{
          xmlhttp = new ActiveXObject("Mircrosoft.XMLHTTP");//IE内核浏览器
          }
          xmlhttp.onreadystatechange = cfunc;
          xmlhttp.open("GET",url,true);
          xmlhttp.send();
     }
     function myFunction(){
        loadXMLDoc("/ajax/test1.txt",function(){                         //这里使用callback函数
            if(xmlhttp.readystate ==4 &&xmlhttp.status==200){
             document.getELementById("mydiv").innerHTML = xmlhttp.responseText;
             }
             });
        }
```

```html
<div id="mydiv">ljkjlkjlkj</div>
<button type="button" onclick="muFunction()">通过AJAX改变内容</button>
```


#### 八、AJAX的优点和缺点
- AJAX的优点：
①实现局部动态刷新；
②异步加载，提高客户端浏览器响应速度；
③利用客户端闲置的资源来缓解服务器端的压力；
④不需要使用任何插件，使用方便；
⑤网页操作反应速度快，动态效果十足，用户体验好。
- AJAX的缺点：
①破坏了浏览器的后退按钮，使之无法运行；
解决方案，通过js调用history.go(-1)实现后退功能。自行处理，为用户提供误操作返回入口。
②对搜索引擎不友好；
换句话说，AJXA实现异步加载，也就意味着有一部分内容通过请求后才从服务器接收过来。而搜索引擎则是通过页面抓取进行搜索的，服务器上的东西根本访问不到。
③AJAX的脚本语言是嵌入在HTML页面中的，通过查看源代码或者firebug等工具可直接查看，不利于项目代码的保密；
④不支持跨域请求；
解决方案,JSONP(JSON with Padding,即填充式JSON)。


参考：
http://blog.csdn.net/chenmoquan/article/details/38560649
http://www.itxueyuan.org/view/6454.html
http://www.jb51.net/article/16966.htm
http://blog.csdn.net/libertea/article/details/11059425