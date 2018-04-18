---
title: 掌握 HTTP2.0
date: 2018-03-30 07:39:01
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/http2.png
thumbnailImagePosition: left
tags: 
- http2
comments: false
metaAlignment: center
categories: 技术博文
---
提到 HTTP2.0 ，第一印象就是：这与 HTTP1.0 的区别是什么，有怎样的改进？大的版本变化必然会有更强力的特性，那不妨拿来了解一下。
<!-- more -->
#### 一、http1.0 和 http1.1
-长连接
HTTP 1.0需要使用keep-alive参数来告知服务器端要建立一个长连接，而HTTP1.1默认支持长连接。
-节约带宽
HTTP 1.1支持只发送header信息(不带任何body信息)，如果服务器认为客户端有权限请求服务器，则返回100，否则返回401。客户端如果接受到100，才开始把请求body发送到服务器。
-HOST域
现在可以web server例如tomat，设置虚拟站点是非常常见的，也即是说，web server上的多个虚拟站点可以共享同一个ip和端口。HTTP1.0是没有host域的，HTTP1.1才支持这个参数。

更多细节请[参考](https://blog.csdn.net/linsongbin1/article/details/54980801)

#### 二、http2.0 新特性
-多路复用
-数据压缩
HTTP1.1不支持header数据的压缩，HTTP2.0使用HPACK算法对header的数据进行压缩，这样数据体积小了，在网络上传输就会更快。
-服务器推送
当我们对支持HTTP2.0的web server请求数据的时候，服务器会顺便把一些客户端需要的资源一起推送到客户端，免得客户端再次创建连接发送请求到服务器端获取。这种方式非常合适加载静态资源。

#### 三、深入研究：HTTP2 的真正性能到底如何
https://segmentfault.com/a/1190000007219256
通过上述一系列的实验，我们可以知道http2的性能优势集中体现在“多路复用”和“服务端推送”上。对于请求数目较少（约小于30个）的情况下，http1.x和http2的性能差异不大，在请求数目较多且延迟大于30ms的情况下，才能体现http2的性能优势。对于网络状况较差的环境，http2的性能也高于http1.x。与此同时，如果把静态资源都通过服务端推送的方式来处理，加载速度会得到更加巨大的提升。

在实际的应用中，由于http2多路复用的优势，前端应用团队无须采取把多个文件合并成一个，生成雪碧图之类的方法减少网络请求。除此之外，http2对于前端开发的影响并不大。

#### 四、HTTP/2.0 相比1.0有哪些重大改进？
https://www.zhihu.com/question/34074946

#### 五、http2 加载效果演示
https://http2.akamai.com/demo
回顾 HTTP1.0
