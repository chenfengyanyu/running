---
title: Web 优化之 Request
date: 2018-02-09 10:37:00
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/web0.png
thumbnailImagePosition: left
tags: 
- web
- optimise
- request
- 优化
comments: false
metaAlignment: center
categories: 技术博文
---
对于 Web 页面的优化，可能大家都会提出很多方案。可是如果有一天，你发现由于请求接口的数据量太大，导致页面长时间 Loading，这时候你会怎么办？
<!-- more -->
#### 一、场景介绍
先来明确一下相关场景：业务需求需要将海量的数据加载在地图页面「目前是 2 万数据，不久后可能是 5 万数据」，地图方案我们采用了海量点，经测试，前端性能压力不是很大。另一方面，产品对地图数据的实时性要求较高，所以无法使用数据缓存方案，无论是前端还是后端。

#### 二、定位问题
页面长时间 `Loading` ，肯定你会怀疑接口的请求状态，我们来看一下：
![Timing](http://7xvi3w.com1.z0.glb.clouddn.com/web1.png-blog)

从上图可以大致看出一些问题，浏览器发送请求还是很快的，「等待数据返回」和「内容下载」的总时间长达 `12.59s`，服务端也开启了 `Gzip` 压缩，也确实生效了，那为什么还是如此慢？

{% alert danger %}
请注意：网络状况也是不容忽视的一个优化点，这里不再赘述。
{% endalert %}

#### 三、工具介绍
上面我们用到了 `Chrome Network` 来查看接口请求时间，可能有童鞋对 `Timing` 还不太熟悉，这里补充说明一下：

1.`Queueing`：
浏览器线程限制，从添加到待处理队列，到实际开始处理的时间间隔标识；

2.`Stalled`：
是浏览器得到要发出这个请求的指令到请求可以发出的等待时间，一般是代理协商、以及等待可复用的 `TCP` 连接释放的时间，不包括 `DNS` 查询、建立 `TCP` 连接等时间等；

3.`Request sent`：
请求第一个字节发出前到最后一个字节发出后的时间，也就是上传时间；

4.`Waiting(TTFB)`：
请求发出后，到收到响应的第一个字节所花费的时间(Time To First Byte),发送请求完毕到接收请求开始的时间；

5.`Content Download`：
收到响应的第一个字节，到接受完最后一个字节的时间，就是内容下载时间；

{% alert info %}
补充：TTFB(Time to First Byte) 代表服务器处理和返回数据网络延时时间了。服务器优化的目的就是要让这个时间段尽可能短。
{% endalert %}

更多细节，请查看介绍：
- [Chrome Timing](http://blog.csdn.net/qq_20881087/article/details/56682525)
- [优化TTFB](http://blog.csdn.net/fumier/article/details/50511174)

#### 四、请求的生命周期
在 [Chrome Timing](http://blog.csdn.net/qq_20881087/article/details/56682525) 中摘录出如下解释，这里我就不翻译了，怕误导芸芸众生。

The primary phases of the request lifecycle are:
- Redirect: Immediately begins startTime. If a redirect is happening, redirectStart begins as well. If a redirect is occurring at the end of this phase then redirectEnd will be taken.
- App Cache: If it’s application cache fulfilling the request, a fetchStart time will be taken.
- DNS: domainLookupStart time is taken at the beginning of the DNS request. domainLookupEnd time is taken at the end of the DNS request.
- TCP: connectStart is taken when initially connecting to the server. If TLS or SSL are in use then secureConnectionStart will start when the handshake begins for securing the connection. connectEnd is taken when the connection to the server is complete.
- Request: requestStart is taken once the request for a resource has been sent to the server.
- Response: responseStart is the time when the server initially responds to the request. responseEnd is the time when the request ends and the data is retrieved.

我们来看看具体[流程图](http://blog.csdn.net/qq_20881087/article/details/56682525)：
![request lifecycle](http://7xvi3w.com1.z0.glb.clouddn.com/web2.png)

这里推荐一篇文章：[Network Analysis Reference](https://developers.google.com/web/tools/chrome-devtools/network-performance/reference#timing-explanation)

#### 五、尝试方案
- 减少数据传输大小：将后端 `JSON` 数据精简为二维数组，削减 `Key` 值。
```
这个效果很明显，2 万数据从 `2.8M` 减少到 `1.8M`，减少了 `36%` 文件大小。
```
- 调整 `Nginx Gzip` 压缩阈值，参考如下代码修改你的 `nginx.config` 配置文件：
```json
#gzip模块设置
gzip on; #开启gzip压缩输出
gzip_proxied any;
gzip_min_length 1k; #最小压缩文件大小
gzip_buffers 4 16k; #压缩缓冲区
gzip_http_version 1.0; #压缩版本（默认1.1，前端如果是squid2.5请使用1.0）
gzip_comp_level 4; #压缩等级
gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
#压缩类型，默认就已经包含 textml，所以下面就不用再写了，写上去也不会有问题，但是会有一个 warn。
gzip_vary on;
```
- 后端数据考虑缓存：在固定时间点生成 `JSON` 文件或者 `CSV`，避开数据库查询时间。
```
只要是缓存的数据，都会有一定的延迟性，所以你需要深刻了解数据特征，而不是一股脑全缓存了。
```

#### 六、最终结果
1.当我们将数据从根源减少后，数据大小减少了 `36%`，时间也缩短了 `3.75` 秒，如下图：

![Timing2](http://7xvi3w.com1.z0.glb.clouddn.com/web3.png)

2.我们调节 `Gzip` 的压缩级别为 `4`「压缩值越高，压缩率越大，对 `CPU` 的使用率也会升高，但文件大小变化会呈现减缓趋势」，数据大小从 `1.8M` 减少到了 `388KB` ，同时数据下载时间缩短了 `3.45` 秒，如下图：

![Timing3](http://7xvi3w.com1.z0.glb.clouddn.com/web4.png)

{% alert warning %}
友情提示：一定要注意你的数据返回格式，同时相应的调整 `gzip_types`，否则 `Gzip` 可能开启了，但并未起作用。
{% endalert %}

#### 七、优化 TTFB
到这里，优化过程其实并没有完，因为我又发现了 `TTFB` 是可以优化的，先附上[原文地址](https://www.incapsula.com/blog/using-cdn-to-improve-seo-and-ttfb.html)。文中提到了两个问题：

1.动态 `HTML` 如何影响 `TTFB` 呢？我们知道 `TTFB` 是作为客户端接收服务器响应首字节的时间，那么服务器首先会发送什么资源给客户端呢？第一个有意义的响应经常是网页 `HTML` 的内容部分，它的渲染时间由以下两部分组成：
- `Processing(Waiting)time` 在 `web` 服务器上生成动态 `HTML` 所产生的时间；
- `Network(Receiving)time` 编译的 `HTML` 到达浏览器的时间；

2.使用 `Incapsula CDN` 管理 `TTFB` 和新鲜度
{% alert success %}
这里不是很明白，原文中大致是这样描述的，先摘录过来。
{% endalert %}
- Purge Cache 
这个最新增加的功能目的是通过移除与我们预先设置的验证周期的依赖来清除所有的缓存对像，内容和自动验证机制。点击鼠标缓存副本即被删除，为所有最新更新的内容或动态生成的内容保持即时新鲜度。

- Cache Everything 
这是基于学习算法版本中最受争议的方法。该功能可用即缓存所有可用的对象，不管他们的类型和功能。明显的缺点是对新鲜度产生重大的影响。尽管如此，如果你运行的是一个静态的博客，可以开发一个程序在每次更新后手动清除缓存，这可能是一次选择方案。

- Always Cache(by resource) 
在缓存一切模式上有一个更多选择的版本。是一个可以手动微调缓存指令的工具。如果你仅仅想缓存某一特殊页面的HTML部分而不影响页面的其他部分来提高TTFB，你可以使用这种方式。

- Async Validation 
这个方式控制自动验证周期。启用这个选型确保所有用户总是会从缓存中提高 `TTFB`。然而，在每个缓存周期中，用户有可能访问过时的副本，因为缓存在他访问的过程中将异步的进行更新。

- Override “Vary:User-Agent”headers 
`Vart:User-Agent` 将防止缓存。然而，这些 `headers` 经常被滥用，他们经常出现在没有真正目的服务的网页上。启用该选项，将允许 `Incapsula` 来重写这些指令，来提高你的 `TTFB` 和用户体验以及搜索引擎排名。

#### 八、总结
`Web` 页面优化本身就不是一蹴而就的，更没有什么捷径可走，你唯一需要做的就是：不断的尝试，不断优化。