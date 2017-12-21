---
title: git实践系列六：分支
date: 2015-09-13 20:57:53
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_20150723024349836.jpg
thumbnailImagePosition: left
tags: 
- git
comments: true
metaAlignment: center
categories: 技术博文
---
git强大之处就是分支的快速切换，那么分支怎么理解呢？
git 中的分支，其实本质上仅仅是个指向 commit 对象的可变指针。
<!-- more -->
git 会使用 master 作为分支的默认名字。在若干次提交后，你其实已经有了一个指向最后一次提交对象的 master 分支，它在每次提交的时候都会自动向前移动。

摘自git分支详解，感兴趣的童鞋猛戳这里：
http://www.open-open.com/lib/view/open1328069889514.html

#### 一、创建分支
```sh
git branch test   
```
并查看分支：
```sh
git branch
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_E0DC03FF-25DB-4300-B571-5AD6D4F181D2.png)

#### 二、切换分支
```sh
git checkout test
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_CC5166DB-A59A-4162-A270-A1042E415B19.png)

#### 三、创建并切换到新的分支
```sh
git checkout -b branch_test
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_5BC4E2A5-044D-4C24-8583-C45F6B73EDF2.png)

#### 四、删除分支 
```sh
git branch -d branch_test
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_D3D0A8AF-BAE9-4A41-8EAF-E4737471523C.png)

#### 五、分支重命名
```sh
git branch -m test branch_rename
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_FD712638-9C5D-49DE-8F55-017DF2930DF9.png)

#### 六、查看更改
```sh
git diff
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_E8E9263A-BF6F-4F07-BD74-8D4E78F8FF6B.png)









