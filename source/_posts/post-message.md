---
title: HTML5 之跨域通讯（postMessage）
date: 2019-06-11 16:31:21
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/postmsg/logo.png
thumbnailImagePosition: left
tags: 
- HTML5
- postMessage
comments: false
metaAlignment: center
categories: 技术博文
---
很多情况下，我们受到浏览器的安全策略限制。如何能规避此限制，并且能安全的使用跨域通讯，这就不得不介绍一下 postMessage 了。
<!-- more -->
#### 一、关于 postMessage
{% alert success %}
window.postMessage() 方法可以安全地实现跨源通信。
{% endalert %}

通常，对于两个不同页面的脚本，只有当执行它们的页面位于具有相同的协议（通常为 `https`），端口号（`443` 为 `https` 的默认值），以及主机 (两个页面的模数 `Document.domain` 设置为相同的值) 时，这两个脚本才能相互通信。

`window.postMessage()` 方法提供了一种受控机制来规避此限制，只要正确的使用，这种方法就很安全。

#### 二、理解过程
`window.postMessage()` 方法被调用时，会在所有页面脚本执行完毕之后向目标窗口派发一个  `MessageEvent` 消息。 该 `MessageEvent` 消息有四个属性： 

1.`message` 属性表示该 `message` 的类型；
2.`data` 属性为 `window.postMessage` 的第一个参数；
3.`origin` 属性表示调用 `window.postMessage()` 方法时调用页面的当前状态；
4.`source` 属性记录调用 `window.postMessage()` 方法的窗口信息。

关于更多细节，我们可以查看[ MDN 文档](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage)，这里就不赘述了。

#### 三、兼容性
![caniuse](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/postmsg/use.png)

通过上面的图片，我们可以看出来，几乎所有的浏览器都支持了 `postMessage`，所以放心大胆的去使用吧。

#### 四、用法简介
基本用例：
```js
otherWindow.postMessage(message, targetOrigin, [transfer]);
```

1.`otherWindow`
其他窗口的一个引用，比如 `iframe` 的 `contentWindow` 属性、执行 `window.open` 返回的窗口对象、或者是命名过或数值索引的 `window.frames`。

2.`message`
将要发送到其他 `window` 的数据。它将会被结构化克隆算法序列化。

{% alert info %}
这意味着我们可以不受什么限制的将数据对象安全的传送给目标窗口而无需自己序列化。
{% endalert %}

3.`targetOrigin`
通过窗口的 `origin` 属性来指定哪些窗口能接收到消息事件，其值可以是字符串 「*」（表示无限制）或者一个 `URI`。

在发送消息的时候，如果目标窗口的协议、主机地址或端口这三者的任意一项不匹配 `targetOrigin` 提供的值，那么消息就不会被发送；

只有三者完全匹配，消息才会被发送。这个机制用来控制消息可以发送到哪些窗口。

我们举个例子，当用 `postMessage` 传送密码时，这个参数就显得尤为重要，必须保证它的值与这条包含密码的信息的预期接受者的 `origin` 属性完全一致，来防止密码被恶意的第三方截获。

如果你明确的知道消息应该发送到哪个窗口，那么请始终提供一个有确切值的 `targetOrigin`，而不是 `*`。

{% alert danger %}
需要注意：不提供确切的目标将导致数据泄露到恶意站点。
{% endalert %}

4.`transfer`
是一串和 `message` 同时传递的 `Transferable` 对象. 这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权。

#### 五、事件监听
我们来看下面这段代码：
```js
window.addEventListener("message", receiveMessage, false);
function receiveMessage(event)
{
  // For Chrome, the origin property is in the event.originalEvent
  // object. 
  // 这里不准确，chrome没有这个属性
  // var origin = event.origin || event.originalEvent.origin; 
  var origin = event.origin
  if (origin !== "http://jartto.wang:8080")
    return;
  // ...
}
```

1.`data`
从其他 `window` 中传递过来的对象。

2.`origin`
调用 `postMessage`  时消息发送方窗口的 `origin` . 这个字符串由 协议、`://`、域名、` : 端口号` 拼接而成。

例如 `https://jartto.wang (隐含端口 443)`、`http://jartto.net(隐含端口 80)`、`http://jartto.com:8080`。请注意，这个 `origin` 不能保证是该窗口的当前或未来 `origin` ，因为`postMessage` 被调用后可能被导航到不同的位置。

3.`source`
对发送消息的窗口对象的引用， 我们可以使用此来在具有不同 `origin` 的两个窗口之间建立双向通信。

#### 六、简单应用
1.监听 `message` 事件：
```js
window.addEventListener("message", receiveMessage, false);

function receiveMessage(event)
{
  console.log('get it !!!',event);
}
```

2.发送数据
```js
window.postMessage({
    name: 'Jartto',
    say: 'hello~',
    arr: [1,2,3]
}, '*');
```
