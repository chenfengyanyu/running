---
title: 将html转化成markdown文档
date: 2016-08-11 23:55:46
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/bs_convert.png
thumbnailImagePosition: left
tags: 
- html
- markdown
comments: true
metaAlignment: center
categories: 技术博文
---
当我们需要去写大量的技术文档的时候，你肯定不愿意用html一个个页面去写，然后再去调样式。那么简洁高效的markdown语法，你不仿试试。
<!-- more -->
这里我们不再介绍markdown语法，如果你还不了解，点击这里[Markdown语法浅析](http://jartto.wang/2015/07/05/Comprehension-of-Markdown-Grammatical-Structure/)。

好了，进入正题，我们来搞一件比较有趣的事情。

#### 一、html语法转化成markdown显示
首先，你需要引入markdown.css[下载地址](https://github.com/mrcoles/markdown-css/blob/master/markdown.css)：
```html
<link rel="stylesheet" href="../css/markdown.css">
```
其次，照常编写你的html代码
```html
<h2 id="section-2">第一部分</h2>
<hr>
<code>注意事项</code>
<blockquote>hello jartto</blockquote>
打开摇一摇：<a href="">超链接</a>
<p></p>
<div class="alert alert-info" role="alert">当云子完成部署后，进入开发阶段。这里我们通常会有三个需求：获取云子设备的部署位置及相关配置的信息，批量修改云子设备的一些配置如UUID、功率、频率、密码等，检测配置是否正确。
</div>
```
最后，我们来看一下效果：
![html2md](http://7xvi3w.com1.z0.glb.clouddn.com/md_html2md.png)

#### 二、html显示成类似 Markdown 纯文本的形式
markdown.css[下载地址](http://mrcoles.com/demo/markdown-css/) 是一个很有意思的 CSS 样式表。它可以让 html 显示成类似 markdown 纯文本的形式。

{% alert info %}
请注意和标题一中的markdown.css区别
{% endalert %}

用法类似，首先引入markdown.css文件；
```html
<link rel="stylesheet" href="../css/markdown.css">
```
其次，编写html规则：
```html
<h2 id="section-1">第一部分</h2>
<blockquote>jartto</blockquote>
<code>just test it!</code>
```
最后，我们来看一下效果：
{% image fancybox left clear group:travel http://7xvi3w.com1.z0.glb.clouddn.com/md_html2md2.png 250px %}

#### 三、在线转换工具
当然，我们也可以依赖在线工具来做一些简单的转换，以适应不同的应用场景。
http://www.atool.org/html2markdown.php

#### 四、本地编写markdown语法，编译成html语法显示
下面介绍一种逼格较高的方式：([官网地址](http://strapdownjs.com))
{% alert info %}
Strapdown.js makes it embarrassingly simple to create elegant Markdown documents.
{% endalert %}
```html
<!DOCTYPE html>
<html>
<title>Hello Strapdown</title>

<xmp theme="united" style="display:none;">
# Markdown text goes in here

## Chapter 1

Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore
et dolore magna aliqua. 

## Chapter 2

Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut
aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse
cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in
culpa qui officia deserunt mollit anim id est laborum.
</xmp>

<script src="http://strapdownjs.com/v/0.2/strapdown.js"></script>
</html>
```
好了，这样你就可以向往常一样使用简单的markdown语法了。

#### 五、其他
https://github.com/showdownjs/showdown
http://www.cnblogs.com/yunfeifei/p/4482495.html
http://showdownjs.github.io/demo/
http://www.tuicool.com/articles/Q7jQFjZ

