---
title: 如何解决 Git 仓库嵌套问题
date: 2017-12-28 10:51:52
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/gitnest0.png
thumbnailImagePosition: left
tags: 
- git
- nest
comments: true
metaAlignment: center
categories: 异常处理
---
用了很久 Git ，今天却被 Git 嵌套问题卡住了。本文将围绕“如何解决 Git 仓库嵌套”问题展开讨论，并提供解决方案。
<!-- more -->
#### 一、问题描述
{% alert danger %}
Git 仓库嵌套使用后，被嵌套的 Git 仓库不能被外层 Git 仓库检测到。
{% endalert %}
这也就意味着你在被嵌套的 `Git` 仓库中的更改，外层仓库是无感知的。导致这个问题的原因可能如下：
- 在代码中引入其他 `Git` 仓库；
- `Clone` 别人的 `Demo` 并做修改；

{% alert info %}
不看场景的解决方案就是耍流氓。
{% endalert %}

#### 二、场景一：在代码中引入其他 `Git` 仓库
这种情况，需要使用 `submodule`，过程如下：
```bash
git submodule add https://github.com/chenfengyanyu/fmap-demo.git

# 或者使用 clone
git clone --recursive https://github.com/chenfengyanyu/fmap-demo.git
```
这个操作类似于 `Clone` ，但是会在父仓库下面新建 `.gitmodules` 文件，我们看一下内容：
```bash
vi .gitmodules
```
大致如下：
```bash
[submodule "fmap-demo"]
        path = fmap-demo
        url = https://github.com/chenfengyanyu/fmap-demo.git
```
然后做正常提交就可以了。
{% alert info %}
需要注意的是，这样的操作只能提交一个引用，类似：`fmap-demo @ 90758cf`，打开的话，会跳到项目源地址。
{% endalert %}

#### 三、场景二：`Clone` 别人的 `Demo` 并做修改
那么有时候，我们可能会 `Clone` 到一段代码来作为项目的脚手架，这时候嵌套的话，肯定不希望改子仓库的时候，父仓库毫无感知。那么这种情况如何处理呢？

![github](http://7xvi3w.com1.z0.glb.clouddn.com/gitnest1.png)

首先，进入子仓库，也就是被嵌套的 `Git` 仓库，删除 `.git` 目录：
```bash
rm -rf .git
```
{% alert info %}
可以这么理解，一个项目不应该有俩 `Git` 地址，所以断开子仓库的原有联系，也就是删除 `.git` 目录。
{% endalert %}
然后，将你添加的项目拖出当前项目文件，进行提交。之后，再将项目拖回来，返回项目根目录，执行 `add` 操作：
```bash
git add fmap-demo
```

{% alert success %}
这里必须要选择子项目进行 `add`，此时 `add .` 和 `add *` 都无效。
{% endalert %}

之后正常 `commit` 和 `push`，就可以了。
