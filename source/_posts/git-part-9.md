---
title: git实践系列九：撤销操作
date: 2015-09-22 17:24:48
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_20150723024349836.jpg
thumbnailImagePosition: left
tags: 
- git
comments: true
metaAlignment: center
categories: 技术博文
---
本节重点是如何使用git来撤销一次错误提交，那么我们来假设一种场景：你的本地代码已经修改，并提交到远程库中。突然发现提交有问题，此时需要回退到上一个版本，怎么办？oh , fuck !
<!-- more -->
当然，除了fuck之外，我们还有别的办法，譬如：撤销。

没关系，我们来操作一次。说了是实践教程，那当然是举例说明喽！

一、查看提交日志：
```sh
git reflog
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_70DE4C99-2A2B-41AE-92BC-343796CDBDF1.png)

二、回退到最近的一个正常版本：
```sh
git reset  --hard 45af637
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_E03F2948-FB0C-4957-8866-45A80F34BAB9.png)

三、提交本地代码到远程库
执行
```sh
git status -s
```
发现暂存区为空，接着执行
```sh
git commit -a -m 'bla~bla'
```
没法提交，怎么办？那么直接就用本地代码去覆盖远程
```sh
git push origin dev
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_7DB3138A-5622-4E3B-9C29-B8874C6E8EC8.png)

git给出提示，当前的代码是之后的，提示你去pull

但是我们就是要恢复远程库到我改后的正常版本,所以，强制执行，
```sh
git push -f origin dev
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_D70D6CF0-1A17-4C08-A4E4-86D2B79D6DE7.png)

ok，总算恢复了一次错误的提交。
















