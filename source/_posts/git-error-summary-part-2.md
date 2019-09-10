---
title: git错误汇总（下）
date: 2015-09-28 23:29:18
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_43A0391D-8F3C-4215-B75B-D2A4CB356BA4.png
thumbnailImagePosition: left
tags: 
- git
- 错误汇总
comments: true
metaAlignment: center
categories: 技术博文
---
由于篇幅有限，我们接着把剩下的问题汇总一下。这都是实在的干货，我真是太有心了，也不知道有没有人点赞😊。废话不多说，我们直入正题，从七开始吧～
<!-- more -->
#### 七、未监视的工作树文件，如何处理？
```
error: Untracked working tree file 'gulpfile.js' would be overwritten by merge.  Aborting
```
解决方案如下：
```sh
git reset --hard HEAD
#删除untracked文件，参数fd是强制并删除目录
git clean -fd 
git pull
```

#### 八、代码冲突了，以谁的版本为准？
```
Your local changes to the following files would be overwritten by merge
error: Your local changes to the following files would be overwritten by merge
```

如果希望保留生产服务器上所做的改动,仅仅并入新配置项, 处理方法如下:
```sh
git stash
git pull
git stash pop
```
然后可以使用如下命令：
```sh
git diff -w +文件名 
```

反过来,如果希望用代码库中的文件完全覆盖本地工作版本. 方法如下:
```sh
git reset --hard
git pull
```
其中git reset是针对版本,如果想针对文件回退本地修改,使用
```sh
untracked working tree file
```

#### 九、git放弃本地修改，强制更新
```sh
git fetch --all
git reset --hard origin/master
git fetch 
```
这里需要注意的是：git fetch只是下载远程的库的内容，不做任何的合并，git reset 把HEAD指向刚刚下载的最新的版本。

#### 十、取消本地修改
```sh
git checkout -- README.md
```

此操作将丢弃本地修改

#### 十一、取消本地暂存文件操作，即取消 git add
```sh
git reset HEAD README.md
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_9A5246ED-9E43-42B3-9A84-1C9D8B461BC5.png)

#### 十二、修改上一次commit 提交信息
```sh
git commit -- amend
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_6DB344AF-66FC-479E-A62F-77155919BA45.png)

程序进入了vim模式（如果对vim编辑器不太熟悉，猛戳这里！[《熟练使用vim编辑器》](http://jartto.wang/2015/10/03/skilful-with-vim/)），编辑之后:wq!保存并退出，接下来我们来查看commit提交日志：

![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_ED01E167-54D4-4245-9F9D-5F3772B074E9.png)

#### 十三、彻底撤销最近的提交
```sh
git reset --hard HEAD^
```
引用回退到前一次，而且工作区和暂存区都会回退到上一次提交的状态。自上一次以来的提交全部丢失。适用于完全恢复到上一个版本：
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_4D119908-2F9C-4AD9-8699-119223F38CDA.png)

#### 十四、回退到上一次提交的状态，按照某一次的commit完全反向的进行一次commit.(代码回滚到上个版本，并提交git)
```sh
git revert HEAD
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_56B1AB35-2F5E-47FF-A889-B469DBD095FE.png)
git log --oneline查看日志
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_6A759CE9-5487-47D5-A16A-ECBE675C0CCD.png)
git reset --hard a103285 我们接着回到之前的版本
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_D42CB1FB-EDBE-4AE1-99D2-F20CA4C7220D.png)
可以看到我们的HEAD已经指到了a103285，于是，我们完成了一次回退。

{% alert info %}
基本上就这些了，以后再碰到新的问题，我接着往下写吧。
{% endalert %}

参考：
http://blog.chinaunix.net/uid-10415985-id-4142896.html
http://www.cnblogs.com/leehongee/p/3674570.html
http://blog.csdn.net/cankingapp/article/details/18312117
http://www.oschina.net/translate/10-tips-git-next-level




