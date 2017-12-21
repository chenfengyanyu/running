---
title: git实践系列十：常用技巧
date: 2015-09-25 09:15:02
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_20150723024349836.jpg
thumbnailImagePosition: left
tags: 
- git
comments: true
metaAlignment: center
categories: 技术博文
---
git的基本语法也说完了，具体实践也演示了，甚至连错误汇总都整理了，你以为完了？图样图森破！技巧我都留在后面了，当然，本着geek的精神，我还是要分享出来。
<!-- more -->
#### 一、查看git全局配置
```sh
git config -l
```

#### 二、如何忽略文件文件？
vim 直接创建 .gitignore文件，然后写入规则，!wq保存并退出。

#### 三、关于Mac环境中的.DS_Store文件
有两种方法：
- 直接删除系统中的.DS_Store文件，且不允许再生成；
- 使用忽略文件，如问题二；

此处扩展一下：
> 在 Mac OS X 系统下，几乎绝大部分文件夹中都包含 .DS_Store 隐藏文件，这里保存着针对这个目录的特殊信息和设置配置，例如查看方式、图标大小以及这个目录的一些附属元数据。

执行如下命令，删除文件.DS_Store：
```sh
sudo find / -name ".DS_Store" -depth -exec rm {} \;
```
然后，不再生成.DS_Store：
```sh
defaults write com.apple.desktopservices DSDontWriteNetworkStores true
```
原谅.DS_Store的乱入，欲知详情，请移步博文：[《Mac下如何删除.DS_Store文件》](http://jartto.wang/2015/10/05/how-to-delete-DS-Store-of-Mac/)

#### 四、对比文件更改
```sh
git diff <file name>
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_B160A3D5-EF83-4F70-9D73-7AF409C89BD4.png)

对比a版本和b版本的差别，语法不是很复杂，了解一下基本都能看懂了。
{% alert danger %}
“---”未修改版本，“+++”修改版本，显示1-4行，“-”被移除行，“+”添加行
{% endalert %}


#### 五、查看文件更改纪录
```sh
git blame [file name]
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_9447163C-5A62-4A16-A3B0-9B16EAE46F4F.png)
当程序出现问题时，先去指责别人是人们的基本天性。如果你的程序崩溃了，使用git blame可以很容易找出罪魁祸首。这个命令可以将文件中的每一行的作者、最新的变更提交和提交时间展示出来。


#### 六、查看仓库历史纪录
```sh
git log --oneline
```
压缩模式，在每个提交的旁边显示经过精简的提交哈希码和提交信息，以一行显示。
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_D38F4E88-C6AE-4744-AE9A-B08BB81DEA57.png)

#### 七、图形模式
```sh
git log --graph
```
图形模式，使用该选项会在输出的左边绘制一张基于文本格式的历史信息表示图。如果你查看的是单个分支的历史记录的话，该选项无效。
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_BE553FCB-802E-498A-A3F7-62AFDD85DE48.png)

#### 八、显示所有分支的历史记录
```sh
git log --all
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_D10BF41B-8AEE-405B-8D63-CB7C1AFD9E5A.png)

#### 九、列出commit
git reflog则列出了head曾经指向过的一系列commit。要明白它们只存在于你本机中；而不是你的版本仓库的一部分，也不包含在push和merge操作中。
```sh
git reflog
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_468D2BFA-D205-45A2-B58C-F9BDCDCE98A4.png)

#### 十、回退到上一版本
```sh
#回退所有内容到上一个版本
git reset HEAD^
```
举例来说明：
```sh
#回退README.md这个文件的版本到上一个版本
git reset HEAD^ README.md
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_D485623B-1561-47FF-8362-01BEEBB2C070.png)
```sh
git reset --filename
```
仅将文件filename 的改动撤出暂存区，暂存区中其他文件不改变。此操作相当于命令git add filename 的反向操作。

#### 十一、创建git全局忽略文件
```sh
git config --global core.excludesfile ~/.gitignore_global
vim ~/.gitignore_global
```
首先打开git全局忽略文件，然后通过vim编辑器添加配置项，!wq保存搞定收工。







