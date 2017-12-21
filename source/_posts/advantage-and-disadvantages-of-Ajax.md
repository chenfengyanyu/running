---
title: 浅谈AJAX的优缺点
date: 2015-10-01 15:49:33
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/ajax_6EB020FE-7873-42F3-B294-3F9CBF0A6C39.png
thumbnailImagePosition: left
tags: 
- ajax
comments: true
metaAlignment: center
categories: 技术博文
---
午饭的时候和同事闲聊，谈到了AJAX。对于AJAX的优点，大家已经耳熟能详了，可是缺点却有很多人不是很清楚。任何事情都有两面性，有优点的同时，也肯定存在缺点，AJAX也不例外。
<!-- more -->
针对AJAX的优缺点，我上网搜了些资料，看了看书籍，总结如下（可能不是很全面，学习到了再进行补充吧）：

#### 一、AJAX的优点：

①实现局部动态刷新；
②异步加载，提高客户端浏览器的响应速度；
③利用客户端闲置的资源来缓解服务器端的压力，ajax按需取数据；
④不需要使用任何插件，使用方便；

#### 二、AJAX的缺点：
①破坏了浏览器的后退按钮，使之无法运行；
解决方案，通过js调用history.go(-1)实现后退功能。自行处理，为用户提供误操作返回入口。
②对搜索引擎不友好；
换句话说，AJXA实现异步加载，也就意味着有一部分内容通过请求后才从服务器接收过来。而搜索引擎则是通过页面抓取进行搜索的，服务器上的东西根本访问不到（详见参考文献）。
③AJAX的脚本语言是嵌入在HTML页面中的，通过查看源代码或者firebug等工具可直接查看，不利于项目代码的保密；
④不支持跨域请求；
解决方案,JSONP(JSON with Padding,即填充式JSON)。

参考文章：
搜索引擎基本工作原理http://baike.baidu.com/view/1840600.htm
AJAX 跨域请求-JSONP获取JSON数据http://justcoding.iteye.com/blog/1366102
