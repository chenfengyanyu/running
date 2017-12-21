---
title: HTTP协议入门
date: 2016-08-04 11:02:45
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/http_003F8BF9-89C8-445A-AF1C-5B038BDE6D35.png
thumbnailImagePosition: left
tags: 
- http
comments: true
metaAlignment: center
categories: 技术博文
---
作为一个web前端开发工程师，浏览器恐怕比媳妇还要亲吧。那么这位朝夕相处的第三者你到底了解多少，关于人家的聊天语言－http协议，你又懂多少？
<!-- more -->
别再拿200，404，503来说事了，这跟只会say hello有啥区别。让我们开启狂暴模式，疯狂的撩妹吧！

#### 一、关于HTTP
HTTP(HyperText Transfer Protocol)是一套计算机通过网络进行通信的规则，使HTTP客户端（如Web浏览器）能够从HTTP服务器(Web服务器)请求信息和服务。

- HTTP是一种无状态的协议
 - 无状态是指Web浏览器和Web服务器之间不需要建立持久的连接，这意味着当一个客户端向服务器端发出请求，然后Web服务器返回响应(response)，连接就被关闭了，在服务器端不保留连接的有关信息。
- HTTP遵循请求(Request)/应答(Response)模型
 - Web浏览器向Web服务器发送请求，Web服务器处理请求并返回适当的应答。所有HTTP连接都被构造成一套请求和应答。
- HTTP使用内容类型
 - 是指Web服务器向Web浏览器返回的文件都有与之相关的类型。所有这些类型在MIME Internet邮件协议上模型化，即Web服务器告诉Web浏览器该文件所具有的种类，是HTML文档、GIF格式图像、声音文件还是独立的应用程序。

#### 二、HTTP通信机制
一次完整的HTTP通信过程中，Web浏览器与Web服务器之间将完成下列7个步骤：

①建立TCP连接
在HTTP工作开始之前，Web浏览器首先要通过网络与Web服务器建立连接，该连接是通过TCP来完成的，该协议与IP协议共同构建Internet，即著名的TCP/IP协议族，因此Internet又被称作是TCP/IP网络。HTTP是比TCP更高层次的应用层协议，根据规则，只有低层协议建立之后，才能进行更层协议的连接，因此，首先要建立TCP连接，一般TCP连接的端口号是80

②Web浏览器向Web服务器发送请求命令
一旦建立了TCP连接，Web浏览器就会向Web服务器发送请求命令
例如：GET/sample/hello.jsp HTTP/1.1

③Web浏览器发送请求头信息
浏览器发送其请求命令之后，还要以头信息的形式向Web服务器发送一些别的信息，之后浏览器发送了一空白行来通知服务器，它已经结束了该头信息的发送。

④Web服务器应答
客户机向服务器发出请求后，服务器会客户机回送应答，
HTTP/1.1 200 OK
应答的第一部分是协议的版本号和应答状态码

⑤Web服务器发送应答头信息
正如客户端会随同请求发送关于自身的信息一样，服务器也会随同应答向用户发送关于它自己的数据及被请求的文档。

⑥Web服务器向浏览器发送数据
Web服务器向浏览器发送头信息后，它会发送一个空白行来表示头信息的发送到此为结束，接着，它就以Content-Type应答头信息所描述的格式发送用户所请求的实际数据

⑦Web服务器关闭TCP连接
一般情况下，一旦Web服务器向浏览器发送了请求数据，它就要关闭TCP连接，然后如果浏览器或者服务器在其头信息加入了这行代码
```
Connection:keep-alive
```
TCP连接在发送后将仍然保持打开状态，于是，浏览器可以继续通过相同的连接发送请求。保持连接节省了为每个请求建立新连接所需的时间，还节约了网络带宽。

#### 三、HTTP请求／应答格式
打开chrome浏览器的开发者工具，我们来看一下http的头文件。代码有点多，为了避免辣眼睛，我来做一个拆分，咱们逐一来看：
{% alert info %}
General部分：
{% endalert %}
```
Request URL:http://jartto.wang/usr/GetMicWebImgs.do?name=jartto&ida=1
Request Method:GET
Status Code:200 OK
```
这里主要是请求链接，请求方式以及响应状态码。

