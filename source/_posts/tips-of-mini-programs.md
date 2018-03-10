---
title: 小程序开发 Tips
date: 2018-03-06 18:59:12
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/wxtips0.png
thumbnailImagePosition: left
tags: 
- mini-programs
- wx
comments: false
metaAlignment: center
categories: 技术博文
---
继「[细数小程序的坑](http://jartto.wang/2018/02/08/count-pit-of-mini-programs/)」之后，我们来说一些小程序开发中可以用到的技巧，毕竟抱怨并不能解决问题，更何况还「人在屋檐下，不得不低头」。
<!-- more -->
本文是小程序系列的第三篇文章，如果你还没有实际开发小程序的经验，建议按照顺序阅读如下文章：
1.[快速上手小程序](http://jartto.wang/2018/01/25/quick-start-mini-programs/)
2.[细数小程序的坑](http://jartto.wang/2018/02/08/count-pit-of-mini-programs/)
3.[小程序开发 Tips](http://jartto.wang/2018/03/06/tips-of-mini-programs/)

#### 一、小程序生命周期
我们先来熟悉一下小程序的生命周期函数：
- data: Object
  - 页面初始数据
- onLoad: Function
  - 监听页面加载
- onReady: Function
  - 监听页面渲染完成
- onShow: Function
  - 监听页面显示
- onHide: Function
  - 监听页面隐藏
- onUnload: Function
  - 监听页面卸载

{% alert success %}
接下来我们来看一张状态图，深入了解一下：
{% endalert %}
![状态图](http://7xvi3w.com1.z0.glb.clouddn.com/wxtips1.png)

如果用代码来表示小程序的生命周期的话，大致如下：
```js
//获取应用实例
var app = getApp()
Page({
  // 通过data初始化数据
  data: {
    blog: 'jartto',
  },
  // 监听页面开始加载的状态，页面加载完成之后就不会在执行
  onLoad: function () {
    console.log('onLoad()');
  },
  // 监听页面渲染完成，完成之后不会在执行
  onReady: function() {
    console.log('onReaday()');
  },
  // 监听页面显示，当从当前页面调转到另一个页面，另一个页面销毁时会再次执行
  onShow: function() {
    console.log('onShow()');
  },
  // 监听页面隐藏，当前页面跳转到另一个页面时会执行
  onHide: function() {
    console.log('onHide()');
  },
  // 当页面销毁时调用
  onUnload: function() {
    console.log('onUnload');
  }
})
```

#### 二、小程序返回监听
提到返回监听，可能会有几种业务场景，如：
1.监听页面返回按钮，警告用户是否确认离开？
2.提醒用户进行保存操作；
3.更新一些状态值；
...
{% alert danger %}
遗憾的是，小程序并没有提供类似的方法，所以只能自己想办法实现。
{% endalert %}


如果你真正了解了上文描述的生命周期，那么就有两个方式可以变通解决问题：
- 其一：通过监听 `onShow` 或 `onHide` 来实现；
- 其二：页面被 `destroy` 后，`onUnload` 来实现。

#### 三、小程序 onUnload 未触发
{% alert info %}
如果你碰到了页面 `onUnload` 未触发的问题，可能原因是：页面类型不对。
{% endalert %}

正常的业务开发中，我们都会使用小程序的框架 `wepy`，那么页面就会被分为三类：
1.小程序入口
```js
<script>
import wepy from 'wepy';
export default class extends wepy.app {
    config = {
        "pages":[
            "pages/index/index"
        ],
        "window":{
            "backgroundTextStyle": "light",
            "navigationBarBackgroundColor": "#fff",
            "navigationBarTitleText": "WeChat",
            "navigationBarTextStyle": "black"
        }
    };
    onLaunch() {
        console.log(this);
    }
}
</script>

<style lang="less">
/** less **/
</style>
```
入口文件 `app.wpy` 中所声明的小程序实例继承自 `wepy.app` 类，包含一个 `config` 属性和其它全局属性、方法、事件。其中 `config` 属性对应原生的 `app.json` 文件，`build` 编译时会根据 `config` 属性自动生成 `app.json` 文件，如果需要修改 `config` 中的内容，请使用微信提供的相关 `API`。

2.页面 `page.wpy`
```js
<script>
import wepy from 'wepy';
import Counter from '../components/counter';

export default class Page extends wepy.page {
    config = {};
    components = {counter1: Counter};

    data = {};
    methods = {};

    events = {};
    onLoad() {};
    // Other properties
}
</script>

<template lang="wxml">
    <view>
    </view>
    <counter1></counter1>
</template>

<style lang="less">
/** less **/
</style>
```

3.组件 `com.wpy`
```js
<template lang="wxml">
    <view>  </view>
</template>

<script>
import wepy from 'wepy';
export default class Com extends wepy.component {
    components = {};

    data = {};
    methods = {};

    events = {};
    // Other properties
}
</script>

<style lang="less">
/** less **/
</style>
```
组件文件 `com.wpy` 中所声明的组件实例继承自 `wepy.component` 类，除了不需要 `config` 配置以及页面特有的一些生命周期函数之外，其属性与页面属性大致相同。

仔细看完这些类型之后，「onUnload 未触发」这个问题就不攻自破了。你还会去 `component` 中调用 `onShow` 方法吗？

#### 四、小程序取消授权怎么办？
一般情况下，当我们需要获取用户信息的时候，就需要用到授权 `API`，那么问题来了，如果用户直接点击了拒绝，你该如何处理？试用了很多小程序后，我得到如下几种方式：
1.拒绝将直接退出小程序，这个做法粗暴而又简单，但用户体验极差；
2.一直提示用户去开启设置；

我们不去探讨这种做法对不对，这里我提供一种方式：
```js
getUserInfo() {
  let self = this;
  wx.getUserInfo({
    success: function(res) {
      let userInfo = res.userInfo;
      // let nickName = userInfo.nickName
      // let avatarUrl = userInfo.avatarUrl
      // let gender = userInfo.gender //性别 0：未知、1：男、2：女
      // let province = userInfo.province
      // let city = userInfo.city
      // let country = userInfo.country
    },
    fail: function(error) {
      wx.showModal({
        title: '警告',
        confirmText: '授权',
        cancelText: '不授权',
        content: `若不授权微信登陆，则无法使用「AI识图」功能；点击重新获取授权，则可重新使用；若点击不授权，后期还使用小程序，需在微信「发现小程序」，删掉「AI识图」，重新搜索授权登陆，方可使用。`,
        success: function(res) {
          if (res.confirm) {
            wx.openSetting({
              success:function(res){
                // todo
              }
            })
          } else if (res.cancel) {
            console.log('用户点击取消');
          }
        }
      })
    }
  })
}
```
检测到用户授权失败，则提醒用户开启「用户设置」，用户开启后，调用你的初始化方法。

#### 五、小程序 wepy 中启用 async/await
1.进入项目根目录，安装 `runtime` 包：
```bash
npm install wepy-async-function --save
```
2.修改 `wepy.config.js` 加入 `runtime` 配置：
```json
babel: {
  "presets": [
    "env"
  ],
  "plugins": [
    "transform-export-extensions",
    "syntax-export-extensions"
  ]
}
```
3.在 `app.wpy` 中引入引入 `runtime` 包：
```js
import 'wepy-async-function'; 
```
4.在 `app.wpy` 中使 `API promise` 化：
```js
export default class extends wepy.app {
  constructor () {
    super();
    this.use('promisify');
  }
}
```
5.重启编译：
```bash
wepy build --no-cache
```

详细信息可阅读：[wepy 项目中使用 async await](https://github.com/Tencent/wepy/wiki/wepy项目中使用async-await)

#### 六、小程序的 textarea 光标问题
如果你使用了 `textarea` 框，同时又有更改内容值的需求，那么恭喜你，此 `bug` 必现。去小程序开发者论坛可以搜到一大堆此类问题，那么解决办法呢？我找到的一种解决方式如下：
```html
<textarea maxlength="-1" focus selection-start="{{ cursorIndex }}" selection-end="{{ cursorIndex }}" @input="updateTextarea" value="{{val}}" />
```
`js` 部分如下：
```js
updateTextarea(e)  {
  const { value, cursor } = e.detail;
  this.setData({
     val: value,
     cursorIndex: cursor
   });
}
```
{% alert success %}
主要原理是重置了光标的当前位置，不至于出现光标混乱，而导致内容被错误修改。
{% endalert %}

#### 七、页面多次跳转，直接返回首页
微信提供了多种页面跳转 `API`，应该能够满足页面跳转的业务逻辑，主要如下：
1.`wx.navigateTo(OBJECT)`，保留当前页面，跳转到应用内的某个页面，使用 `wx.navigateBack` 可以返回到原页面。
```js
wx.navigateTo({
  url: 'test?name=jartto'
})
```
2.`wx.redirectTo(OBJECT)`，关闭当前页面，跳转到应用内的某个页面。
```js
wx.redirectTo({
  url: 'test?name=jartto'
})
```
3.`wx.reLaunch(OBJECT)`，关闭所有页面，打开到应用内的某个页面。
```js
wx.reLaunch({
  url: 'test?name=jartto'
})
```
4.`wx.switchTab(OBJECT)`，跳转到 `tabBar` 页面，并关闭其他所有非 `tabBar` 页面。
```js
{
  "tabBar": {
    "list": [{
      "pagePath": "index",
      "text": "首页"
    },{
      "pagePath": "other",
      "text": "其他"
    }]
  }
}

wx.switchTab({
  url: '/index'
})
```
5.`wx.navigateBack(OBJECT)`，关闭当前页面，返回上一页面或多级页面。可通过 `getCurrentPages()` 获取当前的页面栈，决定需要返回几层。
```js
// 此处是A页面
wx.navigateTo({
  url: 'B?id=1'
})
// 此处是B页面
wx.navigateTo({
  url: 'C?id=1'
})
// 在C页面内 navigateBack，将返回A页面
wx.navigateBack({
  delta: 2
})
```
上面是文档的说名，其实意思很清楚了。这里我假设一种场景：用户从 `A－B－C` 页面，当从 `C` 页面返回的时候，其实就不需要再经过 `B` 页面，那么就需要用到 `wx.redirectTo()`。如下，在 `B` 页面执行：
```js
wx.redirectTo({
  url: 'C?name=jartto'
})
```

#### 八、表单域控制
在小程序中如果要取得 `input` 的值需要如下操作：
```html
<view class="section">
  <input @input="bindKeyInput" placeholder="" />
</view>
```
js 代码如下：
```js
bindKeyInput: function(e) {
  this.setData({
    inputValue: e.detail.value
  })
}
```

其他表单类似，可以查看：[文档说明](https://mp.weixin.qq.com/debug/wxadoc/dev/component/input.html)

{% alert warning %}
切记：很多组件都被小程序重写了，所以碰到问题一定要先查阅文档，确定无法实现的话，再自行扩展。
{% endalert %}

#### 九、不要轻易移除 dist 文件
这是一条友善的提醒，你肯定会有疑问：生成出来的文件还不能移除？答案是肯定的，具体为什么，请看[这里](https://github.com/Tencent/wepy/issues/758)。当然，有的人通过重启后问题解决了，但是有些人出现了更多的 `bug`。

可能很多童鞋都发现了，小程序使用 `wepy` 开发过程中，构建出来的 `dist` 目录会有冗余文件。也就意味着，我先添加一张图片，然后从项目中移除，`dist` 目录就会一直保留该文件。

{% alert danger %}
小程序文件限定大小为 2M
{% endalert %}

所以，当某天你觉得项目比较大的时候，记得去清理一些冗余文件。官方给出的建议如下：

开发者工具会监听 `dist` 目录的修改，如果删除 `dist` 后，再生成 `dist` 。不会监听到新的 `dist` 文件如果需要删除 `dist` 重建的话，请使用 `rm -rf dist/*` 来代替 `rm -rf dist`。

#### 十、为小程序添加转发
这条就比较简单了，但是也越容易被忽略。在 `Page` 中定义 `onShareAppMessage` 函数，设置该页面的转发信息。
- 只有定义了此事件处理函数，右上角菜单才会显示 “转发” 按钮；
- 用户点击转发按钮的时候会调用；
- 此事件需要 `return` 一个 `Object`，用于自定义转发内容；

代码参考：
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

详细文档，请参考[这里](https://mp.weixin.qq.com/debug/wxadoc/dev/api/share.html#onshareappmessageoptions)

#### 十一、合理使用 scroll-view
{% alert info %}
页面滚动区域的限制也比较大，所以一定要在需要限制的容器外套上 `scroll-view`
{% endalert %}

主要问题在于官方的几种警告：
- 请勿在 `scroll-view` 中使用 `textarea、map、canvas、video` 组件
- `scroll-into-view` 的优先级高于 `scroll-top`
- 在滚动 `scroll-view` 时会阻止页面回弹，所以在 `scroll-view` 中滚动，是无法触发 `onPullDownRefresh`
- 若要使用下拉刷新，请使用页面的滚动，而不是 `scroll-view` ，这样也能通过点击顶部状态栏回到页面顶部

#### 十二、总结
从头开始一步步研究了小程序，也通过实际项目尝试了一把。虽然过程中比较痛苦，但是结局是欢乐的。文末打个广告吧，微信小程序搜索：「AI 识图」，欢迎体验，欢迎反馈。



