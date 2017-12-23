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
假定我现在的场景是：只有一台 `Mac` ，而我想配置连接两个 `Github` 账号，你可能会失败。因为：
{% alert success %}
GitHub will use the key as means to identify you when you connect to them via SSH. As such, you cannot have multiple accounts with the same key, as GitHub won’t be able to tell then which of your accounts you want to use.
{% endalert %}

大致意思就是：你只有一个 SSH 公钥的话，你只能连接一个 Github 账号，否则 Github 将无法区分你想使用哪个账号。提示很明显，既然是这样，那我们就尝试添加新的私钥/公钥来解决问题。

#### 二、添加私钥/公钥
1.在 Mac 命令行中输入：
```bash
ls ~/.ssh
```
如果有 `id_rsa` 和 `id_rsa.pub`，说明已存在一对私钥/公钥。
2.创建新的私钥/公钥，并指定私钥名称，比如 `id_rsa_x`（ x 为任意名称）
```bash
ssh-keygen -t rsa -f ~/.ssh/id_rsa_1 -C "yourmail@xxx.com"
```
{% alert info %}
操作完成后，该目录会多出 `id_rsa_1` 和 `id_rsa_1.pub` 两个文件。
{% endalert %}

#### 三、配置 `config` 文件
按照上述过程，我们已经创建完成了私钥/公钥，然后在 `~/.ssh/` 文件夹下创建一个 `config` 文件并打开：
```bash
vim config
```
编辑 `config` 文件，配置不同的仓库指向不同的私钥文件。
```bash
# 第一个账号，默认使用的账号
Host github.com
HostName github.com
User git
IdentityFile ~/.ssh/id_rsa
# 第二个账号
Host second.github.com  # second为前缀名，可以任意设置
HostName github.com
User git
IdentityFile ~/.ssh/id_rsa_1
```
{% alert info %}
配置完 `config` 文件，接下来就是要告诉 `Github` 相关信息了。
{% endalert %}

#### 四、为 `Github` 添加 `Deploy keys`
首先，查看两个账号的私钥，并拷贝：
```bash
more id_rsa.pub
more id_rsa_1.pub
```
打开 `Github`，`Setting - Deploy keys - Add deploy key`，将 `id_rsa.pub` 和 `id_rsa_1.pub` 分别填入不同的 `Github` 账号，并保存。

#### 五、测试 `ssh` 是否可用
```bash
ssh -T git@github.com
ssh -T git@second.github.com
```
如果出现如下提示，表示配置成功：
```bash
Hi jartto! You have successfully authenticated, but GitHub does not provide shell access.
```
#### 六、注意事项
因为要对应不同的账号，所以需要取消 `git` 全局用户名/邮箱的设置，设置独立的用户名/邮箱：
```
# 取消全局 用户名/邮箱 配置
$ git config --global --unset user.name
$ git config --global --unset user.email

# 进入项目文件夹，单独设置每个repo 用户名/邮箱
$ git config user.email "xxxx@xx.com"
$ git config user.name "xxxx"
```

#### 七、`ssh` 如何区别不同的账号
有意思的是，`ssh` 是如何区分不同的账号呢，大概[原理](http://www.jianshu.com/p/04e9a885c5c8)如下：
1.`ssh` 客户端是通过类似 `git@github.com:githubUserName/repName.git **` 的地址来识别使用本地的哪个私钥的，地址中的 `User` 是 `@` 前面的 `git`， `Host` 是 `@` 后面的 `github.com`。

2.如果所有账号的 `User` 和 `Host` 都为 `git` 和 `github.com`，那么就只能使用一个私钥。所以要对 `User` 和 `Host` 进行配置，让每个账号使用自己的 `Host`，每个 `Host` 的域名做 `CNAME` 解析到 `github.com`，如上面配置中的 `Host second.github.com`。

3.配置了别名之后，新的地址就是 `git@second.github.com:githubUserName/repName.git**`（在添加远程仓库时使用）。

#### 八、异常处理
如果配置过程一帆风顺的话，可能也就不会有这篇总结了。
{% alert success %}
一些细节问题可能会导致你所有努力前功尽弃。
{% endalert %}
1.`Permission denied (publickey)`
如果你碰到这样的问题，可以[打出异常](https://help.github.com/articles/error-permission-denied-publickey/)：
```
ssh -vT git@github.com
```
然后根据提示的消息来查找解决方案

2.`Permission to jartto1.git denied to jartto`
这个意思很明显，访问被拒绝了，应该是账号没有正确对应。解决方案就是：
```bash
git remote set-url origin jartto.github.com:jartto/jartto.github.io.git
```

#### 九、参考
[Git 之同一台电脑连接多个远程仓库](http://www.jianshu.com/p/04e9a885c5c8)
[Git 最著名报错 “ERROR: Permission to XXX.git denied to user” 终极解决方案](http://www.jianshu.com/p/12badb7e6c10)
[error-permission-denied-publickey](https://help.github.com/articles/error-permission-denied-publickey/)