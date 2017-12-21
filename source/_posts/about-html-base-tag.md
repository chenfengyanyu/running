---
title: 关于html中的base标签
date: 2016-10-01 21:59:20
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/20161206_html.png
thumbnailImagePosition: left
tags: 
- html
- base
comments: true
metaAlignment: center
categories: 技术博文
---
日常工作中，我们可能会更改项目文件的目录结构，这样的话，就引发了一个问题，静态资源的路径如何处理？
<!-- more -->
#### 一、问题由来
因为项目要根据浏览器语言版本切中英文网站，所以需要将静态资源放到各自语言版本目录中，所以我不得不更改目录结构。导致的问题就是，静态资源路径异常，图片，css以及js无法正常加载。

当然，这也不算什么大问题，全局搜索路径，整体替换就ok了。可是我并不想这么做，有没有一种更简洁的办法达到这个目的呢？

#### 二、方案：nginx控制静态资源访问路径
首先，我想到了去nginx中配置静态资源的访问路径，对路径进行重新拼接或者重定向，如下：
```sh
rewrite /assets /zh/assets redirect;
```
{% alert danger %}
这样理论上是可行的，但是却不是最好的解决方案。
{% endalert %}
譬如：我们需要中英文版本切换，此时，逻辑依赖真实的物理路径，而不是一个在访问时候重定向的路径。

#### 三、方案：base标签
正在我一筹莫展的时候，同事给出建议：你试试base呗？我突然间意识到，这可能就是我要找的最优解决方案。

那么base究竟怎么用呢？w3school给出了这样一个🌰：
```html
<html>
	<head>
		<base href="http://www.w3school.com.cn/i/" />
	</head>
	<body>
		<img src="eg_smile.gif" />
	</body>
</html>
```
请注意，我们已经为图像规定了一个相对地址。由于我们已经在 head 部分规定了一个基准 URL，浏览器将在如下地址寻找图片：
```
http://www.w3school.com.cn/i/eg_smile.gif
```
用法很简单，我们设置base标签，给一个基准href，那么其他的链接都是以此为基准，那么路径变更问题就迎刃而解了。

base还提供了另一个参数：
```html
<base target="_blank" />
```
什么意思呢，请看下面的例子：
```html
<html>
<head>
<base href="http://www.w3school.com.cn/i/" />
<base target="_blank" />
</head>

<body>
<img src="eg_smile.gif" />
<a href="http://jartto.wang">Jartto's blog</a>
</body>
</html>
```
{% alert info %}
请注意，Jartto's blog链接会在新窗口中打开，即使链接中没有 target='_blank' 属性。
{% endalert %}
这是因为 base 元素的 target 属性已经被设置为 '_blank' 了。

#### 四、注意事项
一行代码就搞定了我大量重复替换的工作，真是不错！但是，别高兴的太早，这里有一点需要我们注意。
{% alert info %}
base适合于全路径或者相对路径，如果你写的绝对路径，那么base路径将会失效。
{% endalert %}
我们来看下面这个栗子：
```html
<base href="/zh" />
<link rel='stylesheet' href='/assets/lib/swiper.min.css'>
```
如上所示，解析出来结果是：
```html
href='http://jartto.wang/assets/lib/swiper.min.css'
```
而我想要的是：
```html
href='http://jartto.wang/zh/assets/lib/swiper.min.css'
```
所以，这里是需要特别注意的。

当然，根据不同的项目需求，此类问题也会存在其他最优解。一个字总结：具体问题具体对待！

#### 五、参考
html base标签：
http://www.w3school.com.cn/tags/tag_base.asp
