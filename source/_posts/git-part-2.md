---
title: git实践系列二：创建新仓库
date: 2015-09-08 14:58:37
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_20150723024349836.jpg
thumbnailImagePosition: left
tags: 
- git
comments: true
metaAlignment: center
categories: 技术博文
---
本节主要介绍如何创建git仓库，以及git常用操作说明。如果你有时间，我建议最好实际去操作一下文中列举出的命令，git的用法不外乎如此。
<!-- more -->
#### 一、进入文件夹，执行git init，创建新仓库
![git初始化](http://7xvi3w.com1.z0.glb.clouddn.com/blog_8DCAB4B8-F6A0-4BD2-88EF-09E5948B1521.png)
#### 二、检出仓库
执行如下命令以创建一个本地仓库的克隆版本：
```sh
git clone /path/to/repository
```
如果是远端服务器上的仓库，你的命令会是这个样子：
```sh
git clone username@host:/path/to/repository
```

#### 三、工作流
非常喜欢下面这张图片，言简意赅的阐明了git工作流，就连画风也是我喜欢的风格：
![git工作流](http://7xvi3w.com1.z0.glb.clouddn.com/blog_7E48F42D-AB02-4E58-B6AB-2507AEA25434.png)

#### 四、添加和提交
你可以提出更改（把它们添加到暂存区），使用如下命令：
```sh
git add <filename>
git add *
```
这是 git 基本工作流程的第一步；使用如下命令以实际提交改动：
```sh
git commit -m "代码提交信息"
```
现在，你的改动已经提交到了 HEAD，但是还没到你的远端仓库。

#### 五、推送改动
你的改动现在已经在本地仓库的 HEAD 中了。执行如下命令以将这些改动提交到远端仓库：
```sh
git push origin master
```
可以把 master 换成你想要推送的任何分支。

如果你还没有克隆现有仓库，并欲将你的仓库连接到某个远程服务器，你可以使用如下命令添加：
```sh
git remote add origin <server>
```
如此你就能够将你的改动推送到所添加的服务器上去了。

#### 六、分支
分支是用来将特性开发绝缘开来的。在你创建仓库的时候，master 是“默认的”分支。在其他分支上进行开发，完成后再将它们合并到主分支上。

![git分支](http://7xvi3w.com1.z0.glb.clouddn.com/blog_68BFE499-42D4-460E-AB94-7E2F8A5455EB.png)

创建一个叫做“feature_x”的分支，并切换过去：
```sh
git checkout -b feature_x
```
切换回主分支：
```sh
git checkout master
```
再把新建的分支删掉：
```sh
git branch -d feature_x
```
除非你将分支推送到远端仓库，不然该分支就是 不为他人所见的：
```sh
git push origin <branch>
```
#### 七、更新与合并
要更新你的本地仓库至最新改动，执行：
```sh
git pull
```
以在你的工作目录中 获取（fetch） 并 合并（merge） 远端的改动。
要合并其他分支到你的当前分支（例如 master），执行：
```sh
git merge <branch>
```
在这两种情况下，git 都会尝试去自动合并改动。遗憾的是，这可能并非每次都成功，并可能出现冲突（conflicts）。 这时候就需要你修改这些文件来手动合并这些冲突（conflicts）。改完之后，你需要执行如下命令以将它们标记为合并成功：
```sh
git add <filename>
```
在合并改动之前，你可以使用如下命令预览差异：
```sh
git diff <source_branch> <target_branch>
```

#### 八、标签
为软件发布创建标签是推荐的。这个概念早已存在，在 SVN 中也有。你可以执行如下命令创建一个叫做 1.0.0 的标签：
```sh
git tag 1.0.0 1b2e1d63ff
```
1b2e1d63ff 是你想要标记的提交 ID 的前 10 位字符。可以使用下列命令获取提交 ID：
```sh
git log
```
你也可以使用少一点的提交 ID 前几位，只要它的指向具有唯一性。

#### 九、替换本地改动
假如你操作失误（当然，这最好永远不要发生），你可以使用如下命令替换掉本地改动：
```sh
git checkout -- <filename>
```

此命令会使用 HEAD 中的最新内容替换掉你的工作目录中的文件。已添加到暂存区的改动以及新文件都不会受到影响。

假如你想丢弃你在本地的所有改动与提交，可以到服务器上获取最新的版本历史，并将你本地主分支指向它：

```sh
git fetch origin
git reset --hard origin/master
```
<span style="color:red;">这个很常用，随时回退，加强记忆哦～</span>
#### 十、其他
内建的图形化 git：
```sh
gitk
```
彩色的 git 输出：
```sh
git config color.ui true
```
显示历史记录时，每个提交的信息只显示一行：
```sh
git config format.pretty oneline
```
交互式添加文件到暂存区：
```sh
git add -i
```

参考：git简明教程，强烈推荐！
http://www.runoob.com/manual/git-guide/














