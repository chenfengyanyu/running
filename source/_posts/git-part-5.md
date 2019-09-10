---
title: git实践系列五：日志和标签
date: 2015-09-13 17:53:21
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_20150723024349836.jpg
thumbnailImagePosition: left
tags: 
- git
comments: true
metaAlignment: center
categories: 技术博文
---
项目开发以及版本控制，可能并非一帆风顺，所以我们要学会查看git日志，及时的对项目做一些相应更改。本节重点在于尝试git日志操作和创建个人标签。
<!-- more -->
#### 一、获取最近一次提交
```sh
git log -1
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_3F9161CA-C623-47E9-94AA-25438AE69064.png)

#### 二、创建标签
> 让我们标记当前HEAD使用git tag命令。他提供的标记名称前加上-a选项，使用-m选项，并提供标签信息。

```sh
git tag -a 'Release_1_0' -m ‘create tag test' HEAD
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_0509E939-B180-4066-BD07-6C4B240DCA2C.png)

```sh
git push origin tag Release_1_0
```

如果想标记特定提交然后使用适当的COMMIT ID，而不是HEAD 指针。 
使用下面的命令推到远程存储库中的标签。
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_65EC5DBF-DBEE-4620-A773-F4B8ED4736E2.png)

#### 三、查看标签
```sh
git tag -l
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_030DC5B2-074D-431C-B6FA-384F748EF04A.png)

#### 四、查看细节 
```sh
git show Release_1_0
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_BF510A28-D7D6-4B5B-9934-7132DC88C824.png)

#### 五、删除标签
```sh
git tag -d Release_1_0
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_C4A67721-EBAB-4A7F-8BBA-CCCCF0AFC57D.png)

删除远程库标签
```sh
git push origin :Release_1_0
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_86A63048-0036-4F21-BFF4-872061C3B2BD.png)