{% alert info %}
Response Headers部分：
{% endalert %}
```
Connection:Keep-Alive
Content-Length:58
Content-Type:application/json
Date:Thu, 04 Aug 2016 06:01:46 GMT
Keep-Alive:timeout=5, max=100
Server:HAP/Vertx
```
1.Connection表示是否需要持久连接，(HTTP1.1默认进行持久连接)。如：Connection：close，Connection：keep-Alive，表示持久连接。
2.Content-Length表示内容长度。只有当浏览器使用持久HTTP连接时才需要这个数据。如果你想要利用持久连接的优势，可以把输出文档写入ByteArrayOutputStram，完成后查看其大小，然后把该值放入Content-Length头，最后通过 byteArrayStream.writeTo(response.getOutputStream()发送内容。
3.Content-Type实体头用于向接收方指示实体的介质类型，指定HEAD方法送到接收方的实体介质类型，或GET方法发送的请求介质类型 Content-Range实体头
4.Date头域表示消息发送的时间，Date描述的时间表示世界标准时，换算成本地时间，需要知道用户所在的时区。 
5.Keep-Alive中timeout：过期时间5秒（对应httpd.conf里的参数是：KeepAliveTimeout），就是在timeout时间内又有新的连接过来，同时max会自动减1，直到为0，强制断掉。
6.Server响应头包含处理请求的原始服务器的软件信息。此域能包含多个产品标识和注释，产品标识一般按照重要性排序。 

{% alert info %}
Request Headers部分：
{% endalert %}
```
Accept:*/*
Accept-Encoding:gzip, deflate, sdch
Accept-Language:zh-CN,zh;q=0.8,en;q=0.6
Cache-Control:no-cache
Connection:keep-alive
Host:jartto.wang
Pragma:no-cache
Referer:http://jartto.wang/
User-Agent:Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13B143 Safari/601.1
```
1.Accept请求报头域用于指定客户端接收哪些类型的信息。
2.Accept-Encoding指定浏览器可以支持的web服务器返回内容压缩编码类型。
3.Accept-Language指定客户端可以接受的语言。
4.Cache-Control指定请求和响应遵循的缓存机制。在请求消息或响应消息中设置 Cache-Control并不会修改另一个消息处理过程中的缓存处理过程。请求时的缓存指令包括no-cache、no-store、max-age、 max-stale、min-fresh、only-if-cached，响应消息中的指令包括public、private、no-cache、no-store、no-transform、must-revalidate、proxy-revalidate、max-age。[详情](http://www.cnblogs.com/9988/archive/2012/03/21/2409086.html)
5.Connection同响应头中Connection
6.Host头域指定请求资源的Intenet主机和端口号，必须表示请求url的原始服务器或网关的位置。HTTP/1.1请求必须包含主机头域，否则系统会以400状态码返回。
7.Pragma头域用来包含实现特定的指令，最常用的是Pragma:no-cache。在HTTP/1.1协议中，它的含义和Cache-Control:no-cache相同。
8.Referer 头域允许客户端指定请求uri的源资源地址，这可以允许服务器生成回退链表，可用来登陆、优化cache等。他也允许废除的或错误的连接由于维护的目的被 追踪。如果请求的uri没有自己的uri地址，Referer不能被发送。如果指定的是部分uri地址，则此地址应该是一个相对地址。
9.User-Agent头域的内容包含发出请求的用户信息。 

{% alert info %}
Query String Parameters部分：
{% endalert %}
```
name:jartto
ida:1
```
参数部分最好理解了，这里就不赘述了。
#### 四、 HTTP应答码
HTTP应答码也称为状态码，它反映了Web服务器处理HTTP请求状态。HTTP应答码由3位数字构成，其中首位数字定义了应答码的类型：
- 1XX
信息类(Information),表示收到Web浏览器请求，正在进一步的处理中
- 2XX
成功类（Successful）,表示用户请求被正确接收，理解和处理例如：200 OK
- 3XX
重定向类(Redirection),表示请求没有成功，客户必须采取进一步的动作。
- 4XX
客户端错误(Client Error)，表示客户端提交的请求有错误，例如：404 NOT Found，意味着请求中所引用的文档不存在。
- 5XX-服务器错误(Server Error)表示服务器不能完成对请求的处理：如500对于我们Web开发人员来说掌握HTTP应答码有助于提高Web应用程序调试的效率和准确性。

{% alert warning %}
我实在是太懒了，如果你想详细的了解每个应答码的信息，请这边走[HTTP状态码大全](http://www.cnblogs.com/lxinxuan/archive/2009/10/22/1588053.html)
{% endalert %}

参考文章：
http://www.cnblogs.com/yin-jingyu/archive/2011/08/01/2123548.html
http://www.cnblogs.com/9988/archive/2012/03/21/2409086.html
http://blog.csdn.net/u012935755/article/details/51861514
http://www.cppblog.com/woaidongmao/archive/2009/09/08/95544.aspx
http://www.cnblogs.com/lxinxuan/archive/2009/10/22/1588053.html