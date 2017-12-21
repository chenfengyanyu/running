---
title: yslow 优化之 add expires headers
date: 2016-12-08 22:57:06
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/yslow.png
thumbnailImagePosition: left
tags: 
- yslow
- 优化
comments: true
metaAlignment: center
categories: 技术博文
---
相信很多前端童鞋都用过 yslow 来优化页面吧，不知道有多少人会被 add expires headers这一规则困扰，今天我来说道说道。
<!-- more -->
本文就不介绍yslow了，这里着重介绍一下优化项中的 add expires headers 。正如短板效应所阐释的道理，网站评分可能会因为这一项评分F而拉低，所以，让我们一起来弥补短板吧。

#### 一、使用 yslow
我们先从网站评分看起，一图胜千言：
![yslow](http://7xvi3w.com1.z0.glb.clouddn.com/yslow_test.png)
由于 add expires headers 这一项，我的网站整体评分为B，所以，我必须从这里下手。

#### 二、add expires headers
首先，我们需要了解 add expires headers 到底是什么？
{% alert success %}
Expires headers tell the browser whether they should request a specific file from the server or whether they should grab it from the browser's cache.
{% endalert %}

简单来说：就是向文档添加一个有效期，告诉浏览器这个文档的有效性和持久性。如果已有缓存，文档就可以从浏览器中的缓存(除已经过期)而不是从服务器读取。接着，客户端验证缓存中的副本，看看是否过期或者失效，以决定是否必须从服务器获得更新。

添加该项主要是可以加快网站的访问速度，对于一些不长更新的静态文件，都可以进行设置。如
- images: jpg, gif, png
- favicon/ico
- javascript
- css

#### 三、如何操作
很好，那我该如何设置？根据[gtmetrix](https://gtmetrix.com/add-expires-headers.html)中记载：
{% alert success %}
We define our Expires Headers in the .htaccess file. 
{% endalert %}
格式如下：
```sh
<IfModule mod_expires.c>
# Enable expirations
ExpiresActive On 
# Default directive
ExpiresDefault "access plus 1 month"
</IfModule>
```
嗯，大概可以看出端倪了，然而并没有什么卵用！看来我们需要先补一下课，学点关于.htaccess的知识。

#### 四、关于 .htaccess
.htaccess文件(或者"分布式配置文件"）提供了针对目录改变配置的方法，即，在一个特定的文档目录中放置一个包含一个或多个指令的文件， 以作用于此目录及其所有子目录。

.htaccess可以帮我们实现包括：文件夹密码保护、用户自动重定向、自定义错误页面、改变你的文件扩展名、封禁特定IP地址的用户、只允许特定IP地址的用户、禁止目录列表，以及使用其他文件作为index文件等一些功能。

Unix、Linux系统或者是任何版本的Apache Web服务器都是支持.htaccess的，但是有的主机服务商可能不允许你自定义自己的.htaccess文件。

.htaccess文件中的配置指令作用于.htaccess文件所在的目录及其所有子目录，但是很重要的、需要注意的是，其上级目录也可能会有.htaccess文件，而指令是按查找顺序依次生效的，所以一个特定目录下的.htaccess文件中的指令可能会覆盖其上级目录中的.htaccess文件中的指令，即子目录中的指令会覆盖父目录或者主配置文件中的指令。

更多内容请移步[百科](http://baike.baidu.com/link?url=aq11YuMa6rKydRu5V_8Mw7gpszrRR2UC0zQJ5tb_l_iL87TLd7XBncVRmbEyXNj7jYwca75XXedDqp0EAzvBWX3Fc-j_Ww0ASYn0o7wcMGO)

#### 五、添加以及配置 htaccess
好了，根据上面提取关键字：
1.大部分服务器都支持；
2.将 .htaccess 放置在项目目录下；
3.下级 .htaccess 会覆盖上级 .htaccess；

基本清晰了，我们来开始：
进入test项目目录：
```sh
cd test
```
创建 .htaccess 文件：
```sh
echo '' >> .htaccess
```
打开 .htaccess 文件：
```sh
vim .htaccess
```
拷贝如下代码：
```sh
<IfModule mod_expires.c>
# Enable expirations
ExpiresActive On 
# Default directive
ExpiresDefault "access plus 1 days"
# My favicon
ExpiresByType image/x-icon "access plus 1 year"
# Images
ExpiresByType image/gif "access plus 3 days"
ExpiresByType image/png "access plus 3 days"
ExpiresByType image/jpg "access plus 3 days"
ExpiresByType image/jpeg "access plus 3 days"
# CSS
ExpiresByType text/css "access plus 3 days"
# Javascript
ExpiresByType application/javascript "access plus 3 days”
FileETag none
</IfModule>
```
保存并退出，搞定～

如果你觉得还是不够用，来看看下 面这个超完整版：
```sh
FileETag None
<IfModule mod_expires.c>
    ExpiresActive on
    ExpiresDefault                                      "access plus 1 month"
    ExpiresByType text/css                              "access plus 1 year"
    ExpiresByType application/json                      "access plus 0 seconds"
    ExpiresByType application/xml                       "access plus 0 seconds"
    ExpiresByType text/xml                              "access plus 0 seconds"
    ExpiresByType image/x-icon                          "access plus 1 week"
    ExpiresByType text/x-component                      "access plus 1 month"
    ExpiresByType text/html                             "access plus 0 seconds"
    ExpiresByType application/javascript                "access plus 1 year"
    ExpiresByType application/x-web-app-manifest+json   "access plus 0 seconds"
    ExpiresByType text/cache-manifest                   "access plus 0 seconds"
    ExpiresByType audio/ogg                             "access plus 1 month"
    ExpiresByType image/gif                             "access plus 1 month"
    ExpiresByType image/jpeg                            "access plus 1 month"
    ExpiresByType image/png                             "access plus 1 month"
    ExpiresByType video/mp4                             "access plus 1 month"
    ExpiresByType video/ogg                             "access plus 1 month"
    ExpiresByType video/webm                            "access plus 1 month"
    ExpiresByType application/atom+xml                  "access plus 1 hour"
    ExpiresByType application/rss+xml                   "access plus 1 hour"
    ExpiresByType application/font-woff                 "access plus 1 month"
    ExpiresByType application/vnd.ms-fontobject         "access plus 1 month"
    ExpiresByType application/x-font-ttf                "access plus 1 month"
    ExpiresByType font/opentype                         "access plus 1 month"
    ExpiresByType image/svg+xml                         "access plus 1 month"
</IfModule>
```
{% alert info %}
友情提示：配置完，记得重启服务器哦～
{% endalert %}

好了，赶快应用到你的网站吧，see me fly～

#### 六、参考文档
1.[yslow优化规则说明](https://gtmetrix.com/use-cookie-free-domains.html)
2.[关于htaccess](http://baike.baidu.com/link?url=aq11YuMa6rKydRu5V)
3.[配置过期响应头](http://972169909-qq-com.iteye.com/blog/1796646)
4.[yslow极限优化](http://www.poluoluo.com/jzxy/201009/95482.html)






