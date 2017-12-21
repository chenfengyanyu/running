---
title: 趣谈document.write
date: 2015-05-04 23:13:35
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/animal_20141024125135.jpg
thumbnailImagePosition: left
tags: 
- js
comments: true
metaAlignment: center
categories: 技术博文
---
沉寂了好些天，我又杀回来啦。虽然一天天的吃饭睡觉打豆豆，但是博客还是要坚持写的，今天就来点有意思的。时间有点晚了，今天就少扯一点，下面直接跳过广告，进入正题。
<!--more-->
document.write语句在我们日常书写js中经常遇到，可是你有没有仔细研究过它的语法呢？我们先来演示一个过程：

第一步：打开浏览器，输入任意网址，如m.letv.com
![演示图片一](http://7xvi3w.com1.z0.glb.clouddn.com/blog_AFEF8FEA-8D8F-4410-88F1-7380EAE0BD5E.png)

第二步：在控制台输入
```javascript
document.write('aaa')
```
回车
![演示图片二](http://7xvi3w.com1.z0.glb.clouddn.com/blog_33DC1AEC-5A77-4329-A19C-54C457F31485.png)

好了，原先输入的网站看不到了，取而代之的是aaa。不着急，我们接着往下走。

第三步：接着控制台输入
```javascript
document.write('bbb')
```
回车
![演示图片三](http://7xvi3w.com1.z0.glb.clouddn.com/blog_24C22A66-98C6-45A4-B7A5-34E27BB97BEF.png)

咦，这是什么鬼，aaa和bbb拼接在了一起？

<span style="color:red">那么，问题来了，当我们第一次敲入aaa回车后，原先的网站内容被清空了。可是当我们接着敲入bbb的时候，aaa和bbb竟然走在了一起。显而易见，这其中定有某种不为人知的秘密，可是，到底发生了什么呢？</span>

翻阅了几篇文章和api之后，问题总算有了下文。我们先不急着揭晓答案，下面列出一些重要线索：

> 1.document.write() 方法可以用在两个方面：页面载入过程中用实时脚本创建页面内容，以及用延时脚本创建本窗口或新窗口的内容。该方法需要一个字符串参数，它是写到窗口或框架 中的HTML内容。这些字符串参数可以是变量或值为字符串的表达式，写入的内容常常包括HTML标记语言。

> 2.在载入页面后，浏览器输出流自动关闭。

> 3.document.close()用来关闭输出流。

> 4.在延时脚本的最后一个document.write()方法后面，必须确保含有document.close()方法，不这样做 就不能显示图像和表单。

> 5.只能在HTML输出中使用document.write，如果在文档加载后使用该方法，则会覆盖整个文档。

根据上面的线索，我们很快就能分析出来上述过程究竟是怎样的。
<p style="color:blue">
	<b>首先</b>，我们敲入aaa发生在页面载入之后，此时浏览器输出流已经自动关闭，也就意味着整个文档会被覆盖，那么页面消失也就显得理所当然了。
	<b>其次</b>，当我们接着输入bbb的时候，输出流并未关闭，所以bbb自然就拼接在了aaa的后面，并非覆盖整个文档。
</p>
为了证明上述过程，我作如下尝试：
打开控制台，输入
```javascript
document.close()
```
回车后输入
```javascript
document.write('ccc')
```
![演示图片四](http://7xvi3w.com1.z0.glb.clouddn.com/blog_1E982136-E344-4BB7-9ACB-86C6E7374325.png)

正如我们所想，close关闭了输出流，ccc为新的输出流，当然会覆盖整个文档了。

回过头来看，其实好简单的一个问题，竟然写了这么多字，简直太啰嗦了。哎，洗洗睡吧~

参考文档：
API：http://www.w3school.com.cn/jsref/dom_obj_document.asp
document.write用法：http://blog.csdn.net/youyedemeng/article/details/7925833











