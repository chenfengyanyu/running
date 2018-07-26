---
title: 细数小程序的坑
date: 2018-02-08 14:31:33
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/wx2.png
thumbnailImagePosition: left
tags: 
- 微信
- 小程序
- mini programs
comments: false
metaAlignment: center
categories: 技术博文
---
最近做了一个应用，把玩了一下小程序。总体感觉还是可以，但是限制太多，最适合的场景可能就是手机小型应用。这节，我们来细数一下小程序开发过程的坑。

<!-- more -->

{% alert success %}
查看[原文](http://jartto.wang/2018/02/08/count-pit-of-mini-programs/)，获得更好的体验哦～
{% endalert %}

#### 一、小程序注册前提
小程序注册的前提是，没有注册过任何的微公众平，如未注册过订阅号、服务号之类的。邮箱要求也是是这样的：

{% alert warning %}
未被公众平台注册，未被开放平台注册，未被个人微信号绑定的邮箱
{% endalert %}

恰巧之前开发过微信公众号，结果登陆后死活找不到小程序，后来才发现了这个前提。教训就是，一定要好好审题，提前看看小程序的介绍说明。

#### 二、大小限制
是的，你没有看错，小程序对项目大小做了限制，你的项目不能超过 `2048kb`。这个大小的项目文件，能做的事情就不言自明了。
{% alert info %}
我们可以做工具类，服务类，投票类，在线答题类的小应用。
{% endalert %}

此外，还需要注意的是，如果你用了过多的图片资源，要么精简要么转在线地址，否则，你的小程序很可能大小失控。

{% alert success %}
这里有一些建议，优化后的效果还是很不错的。
{% endalert %}

1. 图片资源过多的话，建议本地和远程协调，将项目大小控制在一定范围内。全用远程图片，可能会出现加载白屏问题；
2. 如果你用了 wepy 构建工具，切记上线前一定要检查 dist 文件夹中的冗余文件，它只添加而不会删除；
3. 图片可以优化一下，减少大小，推荐使用 [TinyPng](https://tinypng.com) 无损压缩一下；

#### 三、水平滚动与垂直滚动
这个 `bug` 就比较气人了，当你使用了 `swiper` 的时候，左右滑动，屏幕上下也会跟着抖动。一般情况，我们禁掉浏览器的滚动行为：
```js
//阻止浏览器默认事件
event.preventDefault();
```
移动端处理水平滚动和垂直滚动的原理大致是这样的：
```js
var xx,yy,XX,YY,swipeX,swipeY ;
div.addEventListener('touchstart',function(event){
    xx = event.targetTouches[0].screenX ;
    yy = event.targetTouches[0].screenY ;
    swipeX = true;
    swipeY = true ;
})
div.addEventListener('touchmove',function(event){
    XX = event.targetTouches[0].screenX ;
    YY = event.targetTouches[0].screenY ;
    if(swipeX && Math.abs(XX-xx)-Math.abs(YY-yy)>0)  //左右滑动
    {
        event.stopPropagation();//组织冒泡
        event.preventDefault();//阻止浏览器默认事件
        swipeY = false ;
        //左右滑动
    }
    else if(swipeY && Math.abs(XX-xx)-Math.abs(YY-yy)<0){  //上下滑动
        swipeX = false ;
        //上下滑动，使用浏览器默认的上下滑动
    }
})
```
本身并不是什么很难的问题，然而，在微信中就没有任何办法了。很多人会说，你 `overflow` 一下啊，我只能呵呵表示，并甩给你一篇文章[微信小程序 在 canvas 画布上划动，如何阻止页面上下动？](https://www.zhihu.com/question/52852717)

还可以看看这篇文章：[解决微信小程序自定义弹窗滚动与页面滚动冲突](https://www.jianshu.com/p/6022f7346ed2)

这里摘自[官方文档](https://mp.weixin.qq.com/debug/wxadoc/dev/component/scroll-view.html)：Bug & Tip
- tip: 请勿在 scroll-view 中使用 textarea、map、canvas、video 组件
- tip: scroll-into-view 的优先级高于 scroll-top
- tip: 在滚动 scroll-view 时会阻止页面回弹，所以在 scroll-view 中滚动，是无法触发 onPullDownRefresh
- tip: 若要使用下拉刷新，请使用页面的滚动，而不是 scroll-view ，这样也能通过点击顶部状态栏回到页面顶部

#### 四、图片组件
既然叫组件了，那说明已经被动过手脚了，具体的表现是啥呢？小程序会给 `image` 组件默认宽高：
{% alert warning %}
注：image 组件默认宽度 300px 、高度 225px
{% endalert %}
看起来是完美的，可是如果你要图片自适应外容器呢？OK，小程序提供了 `mode` 属性，但是并不完全，你还需要这么写：
```html
<image style="width: 100%;" mode="widthFix" src="http://jartto.wang"></image>
```
是的，你还需要设置他的宽度，才能使用 `mode="widthFix"`，否则又开始默认了，沮丧😂。至于 `mode` 提供的其他裁切属性 `top`, `bottom`, `center` 等等，不太清楚具体的应用场景是什么。

#### 五、脚本不能使用 window 等对象
页面的脚本逻辑是在 `JsCore` 中运行，`JsCore` 是一个没有窗口对象的环境，所以不能在脚本中使用 `window`，也无法在脚本中操作组件。

{% alert info %}
当然，zepto/jquery 会使用到 window 对象和 document 对象，所以无法使用。
{% endalert %}

#### 六、wx.navigateTo 无法打开页面
一个应用同时只能打开 5 个页面，当已经打开了 5 个页面之后，`wx.navigateTo` 不能正常打开新页面。请避免多层级的交互方式，或者使用 `wx.redirectTo`。

{% alert success %}
这个应该不算是 `bug` 吧，我们姑且认为它是一个 `feature`。
{% endalert %}

#### 七、样式表不支持级联选择器
`WXSS` 支持以.开始的类选择器。如：
```css
.normal_view {
  color: #000000;
  padding: 10px;
}
```
可以使用标签选择器，控制同一类组件的样式。如：使用 `input` 标签选择器控制 `<input/>` 的默认样式。
```css
input {
  width: 100px;
}
```

#### 八、不能直接操作 Page.data
小程序中的规则，只能遵守：
{% alert info %}
避免在直接对 Page.data 进行赋值修改，请使用 Page.setData 进行操作才能将数据同步到页面中进行渲染。
{% endalert %}

此外，如果你有了 `WePY` 框架的话，赋值操作是有优化的。`WePY` 使用脏数据检查对 `setData` 进行封装，在函数运行周期结束时执行脏数据检查，一来可以不用关心页面多次`setData` 是否会有性能上的问题，二来可以更加简洁去修改数据实现绑定，不用重复去写 `setData` 方法。代码如下：
```js
this.title = 'this is title';
```
需注意的是，在异步函数中更新数据的时，必须手动调用 `$apply` 方法，才会触发脏数据检查流程的运行。如：
```js
setTimeout(() => {
    this.title = 'this is title';
    this.$apply();
}, 3000);
```

#### 九、本地资源无法通过 WXSS 获取
当你需要平铺页面背景的时候，肯能会碰到这样的异常：
![background-image](http://7xvi3w.com1.z0.glb.clouddn.com/wx3.png)

很清楚，小程序已经告诉你怎么回事了：`background-image`：可以使用网络图片，或者 `base64`，或者使用 `<image/>` 标签。


#### 十、修改窗口的背景色
整个小程序的代码是包在 `page` 标签中的，所以使用 `page` 标签选择器，可以修改顶层节点的样式：
```css
page {
  display: block;
  min-height: 100%;
  background-color: red;
}
```

#### 十一、不可设置网络请求的 referer
网络请求的 `referer` 是不可以设置的，格式固定为 `https://servicewechat.com/{appid}/{version}/page-frame.html`，其中 `{appid}` 为小程序的 `appid`，`{version}` 为小程序的版本号，版本号为 0 表示为开发版、体验版以及审核版本，版本号为 `devtools` 表示为开发者工具，其余为正式版本。

这个，文档中有细致的说明，这里就不赘述了。

#### 十二、map 组件总是在最上层
`map`、`canvas`、`video`、`textarea` 是由客户端创建的原生组件，原生组件的层级是最高的，所以页面中的其他组件无论设置 `z-index` 为多少，都无法盖在原生组件上。 原生组件暂时还无法放在 `scroll-view` 上，也无法对原生组件设置 `css` 动画。

#### 十三、你的域名必须备案
如果你是个人搞的小程序，一定要记得找一个备案的域名绑定主机服务器，否则，你连调用接口都费劲。
{% alert danger %}
不能使用 IP 地址访问后端接口，必须经过 `ICP` 备案。
{% endalert %}

#### 十四、转发功能需要自己设置
页面的转发需要自行设置，并没有区分个人和企业用户。唯一麻烦的就是，需要每个页面都设置转发规则。
```js
Page({
  onShareAppMessage: function (res) {
    if (res.from === 'button') {
      // 来自页面内转发按钮
      console.log(res.target)
    }
    return {
      title: '自定义转发标题',
      path: '/page/user?name=jartto',
      success: function(res) {
        // 转发成功
      },
      fail: function(res) {
        // 转发失败
      }
    }
  }
})
```
具体，请查看[文档地址](https://mp.weixin.qq.com/debug/wxadoc/dev/api/share.html#onshareappmessageoptions)。

#### 十五、canvas 的 drawImage 性能问题
当使用图片裁切功能的时候，发现异常卡顿，找到了微信官网更新日志：
- 问题描述：canvas 的 drawImage 性能变差，频繁调动会出现卡顿
- 系统：iOS
- 出现版本：微信 6.6.0
- 预计修复时间：1 月版本
- 兼容方案：尽量减少传入的图片尺寸

如果遇到了这种情况，那么只能等微信官方修复了。

#### 十六、写在最后
零零散散记了这么多，全是小程序的问题，但是我们也不能否定小程序的价值，毕竟人家还是有很多优点的嘛。我知道这么做很烦人，但是我也特别希望在我刚开始接触某个技术或者框架的时候，能有人告诉我，这些坑你不要再踩了。

这并不是在浇小程序的凉水，毕竟小程序需要发展嘛。我一直相信如果大家都能提出问题，那么对一些东西是有正向激励的。
