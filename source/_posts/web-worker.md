---
title: web-worker
date: 2018-12-26 08:47:17
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/manage/logo.jpg
thumbnailImagePosition: left
tags: 
- HTML5
- web
- worker
comments: false
metaAlignment: center
categories: 技术杂谈
---
提到 HTML5 总是让人津津乐道，太多的特性和有趣的 API 让人耳目一新。但是很多童鞋还停留在语义化的阶段，忽视了 HTML5 的强劲之处。这节我们来探讨一下伪多线程 Web-Worker。
<!-- more -->
#### 一、明确 JavaScript 是单线程
{% alert success %}
JavaScript 语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。
{% endalert %}

那么，为什么 JavaScript 不能有多个线程呢？这样能提高效率啊。
JavaScript的单线程，与它的用途有关。作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

所以，为了避免复杂性，从一诞生，JavaScript就是单线程，这已经成了这门语言的核心特征，将来也不会改变。

#### 二、新曙光：web worker
为了利用多核CPU的计算能力，HTML5提出Web Worker标准，允许JavaScript脚本创建多个线程，但是子线程完全受主线程控制，且不得操作DOM。所以，这个新标准并没有改变JavaScript单线程的本质。

Web Workers 是现代浏览器 提供的一个javascript多线程解决方案
-我们可以用web Worker做一些大计算量的操作；
-可以实现轮询，改变某些状态；
-页头消息状态更新，比如页头的消息个数通知；
-高频用户交互，拼写检查，譬如：根据用户的输入习惯、历史记录以及缓存等信息来协助用户完成输入的纠错、校正功能等
-加密：端到端的加密由于对保护个人和敏感数据日益严格的法律规定而变得越来越流行。加密有时候会非常地耗时，特别是如果当你需要经常加密很多数据的时候（比如，发往服务器前加密数据）。这是一个使用 Web Worker 的绝佳场景，因为它并不需要访问 DOM 或者利用其它魔法－它只是纯粹使用算法进行计算而已。一旦在 worker 进行计算，它对于用户来说是无缝地且不会影响到用户体验。
-预取数据：为了优化网站或者网络应用及提升数据加载时间，你可以使用 Workers 来提前加载部分数据以备不时之需。

#### 三、兼容性
![hack](#)

#### 四、基本概念
1.首先记得去判断是否支持
```js
if (window.Worker) {
  ...
}
```
2.创建一个新的 `worker` 很简单
```
Const myWorker = new Worker('worker.js');
```

workers的魔法通过postMessage() 方法和onmessage事件处理函数生效。

3.`postMessage` 用来发送消息，而 `onmessage` 用来监听消息
```js
const worker = new Worker('src/worker.js');

worker.onmessage = e => {
console.log(e.data);
};

worker.postMessage('你好吗!');
```
在主线程中使用时，onmessage和postMessage() 必须挂在worker对象上，而在worker中使用时不用这样做。原因是，在worker内部，worker是有效的全局作用域。

4.异常处理：
```js
worker.onerror = function(error) {
  Console.log(error.message);
  throw error;
};
```

5.终止 `worker`
```js
worker.terminate();
```

worker 线程会被立即杀死，不会有任何机会让它完成自己的操作或清理工作。

6.在 `worker` 线程中，`workers` 也可以调用自己的 `close`  方法进行关闭：
```js
close();
```

#### 五、快速开始
项目结构
```
├── index.html
└── src
    ├── main.js
    └── worker.js
```
Html
```html
<html>

<head>
<title>Web Work Demo</title>
<meta charset="UTF-8" />
</head>

<body>
<div id="app"> Hello Jartto! </div>
<script src="src/main.js"></script>
</body>

</html>
```
main.js
```js
const worker = new Worker('src/worker.js');

worker.onmessage = e => {
const message = e.data;
console.log(`[From Worker]: ${message}`);
document.getElementById('app').innerHTML = message;
};

worker.postMessage(‘写的真好!');
```
Work.js
```js
onmessage = e => {
const message = e.data;
console.log(`[From Main]: ${message}`);
if(message.indexOf('好') > -1) {
postMessage('谢谢支持');
}
};
```