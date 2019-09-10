---
title: 如何让搜索引擎抓取ajax内容？
date: 2016-06-30 15:32:38
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/ajax_5DE53823-F851-4AD0-9BE5-6BD2492D36F9.png
thumbnailImagePosition: left
tags: 
- ajax
comments: true
metaAlignment: center
categories: 技术博文
---
说到ajax的缺点，很多人都会想到它不利于seo的一面。在大搜索时代，搜索引擎都搜不到我们的页面，你肯定会有一种淡淡忧伤。你以为你尽力了吗，显然没有，那么我们该如何做？
<!-- more -->
#### 一、我们先来回顾ajax的一些缺点，
①破坏了浏览器的后退按钮，使之无法运行；
②对搜索引擎不友好；
③不支持跨域请求；
④ajax的脚本语言是嵌入在HTML页面中的，通过查看源代码或者firebug等工具可直接查看，不利于项目代码的保密。

其中第二条就提到了，对搜索引擎不友好。什么意思呢？这样来说吧，越来越多的网站开始采用“单页面结构”，整个网站只有一张网页，采用ajax技术，根据用户的输入，加载不同的内容。 

这种做法的好处是用户体验好、节省流量，缺点是ajax内容无法被搜索引擎抓取。因为搜索引擎会去爬取整个页面的内容，以便统计数据达到搜索的目的，而ajax请求的文件在用户操作之前是放在服务器上面的，搜索引擎再强大，也无法搜索到这些信息或者是资源。

因此，缺点显而易见。以前想到过这方面的问题，但是却没有好的解决方案，直到看到这篇文章，不禁拍案叫绝，特分享出来，供大家参考：
[how_to_make_search_engines_find_ajax_content](http://www.ruanyifeng.com/blog/2013/07/how_to_make_search_engines_find_ajax_content.html)

#### 二、如何解决
1.用History API替代井号结构，让每个井号都变成正常路径的URL，这样搜索引擎就会抓取每一个网页。
```
example.com/1
example.com/2
example.com/3
```
2.定义一个JavaScript函数，处理Ajax部分，根据网址抓取内容（假定使用jQuery）。
```js
function anchorClick(link) {
　　var linkSplit = link.split('/').pop();
　　$.get('api/' + linkSplit, function(data) {
　　　　$('#content').html(data);
　　});
}
```
再定义鼠标的click事件。
```js
$('#container').on('click', 'a', function(e) {
　　window.history.pushState(null, null, $(this).attr('href'));
　　anchorClick($(this).attr('href'));
　　e.preventDefault();
});
```
还要考虑到用户点击浏览器的"前进 / 后退"按钮。这时会触发History对象的popstate事件。
```js
window.addEventListener('popstate', function(e) {
　　anchorClick(location.pathname);
});
```
定义完上面三段代码，就能在不刷新页面的情况下，显示正常路径URL和AJAX内容。
3.设置服务器端。
 因为不使用井号结构，每个URL都是一个不同的请求。所以，要求服务器端对所有这些请求，都返回如下结构的网页，防止出现404错误。
 ```html
<html>
　　<body>
　　　　<section id='container'></section>
　　　　　　<noscript>
　　　　　　　　... ...
　　　　　　</noscript>
　　　　</body>
</html>
```
{% alert success %}
总结，其实意思很明了，就是变着法的用js把url拼写出来，写入地址栏，这样搜索引擎当然就可以抓取页面内容了。
{% endalert %}