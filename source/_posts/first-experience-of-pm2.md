---
title: PM2初体验
date: 2016-06-27 13:37:27
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/pm2_3E086008-0B62-4169-957E-46EE544F070E.png
thumbnailImagePosition: left
tags: 
- pm2
comments: true
metaAlignment: center
categories: 技术博文
---
Advanced, production process manager for Node.js
<!-- more -->
#### 一、安装
```sh
sudo npm install pm2 -g
```
#### 二、启动一个项目
进入目标文件夹，
```sh
pm2 start fs.js
```
![pm2](http://7xvi3w.com1.z0.glb.clouddn.com/pm2_1CB6B592-F09D-4596-947D-CC83EDB4C69C.png)

#### 三、尽管去启动你的项目
![pm2](http://7xvi3w.com1.z0.glb.clouddn.com/pm2_A34F2719-404E-4EBB-A25C-3BB0CF91D52B.png)

#### 四、监测所有的进程
```sh
pm2 monit
```
![pm2](http://7xvi3w.com1.z0.glb.clouddn.com/pm2_F5685E2B-4411-4B70-95F8-6F20F93694CE.png)

#### 五、列出所有进程
```sh
pm2 list
```
![pm2](http://7xvi3w.com1.z0.glb.clouddn.com/pm2_F61BF742-15D7-48D8-A366-CFC5B9A0EA4C.png)

#### 六、查看一个具体的进程详细情况
通过id或者name来查看某一具体进程：
```sh
pm2 show <id|name>
```
![pm2](http://7xvi3w.com1.z0.glb.clouddn.com/pm2_A37555E8-09C9-4EE8-9719-759A75A6DF4D.png)

#### 七、停止进程
```sh
pm2 stop <id|name|all|json|stdin>
```
![pm2](http://7xvi3w.com1.z0.glb.clouddn.com/pm2_15778CD6-CB49-45A1-BEBD-87F9EB41DB94.png)

#### 八、重启进程
```sh
pm2 restart <id|name|all|json|stdin>
```
![pm2](http://7xvi3w.com1.z0.glb.clouddn.com/pm2_99103C3D-DBA7-4E32-8823-FA79DD374052.png)

#### 九、删除进程
```sh
pm2 delete <name|id|script|all|json|stdin>
```
![pm2](http://7xvi3w.com1.z0.glb.clouddn.com/pm2_A47D4592-BD8C-472D-B316-FB8E6767AF43.png)

#### 十、查看日志
```sh
pm2 logs
```
![pm2](http://7xvi3w.com1.z0.glb.clouddn.com/pm2_C29214F7-4CD0-4880-AE33-DB26FCDC2D1E.png)

#### 十一、自动重启应用watch
```sh
pm2 start fs.js --watch
```
![pm2](http://7xvi3w.com1.z0.glb.clouddn.com/pm2_D6BB0D23-D618-4BB8-B29D-9A830B8E0E51.png)

{% alert info %}
这里需要注意：pm2 stop 2 将不能停止watch，必须pm2 stop --watch 2
{% endalert %}

#### 十二、相关资源：
1.官网
http://pm2.keymetrics.io/
2.github地址
https://github.com/Unitech/PM2
3.nodejs监视系统
https://app.keymetrics.io/#/register
4.快速开始
https://github.com/Unitech/PM2/blob/master/ADVANCED_README.md#quick-start-1