---
title: git实践系列七：迁出和回退
date: 2015-09-18 21:01:31
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_20150723024349836.jpg
thumbnailImagePosition: left
tags: 
- git
comments: true
metaAlignment: center
categories: 技术博文
---
本节主要来说说checkout和reset，为啥把把它俩放一起呢？哈哈，不用猜啦，只是随心情而已～，如果非要找点联系，那么我认为是松花江和松花蛋的之间的关系。
<!-- more -->

#### 一、git checkout
也就是我们所说的迁出，主要有三个作用：
第一个就是切换分支。例如你从远程仓库clone下来所有的源代码，你git branch一下会看到你通常是在master，如果你想切换到某一个分支上呢？
```sh
git checkout <branchname>
```

第二个就是放弃对某个文件的修改。例如你想放弃对某个文件Hello.cpp的修改。
```sh
git checkout -- <Hello.cpp>
```

第三个就是新建一个分支
```sh
git checkout <-b> <branchname>，
```
不加-b的话只新建不切换到新的分支上，加-b新建并切换到该分支上

#### 二、git reset
主要是取消上一次的操作，具体用法有很多，说两个：

第一个取消merge，如下代码会退回到merge前的代码
```sh
git reset --hard ORIG_HEAD
```

第二个就是取消commit，取消上num次的commit
```sh
git reset HEAD~<num> <-soft/-hard>
```


第三个是将文件从stage（追踪的库）中移除
```sh
git reset HEAD <filename>
```

关于reset，我觉得这个很有必要好好了解一下，毕竟这里可以找到无尽的后悔药，你就不需要承担自己失误的后果了。这里有两篇笔记：

- git reset学习笔记
http://www.cnblogs.com/craftor/archive/2012/11/04/2754140.html

- git回滚操作 （推荐）
http://blog.163.com/zhulp0372@yeah/blog/static/11589447920124894240873/















