---
title: 如何配置 Git 对应多个 Repository
date: 2017-12-19 15:46:27
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/github0.png
thumbnailImagePosition: left
tags: 
- git
- github
comments: false
metaAlignment: center
categories: 技术博文 
---
突发奇想，做一个在线的 `Demo`，最经济适用的办法就是利用 `github.io` 来演示了。那么问题来了，我本地一个客户端，如何对应多个 `Github` 账号呢？
<!-- more -->
#### 一、问题描述

#### 二、添加私钥/公钥

#### 三、配置 `config` 文件

#### 四、为 `Github` 添加 `Deploy keys`

#### 五、测试 `ssh` 是否可用
```bash
ssh -T git@github.com
ssh -T git@second.github.com
```
如果出现如下提示，表示配置成功：
```bash
Hi jartto! You have successfully authenticated, but GitHub does not provide shell access.
```
#### 六、`ssh` 如何区别不同的账号
大概原理如下：
1.ssh 客户端是通过类似 git@github.com:githubUserName/repName.git ** 的地址来识别使用本地的哪个私钥的，地址中的 User 是@前面的git， Host 是@后面的github.com。
2.如果所有账号的 User 和 Host 都为 git 和 github.com，那么就只能使用一个私钥。所以要对User 和 Host 进行配置，让每个账号使用自己的 Host，每个 Host 的域名做 CNAME 解析到 github.com，如上面配置中的Host second.github.com。
3.配置了别名之后，新的地址就是git@second.github.com:githubUserName/repName.git**（在添加远程仓库时使用）。

#### 七、异常处理
1.`Permission denied (publickey)`
2.`Permission to jartto1.git denied to jartto`