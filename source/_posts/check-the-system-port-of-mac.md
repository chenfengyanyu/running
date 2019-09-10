---
title: Mac下查看端口占用情况
date: 2016-09-28 20:08:32
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/mac.png
thumbnailImagePosition: left
tags: 
- mac
- linux
comments: true
metaAlignment: center
categories: 技术博文
---
当你兴致冲冲的写完代码，准备在服务器上一览网站的容貌时，突然电脑告诉你，服务器启动异常。嗯？那肿么办？来看看我的悲催历程。
<!-- more -->
#### 一、问题由来：
当我想在本地启动一个http-server来查看静态网页的时候，发现服务并不能被启动。查看原因后，得知：
```sh
Error: listen EADDRINUSE 0.0.0.0:8080
```
#### 二、分析问题：
{% alert info %}
看到错误，我们很容易就判断出来了，很明显，8080端口被占用了。
{% endalert %}
解决思路有两个：
其一，修改http-server服务器端口号；
[查阅文档](http://www.cnblogs.com/lucker/p/4108838.html)命令行直接输入：
```sh
http-server -p 6666
```
好的，http-server不能启动的问题解决了，可是我并不打算收手👻

其二，我想查看8080端口被谁占用了。

那么问题来了，mac下如何查看端口被占用情况呢？众所周知，mac os是Unix内核的桌面版操作系统，Linux是类Unix操作系统。

{% alert success %}
bingo！问题转嫁了，如何查看linux端口号情况？
{% endalert %}

#### 三、查看端口号
我查到了这个：
可以使用netstat命令。netstat命令可以显示网络连接，路由表，接口状态，伪装连接，网络链路信息和组播成员组等信息。
命令格式：netstat [选项]
常用参数：
```sh
netstat [-AaLlnW] [-f address_family | -p protocol]
netstat [-gilns] [-f address_family]
netstat -i | -I interface [-w wait] [-abdgRt]
netstat -s [-s] [-f address_family | -p protocol] [-w wait]
netstat -i | -I interface -s [-f address_family | -p protocol]
netstat -m [-m]
netstat -r [-Aaln] [-f address_family]
netstat -rs [-s]
```
都是什么意思呢，一不做二不休，我来一个个试一下。
```sh
#列出所有端口
netstat 

#列出所有 tcp 端口 
netstat -at

#显示网络接口列表
netstat -i

#显示网络工作信息统计表
netstat -s

#显示伪装的网络连线
netstat -m

#显示核心路由信息
netstat -r

#显示合并的信息
netstat -rs
routing:
       	0 bad routing redirects
       	0 dynamically created routes
       	0 new gateways due to redirects
       	4294944612 destinations found unreachable
       	0 uses of a wildcard route
       	8 routes not in table but not freed
```

#### 四、查看某一端口占用情况：
进一步可以使用lsof命令显示占用该端口的进程情况 。
```sh
#命令格式：lsof -i :端口
lsof -i:8080
```
控制台输出如下信息：
```sh
COMMAND   PID   USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
nginx     634 Jartto    6u  IPv4 0x8800c1295e443e43      0t0  TCP *:http-alt (LISTEN)
nginx     635 Jartto    6u  IPv4 0x8800c1295e443e43      0t0  TCP *:http-alt (LISTEN)
WeChat  93768 Jartto  119u  IPv6 0x8800c12977474f33      0t0  TCP 192.168.1.*
```

#### 五、如何杀掉进程？
查看进程，命令行输入：
```sh
ps -ef | more
```
找到需要杀掉的进程，执行：
```sh
kill -9 pid
```
某种情况下，我们也可以根据进程名称来杀进程，如下：
```sh
kill -9 name
```

#### 六、数据太多？
在尝试netstat的各种命令过程中，控制台给出的都是满满的一屏。有没有更好的展现方式，或过滤选项呢？可以试试[awk]()和[grep](http://jartto.wang/2016/10/12/grasp-linux-grep/)。

这里就不展开讨论了，后续我会补充相关文章，此处先占个坑吧！


#### 七、参考文档
1.[netstat详解](http://www.cnblogs.com/ggjucheng/archive/2012/01/08/2316661.html)
2.[netstat命令](http://man.linuxde.net/netstat)







