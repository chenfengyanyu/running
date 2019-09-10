---
title: hexo搭建博客系列一
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_peak.jpg
date: 2016-06-20 17:19:22
thumbnailImagePosition: left
tags: 
- hexo
comments: true
metaAlignment: center
categories: 技术博文
---
很早前就有这样一个想法，有没有一种可能性：我的代码放在本地，保存在类JSON数据中，我不需要数据库，不需要后台接口支持。那么，给我一个服务器，我就可以搭起一套博客网站。
<!-- more -->
具体来说明：我现在想要搭建一个博客系统，我的代码使用git来进行分布式代码管理，数据的话我存在本地的json中。至于图片，找个免费的图床（譬如七牛云）。差的就是服务器了，而github page则充当了我们的服务器角色。

当然，既然是博客系统，你可能要问了：“上面这些也不够啊，我要评论功能，我要rss订阅，我要统计，我要。。。”。是的，你可能忘了，我们有强大的第三方作为后盾，上面所说的统统不是问题。

下来，我来手把手教你，如何使用hexo，github和第三方组建搭建起一个完全属于你的博客系统，纯免费的哦～

#### 一、读法
我们先来看看怎么读，Hexo～嗨克搜

#### 二、介绍：
Hexo是一个简单、快速、强大的博客发布工具，支持Markdown格式。

#### 三、官方文档
英文文档：
https://hexo.io/docs/index.html

中文文档：
https://hexo.io/zh-cn/docs/deployment.html

#### 四、命令行安装，这里以Mac环境做演示
```sh
sudo npm install -g hexo
hexo -v
```

![hexo](http://7xvi3w.com1.z0.glb.clouddn.com/hexo_0B900900-F8F1-45B2-A798-C4FBD86D8110.png)

#### 五、电脑新建hexo-test目录，进入目录，执行
```sh
hexo init
```
初始化项目，此时Hexo会自动在目标文件夹建立网站所需要的所有文件。

![hexo](http://7xvi3w.com1.z0.glb.clouddn.com/hexo_0DDBB3E8-CEE0-424F-A114-AF0699D60E49.png)

#### 六、生成命令
```sh
hexo g
```
![hexo](http://7xvi3w.com1.z0.glb.clouddn.com/hexo_70334364-BA09-4E64-B29C-A92121287021.png)

#### 七、启动服务，默认端口4000
```sh
hexo s
```
![hexo](http://7xvi3w.com1.z0.glb.clouddn.com/hexo_D798F011-092E-42A5-891A-61808D380EEF.png)

#### 八、打开浏览器查看：出现如下网站，证明你已经成功了。
```sh
http://localhost:4000/
```
![hexo](http://7xvi3w.com1.z0.glb.clouddn.com/hexo_AB34E5C0-2B8F-4C2E-AFA9-740BDCB70F7F.png)

是不是感觉很简单呢，如果你感兴趣，后续的文章不要错过哦～

















