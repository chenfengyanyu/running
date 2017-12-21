---
title: git实践系列八：合并与冲突
date: 2015-09-21 22:33:38
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_20150723024349836.jpg
thumbnailImagePosition: left
tags: 
- git
comments: true
metaAlignment: center
categories: 技术博文
---
关于merge，我们再熟悉不过了。多人协同开发，你发布代码，我也发布代码，最终我们都要发布到同一版本库，那么代码合并就必不可少了。本节我们来模拟一个日常开发中的场景作以说明，Let's go!
<!-- more -->
假设我们一直在分支dev开发，此时我们已经写完代码，准备合并到主分支master，以下是具体操作步骤：
#### 一、切换分支到master
```sh
git checkout -f master
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_0A2573A7-F8F3-49E0-B14F-88D0C17717DA.png)
#### 二、将dev分支合并到master
```sh
git merge dev
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_040791A5-BF17-4FC8-BD81-2DB41F5A62FA.png)
不巧的是，我们改了同一个文件。那么这时候就会产生冲突，我们就需要对冲突文件进行合并。
可以看到，有文件自动合并失败了，这样我们不得不手动去合并了。
```
<<<<<<< HEAD
test in master
=======
test in dev
>>>>>>> dev
```

<div style="color:red">
<<<<<<<标记冲突开始，后面跟的是当前分支中的内容。
HEAD指向当前分支末梢的提交。
=======之后，>>>>>>>之前是要merge过来的另一条分支上的代码。
>>>>>>>之后的dev是该分支的名字。
对于简单的合并，手工编辑，然后去掉这些标记，最后像往常的提交一样先add再commit即可。
</div>

#### 三、查看状态git status -s
status -s 传送门http://git-scm.com/docs/git-status
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_BD0CB67C-241E-4837-8DB4-316C1329015B.png)
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_0D72A6B0-6FC4-4A99-A53E-98DCB668E44E.png)
注意，如果还有没有合并冲突的文件，就无法commit，并且给出提示：
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_D27C4672-147C-4533-868A-27A539EC5557.png)
查看状态，合并完之后，我们add刚才修改的文件。
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_83502803-4542-4337-B671-3743538E9435.png)
提交并上传到远程库
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_9AFC9DB7-5B05-440F-81C5-9315E20BE154.png)
ok，搞定收工！

参考：
http://www.cnblogs.com/mengdd/p/3585038.html













