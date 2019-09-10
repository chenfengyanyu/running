---
title: HTML5 之消息通知（Web Notification）
date: 2018-10-30 08:01:56
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/h5.png
thumbnailImagePosition: left
tags: 
- html5
- notification
comments: false
metaAlignment: center
categories: 技术博文 
---
关于 HTML5 ，写了不少文章，总觉得相关的高级 API 都得过一遍。系统的了解，站在更高的高度去思考问题，这样才能事半功倍。
<!-- more -->
#### 一、先睹为快
我们先来尝试一个最简单的例子，打开 `chrome` 开发者工具，粘贴代码进去直接运行：
```js
new Notification('Jartto\'s 消息通知', {
  dir: 'rtl',
  body: '这是一个消息，通过 Web Notification 发送，作为测试！',
  icon: 'https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/logo.jpeg'
})
```
并没有出现什么消息通知，怎么回事？不要着急，接着往下看。

#### 二、用户授权
按照上面的示例，我们运行了，但是并没有成功调起消息推送框。这时候需要查看一下用户是否授权，控制台输入：
```
> Notification.permission
```
很快我们就会发现，原来用户许可是 `default`，因为不知道用户的选择，所以浏览器的行为与 `denied` 时相同，即为拒绝。

{% alert danger %}
所以问题就转变为，如何获取用户授权？
{% endalert %}

#### 三、获取授权
```js
Notification.requestPermission(function (permission) {
  // 如果用户同意，就可以向他们发送通知
  if (permission === 'granted') {
    var notification = new Notification('Hi Jartto!');
  }
});
```
{% alert info %}
Notification.requestPermission 这个方法只能被用户行为调用（比如：onclick 事件），并且不能被其他的方式调用。
{% endalert %}

#### 四、兼容性
看到这些 `HTML5` 的高级 `API`，总是会有一些顾虑。例子虽好，用起来也很方便，但是到底兼容性如何，这是一个值得思考的问题。

![caniuse](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/h5/notificity.png)

可以看到，基本上所有浏览器都支持了，除了 `iOS Safari` 和 `Opera Mini` ，所以移动端应用需谨慎。

#### 五、API 文档
{% alert success %}
我们先来系统性的梳理一下 API，很简单，花 5 分钟过一下。
{% endalert %}

用例：
```
let notification = new Notification(title, options)
```
参数：
- title：一定会被显示的通知标题
- options：一个被允许用来设置通知的对象。它包含以下属性：
  - dir : 文字的方向；它的值可以是 `auto`（自动）, `ltr`（从左到右）, `or rtl`（从右到左）
  - lang: 指定通知中所使用的语言。这个字符串必须在 `BCP 47 language tag` 文档中是有效的。
  - body: 通知中额外显示的字符串
  - tag: 赋予通知一个 `ID` ，以便在必要的时候对通知进行刷新、替换或移除。
  - icon: 一个图片的 `URL`，将被用于显示通知的图标。

属性：
Notification.permission: 一个用于表明当前通知显示授权状态的字符串。可能的值包括：
- `denied` (用户拒绝了通知的显示),
- `granted` (用户允许了通知的显示),
- `default` (因为不知道用户的选择，所以浏览器的行为与 `denied` 时相同)

方法：
- Notification.onclick：处理 `click` 事件的处理，每当用户点击通知时被触发。
- Notification.onshow：处理 `show` 事件的处理，当通知显示的时候被触发。
- Notification.onerror：处理 `error` 事件的处理，每当通知遇到错误时被触发。
- Notification.onclose：处理 `close` 事件的处理，当用户关闭通知时被触发。
- Notification.requestPermission()：用于当前页面想用户申请显示通知的权限。
- Notification.close()：用于关闭通知。



#### 六、完整示例
按照上面 `API` ，我们来写一个完整的示例：
```js
function notifyMe() {
  // 先检查浏览器是否支持
  if (!("Notification" in window)) {
    alert("This browser does not support desktop notification");
  }

  // 检查用户是否同意接受通知
  else if (Notification.permission === "granted") {
    // If it's okay let's create a notification
    var notification = new Notification("Hi there!");
  }

  // 否则我们需要向用户获取权限
  else if (Notification.permission !== 'denied') {
    Notification.requestPermission(function (permission) {
      // 如果用户同意，就可以向他们发送通知
      if (permission === "granted") {
        var notification = new Notification("Hi there!");
      }
    });
  }


  // 最后，如果执行到这里，说明用户已经拒绝对相关通知进行授权
  // 出于尊重，我们不应该再打扰他们了
}
```

