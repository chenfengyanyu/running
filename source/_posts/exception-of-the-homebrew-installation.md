---
title: 安装Homebrew异常处理
date: 2016-09-08 14:28:31
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/homebrew.png
thumbnailImagePosition: left
tags: 
- homebrew
comments: true
metaAlignment: center
categories: 异常处理
---
mac上面安装程序一般都很顺畅，但是今天想借助homebrew安装mongodb的时候，却出现了罕见的问题。如果你恰巧碰到了这个问题，不用找了，正解在这里！
<!-- more -->
#### 一、安装homebrew
打开[官网](http://brew.sh/index.html)，这里以mac环境示例，命令行输入：
```sh
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
并没有预期中的成功，控制台出现了这样的错误：
{% alert danger %}
It appears Homebrew is already installed. If your intent is to reinstall you
should do the following before running this installer again:
    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
The current contents of /usr/local are bin Cellar CODEOFCONDUCT.md etc git include lib Library LICENSE.txt mysql mysql-5.7.12-osx10.11-x86_64 opt README.md share var .git .github .gitignore
{% endalert %}

#### 二、尝试方案
有点莫名其妙😱，我之前是没有安装过homebrew。难道是我另一台电脑上迁移助手迁移过来的？

不管那么多了，先按照提示的日志试一试：
```sh
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```
执行之后，还是有错误，我们来看看细节：
{% alert danger %}
You may consider to remove them by yourself.
You may want to restore /usr/local's original permissions
  sudo chmod 0755 /usr/local
  sudo chown root:wheel /usr/local
{% endalert %}

真是可恶，日志中又给出了解决方案，我这种性格肯定是要试试的：
```sh
sudo chmod 0755 /usr/local
sudo chown root:wheel /usr/local
```
控制台一闪而过，并没有抛出任何问题，难道我成功了？
{% alert danger %}
It appears Homebrew is already installed. If your intent is to reinstall you
should do the following before running this installer again:
    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
The current contents of /usr/local are bin Cellar CODEOFCONDUCT.md etc git include lib Library LICENSE.txt mysql mysql-5.7.12-osx10.11-x86_64 opt README.md share var .git .github .gitignore
{% endalert %}

#### 三、柳暗花明又一村
又回到了原点，日志提示我homebrew已经安装了，那我来试试：
```sh
brew -v
```
控制台输出了：
```sh
Homebrew 0.9.5 (git revision 468a; last commit 2016-03-02)
```
是安装好了么，内心有些小激动？
```sh
brew update
```
控制台彻底怒了，直接抛出了error：
```sh
Error: /usr/local must be writable!
```
是不是不能更新呢，也许可以安装，我又试了一下：
```sh
brew install mongodb
```
又换了一个马甲，控制台依旧没给我好脸色看：
```sh
Error: Cannot write to /usr/local/Cellar
```

#### 四、终级方案
当然，mac上装不上homebrew肯定是不能忍的，是时候请出google了。这里就不细说一些失败的尝试了，我们直接来看[正解](https://github.com/Homebrew/legacy-homebrew/issues/49895)：

对，没错，就是这两行命令：
```sh
sudo chgrp -R admin /usr/local
sudo chmod -R g+w /usr/local
```
真是药到病除啊，就这样，我顺利的执行了如下命令：
```sh
brew update
brew mongodb
```
哈哈，怎一个爽字了得～🙈