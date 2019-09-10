---
title: linux 常用操作
date: 2016-06-24 21:13:32
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_linux.png
thumbnailImagePosition: left
tags: 
- linux
comments: true
metaAlignment: center
categories: 技术博文
---
记得校招那会，我一个前端和一堆后台朋友去面试，经常被考到linux的相关知识。当时还总觉得，前端学这些都没用，所以一直也没有去了解一下，哪怕是简单的命令。腾讯一学长每逢聚会，都会语重心长的说：“有空学学linux吧，特别有用。”
<!-- more -->
直到有一天，公司架起了一台linux服务器，而我又不得不上去做一些事情的时候，我对linux的态度改观了。所以，那时候我就开始去收集一些常用的命令，并记在笔记中。
当然，另一个很重要的原因就是：
{% alert info %}
mac os是unix内核的桌面版操作系统，而linux是类unix操作系统。
{% endalert %}
所以，你还有不学的理由吗？（我会告诉你面试笔试会考吗🐒）

#### 一、ps 命令用于查看当前正在运行的进程，grep 是搜索
如下是查看所有进程里 cmd 是 java 的进程信息
```sh
ps -ef | grep java
```
-aux 显示所有状态
```sh
ps -aux | grep java
```

#### 二、kill 命令用于终止进程
-9 表示强迫进程立即停止，通常用 ps 查看进程 PID ，用 kill 命令终止进程
```sh
kill -9 [PID]
```

#### 三、查看linux系统版本
```sh
uname -a
```

#### 四、退出linux服务器
```sh
exit
```

#### 五、创建目录
```sh
mkdir abc
```

#### 六、修改文件名
```sh
mv abc abc_1
```

#### 七、删除文件件或者文件夹
```sh
rm -rf test
```
关于参数：
-f, --force    忽略不存在的文件，从不给出提示。
-i, --interactive 进行交互式删除
-r, -R, --recursive   指示rm将参数中列出的全部目录和子目录均递归地删除。
-v, --verbose    详细显示进行的步骤
--help     显示此帮助信息并退出
--version  输出版本信息并退出

#### 八、执行拷贝
```sh
cp -rb test test1
```
格式如下：
cp [选项] 源文件或目录 目的文件或目录
关于参数：
-b 同名，备份原来文件
-f 强制覆盖同名文件
-r 按递归方式保留原目录结构复制文件

#### 九、新建文件
```sh
echo jartto > test.txt
cat > test.txt
less > test.txt
vi test.txt
touch test.txt
cp > test.txt
mv > test.txt
```
注意：
- cat会进入编辑模式，输入内容，然后ctrl＋c退出
- less --help可以查看帮助信息
- vi会进入vim编辑器，编写完毕!wq，保存并退出。
- cp是拷贝，mv是移动，勉强算一种方式吧

关于vim可以阅读本篇文章：[熟练使用vim编辑器](http://jartto.wang/2015/10/03/skilful-with-vim/)

#### 十、新建文件夹
```sh
mkdir test
```
这样就会在当前目录下生成一个文件夹。

#### 十一、linux系统重启
```sh
reboot
shutdown -r now 立刻重启（root用户）
shutdown -r 10 过10分钟自动重启（root用户）
shutdown -r 23:00 在23:00的时候重启（root用户）
```
#### 十二、关机命令
```sh
halt 立刻关机
poweroff 立刻关机
shutdown -h now 立刻关机（root用户）
shutdown -h 10 10分钟后自动关机（root用户）

#### 十三、~/表示一个目录
~/.ssh/config，这个是什么意思呢？
~ 表示你的home目录，通过 cd ~ 可以进入你的home目录。
.ssh 可以是文件，也可以是目录，既然有 ~/.ssh/config 这种写法，那.ssh肯定是个目录。
.开头的文件表示隐藏文件，这里.ssh就是个隐藏的目录文件。
config很显然是个文件。

参考：
http://www.cnblogs.com/lucyjiayou/archive/2012/02/24/2366194.html
http://www.educity.cn/linux/1441125.html
http://www.cnblogs.com/peida/archive/2012/10/26/2740521.html









