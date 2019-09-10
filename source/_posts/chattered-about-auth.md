---
title: 浅谈auth认证
date: 2016-07-27 17:07:54
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/auth_201981-120HF0054959.jpg
thumbnailImagePosition: left
tags: 
- auth
comments: true
metaAlignment: center
categories: 每日积累
---
关于认证，开放平台有两种认证方式，一种是Basic Auth，一种是OAuth。
<!-- more -->
#### 一、Basic Auth（HTTP Auth）
{% alert success %}
Basic Auth简单点说明就是每次请求API时都提供用户的username和password。
{% endalert %}
优点：
①使用非常简单，
②开发和调试工作简单，
③没有复杂的页面跳转逻辑和交互过程；
④更利于发起方控制；

缺点：
①安全性低，每次都需要传递用户名和密码，用户名和密码很大程度上存在被监听盗取的可能；
②同时应用本地还需要保存用户名和密码，在应用本身的安全性来说，也存在很大问题；
③开放平台服务商出于自身安全性的考虑（第三方可以得到该服务商用户的账号密码，对于服务商来说是一种安全隐患），未来也会限制此认证方式（Twitter就计划在6月份停止Basic Auth的支持）
④用户如果更改了用户名和密码，还需要重新进行密码校验的过程。

#### 二、OAuth
OAuth为用户资源的授权提供了一个安全、开放的标准，它分为几个交互过程：
1）应用用APP KEY和APP SECRET换取OAuth_token；
2）应用将用户引导到服务商的页面对该OAuth_token进行授权（可能需要输入用户名和密码）；
3）服务商的页面跳转回应用，应用再根据参数去服务商获得Access Token；
4）使用这个Access Token就可以访问API了。

OAuth的优点：
①安全性高，用户的账户和密码只需要提供一次，而且是在服务商的页面上提供，防止了Basic Auth反复传输密码带来的安全隐患；
②Access Token访问权限仅限于应用，被窃取不会影响用户在该服务商的其他服务；
③Access Token即使被监听丢失了随时可以撤销，不像密码丢失可能就被别人篡改了；
④用户修改了密码也不会影响该应用的正常使用。


参考文章：
http://baike.baidu.com/link?url=Ru1yPv68202-u1y8EmdkrV38N57dEXWaqFNuT_MeyGwnRAXlxsPPCYH93UzqkKa4QNxCgm38UYgr9I1t_vwlca




