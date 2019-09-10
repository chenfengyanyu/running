---
title: 掌握 HTTP2.0
date: 2018-03-30 07:39:01
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/http2.png
thumbnailImagePosition: left
tags: 
- http2
comments: false
metaAlignment: center
categories: 技术博文
---
提到 HTTP2.0 ，第一印象就是：这与 HTTP1.0 的区别是什么，有怎样的改进？大的版本变化必然会有更强力的特性，那不妨拿来了解一下。
<!-- more -->
#### 一、先了解 HTTP1.0 和 HTTP1.1
我们先撇开 `HTTP2` 和 `HTTP1` 的区别，来说说 `HTTP1.0` 和 `HTTP1.1` 的区别，因为它俩本身就存在一些大的区别：

- 长连接
`HTTP 1.0` 需要使用 `keep-alive` 参数来告知服务器端要建立一个长连接，而 `HTTP1.1` 默认支持长连接。
- 节约带宽
`HTTP 1.1` 支持只发送 `header` 信息(不带任何 `body` 信息)，如果服务器认为客户端有权限请求服务器，则返回 100，否则返回 401。客户端如果接受到 100，才开始把请求 `body` 发送到服务器。
- HOST 域
现在可以 `web server` 例如 `tomat`，设置虚拟站点是非常常见的，也即是说，`web server` 上的多个虚拟站点可以共享同一个 `ip` 和端口。`HTTP1.0` 是没有 `host` 域的，`HTTP1.1` 才支持这个参数。

