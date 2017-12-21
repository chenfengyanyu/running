---
title: git错误汇总（上）
date: 2015-09-26 22:02:04
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_43A0391D-8F3C-4215-B75B-D2A4CB356BA4.png
thumbnailImagePosition: left
tags: 
- git
- 错误汇总
comments: true
metaAlignment: center
categories: 技术博文
---
每当我新学一门语言或者技术的时候，都会碰到或多或少的问题，有的问题我们可以很容易查到并解决，但是有时候却是很难。当我们一遍遍百度的时候，发现都是同一篇文章被转来钻去，来转去，转去，去～
<!-- more -->
可恶的是它并不能解决我们的问题，反而浪费了宝贵的时间。因此，除了google和stack overflow以外，我又想到了第三种解决方案。

对，把学习过程中的错误以及踩过的坑，整理出来，供初学者参考。

所以，就有了本篇文章。快来快来数一数，我到底碰到了多少坑。

#### 一、git分支切不出来怎么办？
当我试图切换分支的时候，报出以下错误：
```
error: The following untracked working tree files would be overwritten by checkout:
    .DS_Store
Please move or remove them before you can switch branches.
Aborting
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_ECCA803F-CF00-488F-A86C-7F152BAABA13.png)

好的，我们来照着提示操作：

```sh
git rm .DS_Store
```
结果，控制台说：

```
fatal: pathspec '.DS_Store' did not match any files
```
可以看到，.DS_Store这个隐藏文件不能被rm，所以干脆直接强制切换分支。

![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_C3F82CF9-F21E-42DC-B7B6-EA6256F7EE88.png)

按实际情况处理，因为我知道.DS_Store无毒无害。我们有好几种方法可以搞掉.DS_Store文件，这里先卖个关子，下一节展开讨论。于是乎，就切出了master。

#### 二、文件名太长？
控制台告诉我们如下信息：
```
git error:fliename too long
```
虽然错误很莫名其妙，但是我还是找到了解决方案，设置如下：
```sh
git config --system core.longpaths true
```

#### 三、git 大小写敏感问题
当你更改文件名大小写的时候，git默认不区分大小写，所以当你的代码部署到服务器，你会发现好多文件找不到目录。你可能需要如下设置：
```sh
git config core.ignorecase false
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_7D4EE7B5-C6C7-49B3-82AC-C5CA9AC74F77.png)

#### 四、找不到远程仓库？
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_B33C1A89-69B9-4D38-BC53-D399D4AB0866.png)

```sh
fatal: 'origin' does not appear to be a git repository
fatal: Could not read from remote repository.
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_16566B83-4B7B-4F53-8CDE-B1C6D805B9A9.png)

解决方案，既然找不到，那我们只能添加喽：
```sh
git remote add origin https://github.com/chenfengyanyu/node-mysql-test.git
```
查看远程列表：
```sh
git remote -v
```

#### 五、git pull 错误
```sh
failed to push some refs to 'https://github.com/askgreenstone/HFE.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details

Your branch is behind 'origin/master' by 1 commit, and can be fast-forwarded.
  (use "git pull" to update your local branch)
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_18747BC1-8E43-4E0E-8D09-DB978EF062CE.png)

git pull origin dev／master

![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_4C672F47-AF5D-4502-8F92-E4AB48473C50.png)

#### 六、删除远程库连接
有时候，我们有好几个远程库，一不小心把本地库挂错了远程库，那么就可以执行以下操作：
```sh
git remote rm origin
```
然后重新挂到新的远程库就可以了。

{% alert info %}
零零散散写了这么多，看来三言两语是说不完了，那我们下节继续！
{% endalert %}


参考：
http://blog.chinaunix.net/uid-10415985-id-4142896.html
http://www.cnblogs.com/leehongee/p/3674570.html
http://blog.csdn.net/cankingapp/article/details/18312117
http://www.oschina.net/translate/10-tips-git-next-level














