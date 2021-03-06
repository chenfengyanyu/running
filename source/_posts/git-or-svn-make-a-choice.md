---
title: 'git or svn , make a choice!'
date: 2016-06-16 22:34:46
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_72827A00-0137-4B73-BCBE-CFEA4C6242AC.png
thumbnailImagePosition: left
tags: 
- git
- svn
comments: true
metaAlignment: center
categories: 技术博文
---
当前的版本控制大致分为两个流派，其一，git党；其二，svn党；当然，我们有自己的判断标准，前提是你对这两者有多少了解呢？不要紧，下面我们来一一阐述：
<!-- more -->
#### 一、git是分布式的，svn不是：
git跟svn一样有自己的集中式版本库或服务器。但，git更倾向于被使用于分布式模式，也就是每个开发人员从中心版本库/服务器上chect out代码后会在自己的机器上克隆一个自己的版本库。

#### 二、git把内容按元数据方式存储，而svn是按文件：
所有的资源控制系统都是把文件的元信息隐藏在一个类似.svn,.cvs等的文件夹里。如果你把.git目录的体积大小跟.svn比较，你会发现它们差距很大。因为,.git目录是处于你的机器上的一个克隆版的版本库，它拥有中心版本库上所有的东西，例如标签，分支，版本记录等。

#### 三、git分支和svn的分支不同：
分支在svn中一点不特别，就是版本库中的另外的一个目录。如果你想知道是否合并了一个分支，你需要手工运行像这样的命令svn propget svn:mergeinfo，来确认代码是否被合并。所以，经常会发生有些分支被遗漏的情况。

#### 四、git的内容完整性要优于svn：
git的内容存储使用的是SHA-1哈希算法。这能确保代码内容的完整性，确保在遇到磁盘故障和网络问题时降低对版本库的破坏。

#### 五、git 最为出色的是它的合并跟踪（merge tracing）能力。

#### 六、git分支功能最为强大，分支管理能力让svn望尘莫及

git可以很容易地对比两个分支，知道一个分支中哪些提交尚未合并到另一分支，反之亦然。
-查看当前分支比other分支多了哪些提交：
```sh
git log other..
```
查看other分支比当前分支多了哪些提交：
```sh
git log ..other
```
我不认为svn的分支是真正的分支，因为分支最基本的提交隔离svn就没能实现。 在svn中一次提交可以同时更改主线（/trunk）和分支中的内容， 所以判断一个分支中哪些提交未合并到另外的分支，完全不能对svn抱有希望。

#### 七、隔离开发，提交审核

如何对团队中的新成员的开发进行审核呢？在git服务器上可以实现用户自建分支和自建版本库的功能， 这样团队中的新成员既能将本地提交推送到服务器以对工作进行备份， 又能够方便团队中的其他成员对自己的提交进行审核。

审核新成员提交时，从其个人版本库或个人分支获取（fetch）提交，从提交说明、代码规范、编译测试 等多方面对提交逐一审核。审核通过执行 git merge 命令合并到开发主线中。

#### 八、对合并更好的支持，更少的冲突，更好的冲突解决

因为git基于对内容的追踪而非对文件名追踪，所以遇到一方或双方对文件名更改时， git能够很好进行自动合并或提供工具辅助合并。而svn遇到同样问题时会产生树冲突， 解决起来很麻烦。

git的基于DAG（有向非环图）的设计比svn的线性提交提供更好的合并追踪， 避免不必要的冲突，提高工作效率。这是开发者选择git、抛弃svn的重要理由。

#### 九、版本库的安全性

svn版本库安全性很差，是管理员头痛的问题。

- svn版本库服务器端历史数据被篡改，或者硬盘故障导致历史数据被篡改时， 客户端很难发现。管理员的备份也会被污染。
- svn作为集中式版本控制系统，存在单点故障的风险。备份版本库的任务非常繁重。

git在这方面完胜svn。首先git是分布式版本控制系统，每个用户都相当于一份备份， 管理员无需为数据备份而担心。再有git中包括提交、文件内容等都通过SHA1哈希保证数据的完整性， 任何恶意篡改历史数据都会被及时发现从而被挫败。

#### 十、更多的十条喜欢git的理由

- 异地协同工作。
- 现场版本控制。
- 重写提交说明。
- 无尽的后悔药。
- 更好用的提交列表。
- 更好的差异比较。
- 工作进度保存。
- 作为svn前端实现移动办公。
- 无处不在的分页器。
- 快。

#### 十一、总结
> git就是分布式版本控制系统，对应的是集中式的版本控制如svn。简单的说，分布式的版本控制就是每个人都可以创建一个独立的代码仓库用于管理，各种版本控制的操作都可以在本地完成。每个人修改的代码都可以推送合并到另外一个代码仓库中。而像svn这样，只有一个中央控制，所有的开发人员都必须依赖于这个代码仓库。每次版本控制的操作也必须链接到服务器才能完成。很多公司喜欢用集中式的版本控制是为了更好的控制代码。如果个人开发，就可以选择git这种分布式的。


看完了上面所有内容，那么你属于哪一个派系呢?

> To be or not to be ? make a choice!


参考文章：
http://blog.jobbole.com/31444/
http://www.oschina.net/news/29214/why-git-is-better-than-svn
http://blog.jobbole.com/78960/











