---
title: git实践系列四：添加，提交以及推送
date: 2015-09-11 16:30:15
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_20150723024349836.jpg
thumbnailImagePosition: left
tags: 
- git
comments: true
metaAlignment: center
categories: 技术博文
---
本节重点说明add，commit，push等命令。对了，log也很常用，我会在栗子中做出说明。
<!-- more -->
#### 一、clone远程仓库
```sh
git clone https://github.com/askgreenstone/web.git
```

#### 二、添加tools到暂存区git add tools
添加所有文件可以用*
```sh
git add *    
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_003B17B1-AB84-45B2-B335-0C429212CD82.png)

#### 三、提交到HEAD，执行
```sh
git commit -m ‘jartto test1'
```
> 要提交更改他用git的commit 命令-m选项。如果我们省略-m选项git会打开文本编辑器，在这里我们可以写多行提交信息。

![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_CD3333EC-1CD3-4DCC-B0E3-4ADB7F27B5D9.png)

#### 四、提交后查看日志
```sh
git log
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_CEB38EB9-4F5D-40C1-B7E0-CD03C60BD01C.png)
当然，我们也可以用
```sh
git log --oneline
```
<span style="color:green">压缩每条日志文件，显示到一行，便于我们迅速的浏览最近的一些提交日志。</span>
#### 五、查看某一次提交的细节
```sh
git show e4d5a6e9bea053315e3dea0ff4c98cda93e79ee8
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_B22F29D9-4123-4FDB-B97A-2909EA70F7A9.png)

#### 六、修改一处，并提交，当我们show的时候
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_63482D15-114C-4790-B8F3-36108C38775F.png)

#### 七、推送代码到远程代码库
```sh
git push origin master
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_F708EA0F-7E57-44D4-9E8C-22FFB2751BC7.png)

#### 八、当多人协作时，如果远程仓库变化了，那么此时无法提交代码，而应该执行pull更新本地代码
```sh
git pull
```
![git](http://7xvi3w.com1.z0.glb.clouddn.com/blog_F5C666AB-A5F6-42CF-A3A9-D65AE1CB836C.png)
<span style="color:red">
pull 是fetch和merge的合并命令，为防止错误，一般我们采用fetch，然后再执行merge。
而pull会直接获取代码，然后执行自动合并。</span>