更多细节请[参考](https://blog.csdn.net/linsongbin1/article/details/54980801)，还有之前的一篇文章 [HTTP 协议入门](http://jartto.wang/2016/08/04/Rudimentary-http-protocol/)。

#### 二、HTTP2 新特性
{% alert warning %}
众所周知 ，在 `HTTP1.1` 协议中 「浏览器客户端在同一时间，针对同一域名下的请求有一定数量限制。超过限制数目的请求会被阻塞」，也就是我们常说的 `Pending` 状态。
{% endalert %}

提到 `HTTP2` 的新特性，有三个特性肯定是亮点：
1.多路复用：多路复用允许同时通过单一的 `HTTP2` 连接发起多重的请求-响应消息先来看一张[流程对比图](https://www.zhihu.com/question/34074946)。
![diff](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/http2/diff.png)
因此 `HTTP2` 可以很容易的去实现多流并行而不用依赖建立多个 `TCP` 连接，`HTTP2` 把 `HTTP` 协议通信的基本单位缩小为一个一个的帧，这些帧对应着逻辑流中的消息。并行地在同一个 `TCP` 连接上。

{% alert success %}
这样来说的话，多路复用也为前端性能优化提供了更高效的方式。
{% endalert %}

2.首部数据压缩
`HTTP1.1` 不支持 `header` 数据的压缩，`HTTP2.0` 使用 `HPACK` 算法对 `header` 的数据进行压缩，这样数据体积小了，在网络上传输就会更快。

{% alert info %}
HTTP1.1 并不支持 HTTP 首部压缩，为此 SPDY 和 HTTP/2 应运而生， SPDY 使用的是通用的 DEFLATE  算法，而 HTTP2 则使用了专门为首部压缩而设计的。
{% endalert %}

3.服务器推送
当我们对支持 `HTTP2.0` 的 `web server` 请求数据的时候，服务器会顺便把一些客户端需要的资源一起推送到客户端，免得客户端再次创建连接发送请求到服务器端获取。这种方式非常合适加载静态资源。

引用 [HTTP/2.0 相比 1.0 有哪些重大改进](https://www.zhihu.com/question/34074946) 中的描述：
服务端推送是一种在客户端请求之前发送数据的机制。在 `HTTP/2` 中，服务器可以对客户端的一个请求发送多个响应。`Server Push` 让 `HTTP1.x` 时代使用内嵌资源的优化手段变得没有意义；

如果一个请求是由你的主页发起的，服务器很可能会响应主页内容、logo 以及样式表，因为它知道客户端会用到这些东西。这相当于在一个 HTML 文档内集合了所有的资源。

不过与之相比，服务器推送还有一个很大的优势：可以缓存！也让在遵循同源的情况下，不同页面之间可以共享缓存资源成为可能。

{% alert info %}
至于 HPACK 算法是什么鬼，这里就不扩展了，感兴趣的童鞋可以走这里[HPACK 完全解析](https://www.jianshu.com/p/f44b930cfcac)
{% endalert %}

#### 三、关于 HTTP2 性能
说的天花乱坠，HTTP2 不但能多路复用，而且还可以压缩头部数据，以及做一些服务器端推送。那么，HTTP2 真正的性能到底如何呢？我们来看看[深入研究：HTTP2 的真正性能到底如何？](https://segmentfault.com/a/1190000007219256)

{% alert success %}
文章做了一些验证试验，这里就不赘述了，我们来看一下试验结果，有一个感性认识。
{% endalert %}

通过上述一系列的实验，我们可以知道 HTTP2 的性能优势集中体现在“多路复用”和“服务端推送”上。对于请求数目较少（约小于30个）的情况下，`HTTP1` 和 `HTTP2` 的性能差异不大，在请求数目较多且延迟大于 30ms 的情况下，才能体现 `HTTP2` 的性能优势。对于网络状况较差的环境，`HTTP2` 的性能也高于 `HTTP1`。与此同时，如果把静态资源都通过服务端推送的方式来处理，加载速度会得到更加巨大的提升。

在实际的应用中，由于 `HTTP2` 多路复用的优势，前端应用团队无须采取把多个文件合并成一个，生成雪碧图之类的方法减少网络请求。除此之外，`HTTP2` 对于前端开发的影响并不大。

{% alert info %}
对于网络状况较差的环境，`HTTP2` 的性能也高于 `HTTP1`。
{% endalert %}

#### 四、HTTP2 加载效果演示
有个在线的 [DMEO](https://http2.akamai.com/demo) 可以直观的感受一下 `HTTP2` 的实际效果。

{% alert success %}
可以看出 `HTTP1.1` 和 `HTTP2` 在比较接近的服务器延迟条件下，`HTTP2` 的加载速度明显要优于 `HTTP1`。
{% endalert %}

#### 五、扩展：HTTP POST 请求为什么会有一个 OPTION ？
正如上面问题所说，当你打开 `Chrome` 开发工具或者其他抓包工具的时候，会发现一条 `POST` 请求输出两次，第一次状态显示 `OPTION`。

[文档官方](https://blog.csdn.net/leikezhu1981/article/details/7402272)是这么定义的：
- `OPTIONS` 方法是用于请求获得由 `Request-URI` 标识的资源在请求/响应的通信过程中可以使用的功能选项。
- 该请求方法的响应不能缓存。
- 如果这个 `OPTIONS` 请求包含一个正文（有 `Content-Length` 或 `Transfer-Encoding` 存在），则必须有`Content-Type` 来指定媒体类型。虽然规范里没有定义这种正文的用法，但是 `HTTP` 将来的扩展可能会用它来查询服务器上更详细的信息。不支持该扩展的服务器可以忽略该请求正文。
- 如果该 `URI` 是一个星号（“*”），`OPTIONS` 请求将试图应用于服务器，而不是某个指定资源。由于服务器的通信选项通常依赖于资源，所以此“*”请求只能作为“ping”或者“no-op”方法；或者用来测试服务器的性能。例如，用来测试 `HTTP/1.1` 代理。
- 如果该 `URI` 不是星号，则只能用来获取该资源通信中可用的选项。
- 得到的 `200` 响应应该包含一个头域，指明服务器实现的和适用于该资源的可选特征（如：`Allow`），可能还包括该规范尚未定义的扩展。如果有响应正文，则应包含关于通信选项的信息。本规范没有定义该正文格式，但可能在 `HTTP` 将来的扩展中定义。可以利用内容协商来选择合适的响应格式。如果没有响应正文，响应必须包含 `Content-Length`，并且值为“0”。
- 请求头的 `Max-Forwards` 用来请求特定代理。当代理收到一个允许 `URI` 转发的 `OPTIONS` 请求，则检查`Max-Forwards`。如果 `Max-Forwards` 值为0，则不能转发该消息；相反，代理会将自己的通信选项去响应。如果`Max-Forwards` 是正整数，代理转发请求的时候会将该值减1。如果请求中没有 `Max-Forwards`，转发的请求也不会有。

{% alert success %}
通过这个方法，客户端可以在采取具体资源请求之前，决定对该资源采取何种必要措施，或者了解服务器的性能。
{% endalert %}

根据上面描述，我们可以列举出 OPTION 的主要功能：
其一：获取服务器支持的 `HTTP` 请求方法；
其二：用来检查服务器的性能。

{% alert info %}
值得注意的是：其实 HTTP 请求还分为两类：普通请求和简单请求。
{% endalert %}

那么到这里，大家已经了然于胸了吧！`OPTIONS` 请求旨在发送一种“探测”请求以确定针对某个目标地址的请求必须具有怎样的约束（比如应该采用怎样的HTTP方法以及自定义的请求报头），然后根据其约束发送真正的请求。比如针对“跨域资源”的预检（Preflight）请求采用的 `HTTP` 方法就是 `OPTIONS`。

好了，就说到这里，了解更多请移步[ W3C ](https://www.w3.org/TR/cors/#cross-origin-request-with-preflight-0)官方文档。

#### 六、扩展：如何理解 HTTP 中「三次握手」和「四次挥手」 ？
{% alert success %}
众所周知，`HTTP` 底层协议使用了 `TCP/IP` 协议，当我们想要了解细节的时候，这个问题始终绕不开。
{% endalert %}

我们先简单回顾一下 `HTTP` 的[请求过程](http://jartto.wang/2016/08/04/Rudimentary-http-protocol/)：
①建立 `TCP` 连接；
②`Web` 浏览器向 `Web` 服务器发送请求命令；
③`Web` 浏览器发送请求头信息；
④`Web` 服务器应答；
⑤`Web` 服务器发送应答头信息；
⑥`Web` 服务器向浏览器发送数据；
⑦`Web` 服务器关闭 `TCP` 连接；

中间的 ②--⑥ 过程都很明确，那 `TCP` 是如何建立连接的呢？
TCP(Transmission Control Protocol)　传输控制协议，是主机对主机层的传输控制协议，提供可靠的连接服务，采用三次握手确认建立一个连接:

- 第一次握手：建立连接时，客户端 `A` 发送 `SYN` 包 `（SYN=j）` 到服务器 `B`，并进入 `SYN_SEND` 状态，等待服务器 `B` 确认。
- 第二次握手：服务器 `B` 收到 `SYN` 包，必须确认客户 `A` 的 `SYN（ACK=j+1）`，同时自己也发送一个 `SYN` 包 `（SYN=k）`，即 `SYN+ACK` 包，此时服务器 `B` 进入 `SYN_RECV` 状态。
- 第三次握手：客户端 `A` 收到服务器 `B` 的 `SYN＋ACK` 包，向服务器 `B` 发送确认包 `ACK（ACK=k+1）`，此包发送完毕，客户端 `A` 和服务器 `B` 进入 `ESTABLISHED` 状态，完成三次握手。

{% alert info %}
完成三次握手，客户端与服务器开始传送数据，中间过程请参考[请求过程](http://jartto.wang/2016/08/04/Rudimentary-http-protocol/)：
{% endalert %}

那么「四次挥手」又是怎么回事？
由于 `TCP` 连接是全双工的，因此每个方向都必须单独进行关闭。这个原则是当一方完成它的数据发送任务后就能发送一个 `FIN` 来终止这个方向的连接。收到一个 `FIN` 只意味着这一方向上没有数据流动，一个 `TCP` 连接在收到一个 `FIN` 后仍能发送数据。首先进行关闭的一方将执行主动关闭，而另一方执行被动关闭。

`TCP` 的连接的拆除需要发送四个包，因此称为「四次挥手」(four-way handshake)。客户端或服务器均可主动发起挥手动作，在 `socket` 编程中，任何一方执行 `close()` 操作即可产生挥手操作。

- 客户端 `A` 发送一个 `FIN`，用来关闭客户 `A` 到服务器 `B` 的数据传送。 
- 服务器 `B` 收到这个 `FIN` ，它发回一个 `ACK` ，确认序号为收到的序号加 1。和 `SYN` 一样，一个 `FIN` 将占用一个序号。 
- 服务器 `B` 关闭与客户端 `A` 的连接，发送一个 `FIN` 给客户端 `A` 。 
- 客户端 `A` 发回 `ACK` 报文确认，并将确认序号设置为收到序号加 1。 

{% alert success %}
当然，整个过程并没有这么简单，这里就不继续深入了。
{% endalert %}

补充说明：TCP 标志位,有 6 种标示:
- SYN(synchronous建立联机)
- ACK(acknowledgement 确认)
- PSH(push传送)
- FIN(finish结束)
- RST(reset重置)
- URG(urgent紧急)
- Sequence number(顺序号码)
- Acknowledge number(确认号码)

详细过程，请参考：
- [Http 协议三次握手过程](http://blog.163.com/wangzhenbo85@126/blog/static/1013632822013423502833/?suggestedreading&wumii)。
- [TCP 的三次握手(建立连接）和四次挥手(关闭连接）](https://www.cnblogs.com/Jessy/p/3535612.html)

#### 七、扩展阅读
- [7 Tips for Faster HTTP/2 Performance](https://www.nginx.com/blog/7-tips-for-faster-http2-performance/)
- [HTTP2 讲解](https://legacy.gitbook.com/book/ye11ow/http2-explained/details)
- [A Simple Performance Comparison of HTTPS, SPDY and HTTP/2](http://blog.httpwatch.com/2015/01/16/a-simple-performance-comparison-of-https-spdy-and-http2/)
- [HPACK: Header Compression for HTTP/2](http://httpwg.org/specs/rfc7541.html)
- [HTTP/2 for Web Application Developers](https://www.nginx.com/wp-content/uploads/2015/09/NGINX_HTTP2_White_Paper_v4.pdf)
- [HTTP/2 for a Faster Web](https://cascadingmedia.com/insites/2015/03/http-2.html)
- [O'Reilly HTTP2-high-perf-browser-networking](http://www.oreilly.com/webops-perf/free/files/HTTP2-high-perf-browser-networking.pdf)
- [HTTP/2 的系列文章](http://www.unclekevin.org/?cat=15)
- [一分钟预览 HTTP2 特性和抓包分析](#)
- [HTTP,HTTP2.0,SPDY,HTTPS你应该知道的一些事](#)
- [HTTP/2 新特性浅析](http://io.upyun.com/2015/05/13/http2/)