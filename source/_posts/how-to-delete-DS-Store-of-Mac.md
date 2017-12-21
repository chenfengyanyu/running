---
title: Mac下如何删除.DS_Store文件
date: 2015-10-05 11:08:14
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_B511E305-BC4E-4CE8-8C56-D97B22F4FB9E.png
thumbnailImagePosition: left
tags: 
- mac
comments: true
metaAlignment: center
categories: 技术博文
---
8月份，拿到了我的第一台mac笔记本，欣喜之余，有点犯愁。多年的windows使用经验，让我觉得mac很不顺手，但这并不能影响我的积极性。不会我就多花点功夫琢磨呗，如果你也遇到了同样的困惑，那么恭喜你，我会逐步分享，大家共同进步嘛。
<!-- more -->
#### 一、DS_Store是什么？
在 Mac OS X 系统下，几乎绝大部分文件夹中都包含 .DS_Store 隐藏文件，这里保存着针对这个目录的特殊信息和设置配置，例如查看方式、图标大小以及这个目录的一些附属元数据。

#### 二、我们需要删除的场景：
ftp查看本地文件时
git提交本地代码时
hexo生成代码时
等等。。。
虽然我们有办法去移除它，譬如在git下，我们可以添加.gitignore文件来忽略它。但是我依然有删除他的冲动。
![mac](http://7xvi3w.com1.z0.glb.clouddn.com/blog_5AB68F13-9273-4918-8933-A32B950A1610.png)

#### 三、查找解决方案：我们不想文件夹下有DS_Store文件，而且永远也不需要生成。

执行命令：
```sh
sudo find / -name ".DS_Store" -depth -exec rm {} \;
```
![mac](http://7xvi3w.com1.z0.glb.clouddn.com/blog_3DE50546-E2BF-4593-9966-8476D0963508.png)

```sh
defaults write com.apple.desktopservices DSDontWriteNetworkStores true
```

#### 四、重新查看：异常已经被处理了，这也就意味着我们的操作达到了目的，.DS_Store文件已经被删除了。
![mac](http://7xvi3w.com1.z0.glb.clouddn.com/blog_CD809809-746A-4F93-BE18-FE38FC682290.png)

既然问题搞定了，那就这么愉快的散吧～

参考：
http://www.tuicool.com/articles/r2IrIjq






