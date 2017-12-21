---
title: 熟练使用vim编辑器
date: 2015-10-03 16:30:27
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_EFB40354-DB38-495B-AE74-0F3D7613657F.png
thumbnailImagePosition: left
tags: 
- vim
comments: true
metaAlignment: center
categories: 技术博文
---
经常使用linux环境开发和mac的同学，对vim肯定不会陌生。我们可以简单的执行创建，编辑等常用操作。熟悉使用它可以让我们在日常工作中得心应手，高逼格的编程，何乐而不为呢？
<!-- more -->
> Vim是从 vi 发展出来的一个文本编辑器。代码补全、编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用，和Emacs并列成为类Unix系统用户最喜欢的文本编辑器。

#### 一、vi是什么
Vi是一种广泛存在于各种UNIX和Linux系统中的文本编辑程序。
Vi不是排版程序，只是一个纯粹的文本编辑程序。
Vi是全屏幕文本编辑器，它没有菜单，只有命令。
Vi不是基于窗口的，所以，这个多用途编辑程序可以用于在任何类型的终端上编辑各式各样的文件。
Vi的功能十分强大，但是命令繁多，熟练掌握起来有一定难度。
Vi的发明者:bill joy

![vi](http://7xvi3w.com1.z0.glb.clouddn.com/blog_D2F66C28-1AD9-41B2-9808-5C0C41195FD3.png)

#### 二、如何启动vi
```sh
vi  filename 
```
或者
```sh
vi
```
{% alert info %}
如果filename已经存在，vi会打开现存文件，如果是一个新文件，vi会创建它
{% endalert %}

#### 三、vi的退出
在命令行模式下输入:q，:q！，:wq或者:x，可以退出vi

:w                  保存
:w filename         另存为filename
:wq!                保存并退出
:wq! filename       以filename为文件名保存后退出
:q!                 不保存退出
:x                  保存并退出，功能和:wq!相同

#### 四、vi工作模式
{% alert info %}
vi有三种工作模式，分别为：命令模式、插入模式（编辑模式）和末行模式
{% endalert %}

上述这三种模式分别对应不同的操作，它们之间可以进行切换，这也是我们在使用vi编辑器的时候需要特别注意的地方。

a.命令模式：进入Vi后，首先进入的就是命令模式，等待用户输入编辑命令，此时输入的字母将作为编辑命令来解释。
b.插入模式：在命令模式下输入插入命令i、附加命令a、打开命令o等命令可以进入插入模式，在插入模式下，用户输入可以对文本进行编辑，输入的任何字符都被当作文件内容保存起来。【Esc】键可以返回命令模式。
c.末行模式：在命令模式下，按【:】键可以进入末行模式，Vi光标会显示在窗口的最后一行，以“:”作为末行模式的提示符，等待用户输入命令。末行命令执行后，Vi自动回到命令模式。

#### 五、命令大全
- 移动光标
h或者向左方向键：光标向左移动一个字符
l或者向右方向键：光标向右移动一个字符
k或者向上方向键：光标向上移动一个字符
j或者向下方向键：光标向下移动一个字符

- 搜索与替换,
- 删除，复制与粘贴,
- 插入模式
- 文件操作相关

当然，还有很多的操作，这里我就不一一罗列了。如果你想了解跟多vi相关知识，请参考vi详解，讲的非常详细，大力推荐！


参考：
[百科](http://baike.baidu.com/link?url=YmxWPPSNS-S4jnZJonEi-I1MX7XguyBGSewvVOuL0yDzXRRp0IiwCwFhIuSSRsKspBUAK8HuLIv1IfHyrl8WmgxL4ry_DQScqTZTwFzlCOS)  
[vi详解](http://c.biancheng.net/cpp/html/1520.html)