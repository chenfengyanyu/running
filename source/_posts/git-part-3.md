---
title: git实践系列三：移动，移除与提交
date: 2015-09-10 15:34:45
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_20150723024349836.jpg
thumbnailImagePosition: left
tags: 
- git
comments: true
metaAlignment: center
categories: 技术博文
---
本节主要围绕git中的mv，rm，以及commit命令来展开说明。为了方便理解，我贴上了自己在尝试过程中的代码，希望可以起到一些示例作用。
<!-- more -->
#### 一、文件移动
当移动一个文件到另一个文件的时候，这样就起到了重命名的作用，如下：
```sh
git mv index.html home.html   
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_E235B93C-E6B1-42F0-A363-89F0AF0F7E4A.png)

将文件移动到temp文件下：
```sh
git mv home.html temp 
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_11637FD9-D87F-4228-8D65-5474302819FC.png)

查看操作状态：
```sh
git status -s   
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_169DA700-6136-42C3-A18E-A6D79DB16A96.png)

关于status，这里需要补充说明一下，如下图：
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_EEE66011-519C-4657-817E-4FF6D2042BA7.png)

#### 二、移除文件
```sh
git rm index.html
```
这里我舍不得移除index.html，所以我移除了Mac中的.DS_Store文件，这里埋个点（很有意思），后续还要详细说明。
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_577EB4DA-5815-4588-A471-BBF4CF269BA8.png)

#### 三、提交
```sh
git commit -a -m 'desc..’
```
-a标志，这使得git提交自动检测修改过的文件
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_B1790D25-01CA-4C03-8F42-FEB402140EE7.png)


参考：
http://git-scm.com/docs/git-status







