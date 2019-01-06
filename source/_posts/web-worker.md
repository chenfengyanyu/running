---
title: HTML5之多线程（Web Worker）
date: 2018-12-26 08:47:17
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/webworker/logo.png
thumbnailImagePosition: left
tags: 
- HTML5
- web
- worker
comments: false
metaAlignment: center
categories: 技术杂谈
---
提到 HTML5 总是让人津津乐道，太多的特性和有趣的 API 让人耳目一新。但是很多童鞋还停留在语义化的阶段，忽视了 HTML5 的强劲之处。
<!-- more -->
这节我们来探讨一下多线程 Web-Worker。

#### 一、明确 JavaScript 是单线程
{% alert success %}
JavaScript 语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。
{% endalert %}

听起来有些匪夷所思，为什么不设计成多线程提高效率呢？我们可以假设一种场景：
假定 `JavaScript` 同时有两个线程，一个线程在某个 `DOM` 节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

{% alert info %}
作为浏览器脚本语言，`JavaScript` 的主要用途是与用户互动，以及操作 `DOM`。
{% endalert %}

这决定了它只能是单线程，否则会带来很复杂的同步问题。为了避免复杂性，从一诞生，`JavaScript` 就是单线程，这已经成了这门语言的核心特征，估计短期内很难改变。

#### 二、新曙光：Web Worker
单线程始终是一个痛点，为了利用多核 `CPU` 的计算能力，`HTML5` 提出 `Web Worker` 标准，允许 `JavaScript` 脚本创建多个线程。但是子线程完全受主线程控制，且不得操作 `DOM`。
{% alert info %}
所以，这个新标准并没有改变 `JavaScript` 单线程的本质。
{% endalert %}
`Web Workers` 是现代浏览器提供的一个 `JavaScript` 多线程解决方案，我们可以找到很多使用场景：
1.我们可以用 `Web Worker` 做一些大计算量的操作；
2.可以实现轮询，改变某些状态；
3.页头消息状态更新，比如页头的消息个数通知；
4.高频用户交互，拼写检查，譬如：根据用户的输入习惯、历史记录以及缓存等信息来协助用户完成输入的纠错、校正功能等
5.加密：加密有时候会非常地耗时，特别是如果当你需要经常加密很多数据的时候（比如，发往服务器前加密数据）。
6.预取数据：为了优化网站或者网络应用及提升数据加载时间，你可以使用 `Workers` 来提前加载部分数据以备不时之需。

加密是一个使用 `Web Worker` 的绝佳场景，因为它并不需要访问 `DOM` 或者利用其它魔法，它只是纯粹使用算法进行计算而已。随着大众对个人敏感数据的日益重视，信息安全和加密也成为重中之重。这可以从近期的 12306 用户数据泄露事件中体现出来。

{% alert success %}
一旦在 Worker 进行计算，它对于用户来说是无缝地且不会影响到用户体验。
{% endalert %}

#### 三、兼容性
![hack](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/webworker/hack.png)

#### 四、基本概念
1.首先记得去判断是否支持
```js
if (window.Worker) {
  ...
}
```
2.创建一个新的 `worker` 很简单
```js
const myWorker = new Worker('worker.js');
```
{% alert success %}
postMessage() 方法和 onmessage 事件处理函数是 Workers 的黑魔法。
{% endalert %}

3.`postMessage` 用来发送消息，而 `onmessage` 用来监听消息
```js
const worker = new Worker('src/worker.js');

worker.onmessage = e => {
  console.log(e.data);
};

worker.postMessage('你好吗!');
```
在主线程中使用时，`onmessage` 和 `postMessage()` 必须挂在 `worker` 对象上，而在 `worker` 中使用时不用这样做。原因是，在 `worker` 内部，`worker` 是有效的全局作用域。

4.异常处理：
```js
worker.onerror = function(error) {
  console.log(error.message);
  throw error;
};
```

5.终止 `worker`
```js
worker.terminate();
```
{% alert danger %}
`worker` 线程会被立即杀死，不会有任何机会让它完成自己的操作或清理工作。
{% endalert %}


6.在 `worker` 线程中，`workers` 也可以调用自己的 `close`  方法进行关闭：
```js
close();
```

#### 五、快速开始
为了快速掌握，我们来做一个小例子：项目结构如下
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

worker.postMessage('写的真好!');
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
代码很简单，主线程发送：「写的真好！」
web worker 收到消息，发现内容中含有「好」字，回传给主线程：「谢谢支持」

#### 六、局限性
1.在 `worker` 内，不能直接操作 `DOM` 节点，也不能使用 `window` 对象的默认方法和属性。然而我们可以使用大量`window` 对象之下的东西，包括 `WebSockets`，`IndexedDB` 以及 `FireFox OS` 专用的 `Data Store API` 等数据存储机制。

