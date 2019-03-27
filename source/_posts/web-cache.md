---
title: 聊一聊浏览器缓存机制
date: 2019-02-14 08:54:13
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/cache/logo.png
thumbnailImagePosition: left
tags: 
- web
- http
- cache
comments: false
metaAlignment: center
categories: 面试笔试
---
相信很多前端童鞋对于浏览器缓存都不太陌生，但是如果没有系统的归纳总结，可能三言两句很难说明白。如何才能完美的回答，这是一个值得思考的问题。
<!-- more -->

{% alert success %}
当然，我们不能为了应对面试才去掌握，而应该当作技能储备起来，做到活学活用。
{% endalert %}

#### 一、为什么要缓存
1.缓存可以减少用户等待时间，提升用户体验；
2.减少网络带宽消耗
对于网站运营者和用户，带宽都代表着成本，过多的带宽消耗，都需要支付额外的费用。如果可以使用缓存，只会产生极小的网络流量，这将有效的降低运营成本。

3.降低服务器压力
给网络资源设定有效期之后，用户可以重复使用本地的缓存，减少对源服务器的请求，降低服务器的压力。此外，搜索引擎的爬虫机器人也能根据过期机制降低爬取的频率，也能有效降低服务器的压力。

{% alert warning %}
需要注意：缓存使用不当，会有「脏数据」，导致用户数据异常。
{% endalert %}

#### 二、常见缓存类型
{% alert info %}
浏览器缓存分为强缓存和协商缓存。
{% endalert %}

强缓存
1.`Expires`：`GMT` 格式的时间字符串，代表缓存资源的过期时间
`Expires` 也是需要在服务端配置（具体配置也根据服务器而定），`Expires` 添加的是该资源过期的日期。浏览器会根据该过期日期与客户端时间对比，如果过期时间还没到，则会去缓存中读取该资源，如果已经到期了，则浏览器判断为该资源已经不新鲜要重新从服务端获取。

通过这种方式，可以实现直接从浏览器缓存中读取，而不需要去服务端判断是否已经缓存，避免了这次 `HTTP` 请求。值得注意的是 `Expires` 时间可能存在 `客户端时间跟服务端时间不一致` 的问题。

{% alert info %}
建议 Expires 结合 Cache-Control 一起使用，大型网站中一起使用的情况比较多见。
{% endalert %}

2.`Cache-Control`: `max-age` 强缓存利用其 `max-age` 值来判断缓存资源的最大生命周期，它的值单位为秒。
`Cache-Control` 属性值是在 `server` 端配置的，不同的服务器有不同的配置，`web` 服务器 `apache`、`nginx`、`IIS` ,应用服务器 `tomcat` 等配置都不尽相同；

协商缓存
1.`Last-Modified`：值为资源最后更新时间，随服务器 `Response` 返回

2.`If-Modified-Since`：通过比较两个时间来判断资源在两次请求期间是否有过修改，如果没有修改，则命中协商缓存。

3.`ETag`：表示资源内容的唯一标识，随服务器 `Response` 返回。
`Web` 服务器响应请求时，告诉浏览器当前资源在服务器的唯一标识

{% alert danger %}
注：HTTP 中并没有指定如何生成 ETag，哈希是比较理想的选择。
{% endalert %}

4.`If-None-Match`
服务器通过比较请求头部的 `If-None-Match` 与当前资源的 `ETag` 是否一致来判断资源是否在两次请求之间有过修改，如果没有修改，则命中协商缓存。

#### 三、缓存流程解析

看完上面的概念，我们来看看缓存流程是怎样的？先来看看下面这张图：

![process](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/cache/process.png)

如上图所示：
1.浏览器会先检测强缓存类型（`Cache-Control` 或者 `Expires`）是否有效；
2.如果命中了强缓存，则直接从本地获取缓存资源；
3.当强缓存没有命中时，客户端会发送请求到服务器，服务器通过另一些 `Request Header` 验证这个资源是否命中协商缓存，称为 `HTTP` 再验证，如果命中，服务器将请求返回，但不返回资源，而是告诉客户端直接从缓存中获取，客户端收到返回后就会从缓存中获取资源；
4.强缓存不会发送请求到服务器，但协商缓存会发送服务器请求；
5.当协商缓存也没命中时，服务器就会将资源发送回客户端。

需要注意：
1.强缓存和协商缓存共同之处在于，如果命中缓存，服务器都不会返回资源；
2.当 `F5` 刷新网页时，跳过强缓存，但是会检查协商缓存；
3.当 `Ctrl + F5` 强制刷新页面时，直接从服务器加载，跳过强缓存和协商缓存；

#### 四、不会缓存的情况
当然并不是所有请求都能被缓存，无法被浏览器缓存的请求如下：
1.`HTTP` 信息头中包含 `Cache-Control:no-cache` ，`pragma:no-cache（HTTP1.0）`，或`Cache-Control: max-age=0` 等告诉浏览器不用缓存的请求；
2.需要根据 `Cookie`，认证信息等决定输入内容的动态请求是不能被缓存的；
3.经过 `HTTPS` 安全加密的请求；
4.`POST` 请求无法被缓存；
5.`HTTP` 响应头中不包含 `Last-Modified/Etag`，也不包含 `Cache-Control/Expires` 的请求无法被缓存；

#### 五、小故事大道理
上文对整个概念做了阐述，还是不够形象，我们来通过几个小故事生动理解一下：

故事一：`Last-Modified`
浏览器：`Hi`，我需要 `jartto.min.js` 这个文件，如果是在 `Last-Modified: Fri Feb 15 2019 19:57:31 GMT` 之后修改过的，请发给我。
服务器：（检查文件的修改时间）
服务器：`Oh`，这个文件在那个时间之后没有被修改过，你已经有最新的版本了。
浏览器：太好了，那我就显示给用户了。

故事二：`ETag`
浏览器：`Hi`，我需要 `jartto.css` 这个文件，有没有不匹配 `3c61f-1c1-2aecb436` 这个串的
服务器：（检查 `ETag`…）
服务器：`Hey`，我这里的版本也是 `3c61f-1c1-2aecb436`，你已经是最新的版本了
浏览器：好，那就可以使用本地缓存了

{% alert success %}
看完这两个小故事，是否对协商缓存有了更清晰的认识了。
{% endalert %}


参考：
[浏览器缓存机制详解](https://www.cnblogs.com/slly/p/6732749.html)
[web性能优化:详说浏览器缓存](https://www.cnblogs.com/etoah/p/5579622.html)
[前端性能优化之缓存利用](https://blog.csdn.net/bluedandelion/article/details/80895021)