这里举个例子，我们修改 `main.js`：
```js
const worker = new Worker('src/worker.js');

worker.onmessage = e => {
  const message = e.data;
  console.log(`[From Worker]: ${message}`);
  document.getElementById('app').innerHTML = message;
};

+ worker.onerror = function(error) {
+   console.log(error);
+   worker.terminate();
+ };

worker.postMessage('写的真好!');
```
再来修改 `work.js`
```js
+ alert('jartto');
onmessage = e => {
  const message = e.data;
  console.log(`[From Main]: ${message}`);
  if(message.indexOf('好') > -1) {
    postMessage('谢谢支持');
  }
};
```
这时候运行就会报出：
![error](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/webworker/error.png)

这是因为：`worker.js` 执行的上下文，与主页面 `HTML` 执行时的上下文并不相同，最顶层的对象并不是 `Window`，`woker.js` 执行的全局上下文，而是 `WorkerGlobalScope`，[下文](#)我们具体说明。

2.`workers` 和主线程间的数据传递通过这样的消息机制进行：双方都使用 `postMessage()` 方法发送各自的消息，使用`onmessage` 事件处理函数来响应消息（消息被包含在 `Message` 事件的 `data` 属性中）。
{% alert info %}
这个过程中数据并不是被共享而是被复制。
{% endalert %}

3.同源限制
分配给 `Worker` 线程运行的脚本文件，必须与主线程的脚本文件同源。

4.文件限制
`Worker` 线程无法读取本地文件，即不能打开本机的文件系统`（file://）`，它所加载的脚本，必须来自服务器。

5.不允许本地文件
```
Uncaught SecurityError: Failed to create a worker: 
script at '(path)/worker.js' 
cannot be accessed from origin 'null'.
```
{% alert danger %}
Chrome doesn't let you load web workers when running scripts from a local file.
{% endalert %}

那如何解决呢？我们可以启动一个本地服务器，建议使用 `http-server`，简单易用。

6.内容安全策略
有别于创建它的 `document` 对象，`worker` 有它自己的执行上下文。因此普遍来说，`worker` 并不受限于创建它的`document`（或者父级 `worker` ）的内容安全策略。

我们来举个例子，假设一个 `document` 有如下头部声明：
```
Content-Security-Policy: script-src 'self'
```
这个声明有一部分作用在于，禁止它内部包含的脚本代码使用 `eval()` 方法。然而，如果脚本代码创建了一个 `worker`，在 `worker` 上下文中执行的代码却是可以使用 `eval()` 的。

{% alert info %}
为了给 worker 指定 CSP，必须为发送 worker 代码的请求本身加上一个 CSP。
{% endalert %}

关于 [CSP（Content security policy 内容安全策略）](http://jartto.wang/2018/11/12/outline-of-CSP/)，可以看我之前的这篇文章。

有一个例外情况，即 `worker` 脚本的源如果是一个全局性的唯一的标识符（例如，它的 `URL` 指定了数据模式或者 `blob`），`worker`则会继承创建它的 `document` 或者 `worker` 的 `CSP`。


#### 七、扩展：WorkerGlobalScope
关于 ，我们可以在 [`MDN`](https://developer.mozilla.org/zh-CN/docs/Web/API/WorkerGlobalScope) 上面找到文档：
1.`self`：
我们可以使用 `WorkerGlobalScope` 的 `self` 属性来获取这个对象本身的引用。

2.`location`：
`location` 属性返回当线程被创建出来的时候与之关联的 `WorkerLocation` 对象，它表示用于初始化这个工作线程的脚步资源的绝对 `URL`，即使页面被多次重定向后，这个 `URL` 资源位置也不会改变。

3.`close`：
关闭当前线程，与 `terminate` 作用类似。

4.`caches`：
当前上下文得 `CacheStorage`，确保离线可用，同时可以自定义请求的响应。

5.`console`：
支持 `console` 语法。

6.`importScripts`
我们可以通过 `importScripts()` 方法通过 `url` 在 `worker` 中加载库函数。

7.`XMLHttpRequest`
有了它，才能发出 `Ajax` 请求。

8.可以使用：
- `setTimeout/setInterval`
- `addEventListener/postMessage`

还有很多 `API` 可以使用，这里就不一一举例了。


#### 八、异常处理
当 `worker` 出现运行中错误时，它的 `onerror` 事件处理函数会被调用。它会收到一个扩展了 `ErrorEvent` 接口的名为 `error` 的事件。该事件不会冒泡并且可以被取消。

{% alert info %}
为了防止触发默认动作，worker 可以调用错误事件的 preventDefault() 方法。
{% endalert %}

错误事件我们常用如下这三个关键信息：
- `Message`：可读性良好的错误消息；
- `Filename`：发生错误的脚本文件名；
- `Lineno`：发生错误时所在脚本文件的行号；

```js
worker.onerror = function(error) {
  console.log(error.message);
  throw error;
};
```

#### 九、参考文章
[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers)
[从webWorker到serviceWorker](http://jixianqianduan.com/frontend-javascript/2014/06/05/webworker-serviceworker.html)
[浅谈webWorker](https://www.cnblogs.com/giggle/p/5350288.html)