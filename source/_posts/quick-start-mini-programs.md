---
title: 快速上手小程序
date: 2018-01-25 22:53:33
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/logo/wxmini.png
thumbnailImagePosition: left
tags: 
- wx
- mini-programs
comments: false
metaAlignment: center
categories: 技术博文
---
心血来潮，突然想搞个小程序玩玩。微信提供了比较全面的支持，还可以上传托管服务器，又不收钱。对前端来说，这不是白送吗，快抓紧机会试试吧～
<!-- more -->
#### 一、注册账号
首先去[官网注册](https://mp.weixin.qq.com/wxopen/waregister?action=step1)一个小程序账号吧，我们要拿到 `AppId`。

{% alert warning %}
小程序注册的前提是，没有注册过任何的微公众平台，如未注册过订阅号、服务号之类的。邮箱要求也是“未被公众平台注册，未被开放平台注册，未被个人微信号绑定的邮箱。
{% endalert %}

#### 二、安装开发者工具
要开发微信小程序，开发者工具必不可少，快去[这里](https://mp.weixin.qq.com/debug/wxadoc/dev/devtools/download.html?t=2018125)下载吧！打开微信开发者工具，创建项目的时候会用到我们上面拿到的 `AppId`。

#### 三、简易教程
官网的[简易教程](https://mp.weixin.qq.com/debug/wxadoc/dev/)走一遍，主要包含：
- 起步：告诉你怎么申请账号，如何使用开发者工具，以及编译预览。
- 代码构成：`WXML`，`WXSS` 以及 `JSON` 配置。
- 小程序能力：告诉你页面如何启动，可以使用的组件，还有微信的一些 `API`。
- 发布前准备：预览，上传以及版本管理。
- 上线：提交审核，发布产品，以及运营数据的监控等。

{% alert info %}
教程很简单，看一遍文档，基本都会了。
{% endalert %}

#### 四、使用 WePY 框架
本来打算直接按照简易教程上手撸一把，结果意外发现了 WePY 这个框架，看完以后，我决定马上用起来。先来上一张[官网](https://tencent.github.io/wepy/)的介绍：
{% alert success %}
让小程序支持组件化开发的框架一个最受欢迎的小程序框架。
{% endalert %}
![wepy](http://7xvi3w.com1.z0.glb.clouddn.com/wx1.png)
看完上面的图，为什么使用 `WePY` 框架的理由，不言自明了吧！下面，先来准备初始化代码吧：
首先，我们先来安装：
```bash
npm install wepy-cli -g
```
然后，在开发目录中生成 `Demo` 开发项目：
```bash
wepy new myproject
```
切换至项目目录：
```bash
cd myproject
```
开启实时编译：
```
wepy build --watch
```

脚手架生成出来的项目结构如下所示：
```
├── dist                   微信开发者工具指定的目录（该目录由WePY的build指令自动编译生成，请不要直接修改该目录下的文件）
├── node_modules           
├── src                    代码编写的目录（该目录为使用WePY后的开发目录）
|   ├── components         WePY组件目录（组件不属于完整页面，仅供完整页面或其他组件引用）
|   |   ├── com_a.wpy      可复用的WePY组件a
|   |   └── com_b.wpy      可复用的WePY组件b
|   ├── pages              WePY页面目录（属于完整页面）
|   |   ├── index.wpy      index页面（经build后，会在dist目录下的pages目录生成index.js、index.json、index.wxml和index.wxss文件）
|   |   └── other.wpy      other页面（经build后，会在dist目录下的pages目录生成other.js、other.json、other.wxml和other.wxss文件）
|   └── app.wpy            小程序配置项（全局数据、样式、声明钩子等；经build后，会在dist目录下生成app.js、app.json和app.wxss文件）
└── package.json           项目的package配置
```

{% alert info %}
注：dist 目录为 WePY 通过 build 指令生成的目录，除额外增加的 npm 目录外，其目录结构与原生小程序的目录结构类似。
{% endalert %}

#### 五、WePY 代码组成
小程序入口 `app.wpy`：
```jsx
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

页面 `page.wpy`：
```jsx
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
页面文件 `page.wpy` 中所声明的页面实例继承自 `wepy.page` 类，该类的主要属性介绍如下：
- config：页面配置对象，对应于原生的 `page.json` 文件，类似于 `app.wpy` 中的 `config`
- components：页面组件列表对象，声明页面所引入的组件列表
- data：页面渲染数据对象，存放可用于页面模板绑定的渲染数据
- methods：`wxml` 事件处理函数对象，存放响应 `wxml` 中所捕获到的事件的函数，如 `bindtap`、`bindchange`
- events：`WePY` 组件事件处理函数对象，存放响应组件之间通过 `$broadcast`、`$emit`、`$invoke` 所传递的事件的函数
- 其它：小程序页面生命周期函数，如onLoad、onReady等，以及其它自定义的方法与属性

组件 `com.wpy`：
```html
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

#### 六、WePY 使用示例
通过前文的介绍可知，在 `WePY` 中，小程序被分为三个实例：小程序实例 `App`、页面实例 `Page`、组件实例`Component`。其中 `Page` 实例继承自 `Component`。各自的声明方式如下：
```js
import wepy from 'wepy';

// 声明一个App小程序实例
export default class MyAPP extends wepy.app {
}

// 声明一个Page页面实例
export default class IndexPage extends wepy.page {
}

// 声明一个Component组件实例
export default class MyComponent extends wepy.component {
}
```
`App` 小程序实例中主要包含小程序生命周期函数、`config` 配置对象、`globalData` 全局数据对象，以及其他自定义方法与属性。
```js
import wepy from 'wepy';

export default class MyAPP extends wepy.app {
    customData = {};

    customFunction ()　{ }

    onLaunch () {}

    onShow () {}

    config = {}  // 对应 app.json 文件

    globalData = {}
}
```
{% alert info %}
在 `Page` 页面实例中，可以通过 `this.$parent` 来访问 `App` 实例。
{% endalert %}

由于 `Page` 页面实际上继承自 `Component` 组件，即 `Page` 也是组件。除扩展了页面所特有的 `config` 配置以及特有的页面生命周期函数之外，其它属性和方法与 `Component` 一致，因此这里以 `Page`页面为例进行介绍。
```js
import wepy from 'wepy';

// export default class MyPage extends wepy.page {
export default class MyComponent extends wepy.component {
    customData = {}  // 自定义数据

    customFunction ()　{}  //自定义方法

    onLoad () {}  // 在Page和Component共用的生命周期函数

    onShow () {}  // 只在Page中存在的页面生命周期函数

    config = {};  // 只在Page实例中存在的配置数据，对应于原生的page.json文件

    data = {};  // 页面所需数据均需在这里声明，可用于模板数据绑定

    components = {};  // 声明页面中所引用的组件，或声明组件中所引用的子组件

    mixins = [];  // 声明页面所引用的Mixin实例

    computed = {};  // 声明计算属性（详见后文介绍）

    watch = {};  // 声明数据watcher（详见后文介绍）

    methods = {};  // 声明页面wxml中标签的事件处理函数。注意，此处只用于声明页面wxml中标签的bind、catch事件，自定义方法需以自定义方法的方式声明

    events = {};  // 声明组件之间的事件处理函数
}
```
注意，对于 `WePY` 中的 `methods` 属性，因为与 `Vue` 中的使用习惯不一致，非常容易造成误解，这里需要特别强调一下：`WePY` 中的 `methods` 属性只能声明页面 `wxml` 标签的 `bind` 、`catch` 事件，不能声明自定义方法，这与 `Vue` 中的用法是不一致的。示例如下：
```js
// 错误示例

import wepy from 'wepy';

export default class MyComponent extends wepy.component {
    methods = {
        bindtap () {
            let rst = this.commonFunc();
            // doSomething
        },

        bindinput () {
            let rst = this.commonFunc();
            // doSomething
        },

        //错误：普通自定义方法不能放在methods对象中
        customFunction () {
            return 'sth.';
        }
    };

}


// 正确示例

import wepy from 'wepy';

export default class MyComponent extends wepy.component {
    methods = {
        bindtap () {
            let rst = this.commonFunc();
            // doSomething
        },

        bindinput () {
            let rst = this.commonFunc();
            // doSomething
        },
    }

    //正确：普通自定义方法在methods对象外声明，与methods平级
    customFunction () {
        return 'sth.';
    }

}
```

#### 七、WePY 框架的重要事项
1. 使用微信开发者工具 --> 添加项目，项目目录请选择 `dist` 目录。
2. 微信开发者工具 --> 项目 --> 关闭 `ES6` 转 `ES5`。 重要：漏掉此项会运行报错。
3. 微信开发者工具 --> 项目 --> 关闭上传代码时样式自动补全。 重要：某些情况下漏掉此项也会运行报错。
4. 微信开发者工具 --> 项目 --> 关闭代码压缩上传。 重要：开启后，会导致真机 `computed`, `props.sync` 等等属性失效。（注：压缩功能可使用 `WePY` 提供的 `build` 指令代替，详见后文相关介绍以及 `Demo` 项目根目录中的 `wepy.config.js` 和 `package.json` 文件。）
5. 本地项目根目录运行 `wepy build --watch`，开启实时编译。（注：如果同时在微信开发者工具 --> 设置 --> 编辑器中勾选了文件保存时自动编译小程序，将可以实时预览，非常方便。）

{% alert success %}
友情提示，如果你采用了 `WePY` 框架，请一定按照提示做配置，否则错误可能很麻烦。
{% endalert %}

#### 八、配置编辑器高亮
因为我使用了 `VS Code`，可能的配置如下：
1. 在 `Code` 里先安装 `Vue` 的语法高亮插件 `Vetur`。
2. 打开任意 `.wpy` 文件。
3. 点击右下角的选择语言模式，默认为纯文本。
4. 在弹出的窗口中选择 `.wpy` 的配置文件关联...。
5. 在选择要与 `.wpy` 关联的语言模式中选择 `Vue`。

更多的编辑器配置，请移步：[代码高亮](https://tencent.github.io/wepy/document.html#/)

#### 九、为小程序引入 UI 样式库
到这里，整体框架算是有了，但是我们还需要美颜啊。嗯，是时候引入 UI 样式库了！这里我们还是使用微信本家的产品 [WeUI](https://github.com/Tencent/weui-wxss)：
{% alert success %}
WeUI 是一套同微信原生视觉体验一致的基础样式库，由微信官方设计团队为微信内网页和微信小程序量身设计，令用户的使用感知更加统一。包含 button、cell、dialog、progress、toast、article、actionsheet、icon 等各式元素。
{% endalert %}
好了，下面说说怎么使用：
- 首先，需要下载[样式库](https://github.com/Tencent/weui-wxss/tree/master/dist/style)；
- 其次，将完整的样式包文件放入你的项目目录中；
- 最后，如果使用了 `wepy` 的话，`weui` 需要 `less` 文件；

{% alert warning %}
使用 wepy ，样式文件一定是 less 后缀，否则可能会有 unexpected token "@import" 这样的异常。
{% endalert %}

完成上面步骤后，就可以开心的使用微信样式库了：
```html
<view class="page">
  <progress percent="20" show-info />
</view>
```

#### 十、页面跳转如何操作
使用惯了其他的框架，很可能你也像我一样，开始去找小程序的路由文件了。其实大可不必如此，这些事情小程序已经处理好了，你唯一需要知道的就是如何去用。丰富的[ API ](https://mp.weixin.qq.com/debug/wxadoc/dev/api/ui-navigate.html#wxnavigatetoobject)你都可以试试：
1.保留当前页面，跳转到应用内的某个页面，使用 `wx.navigateBack` 可以返回到原页面。
```js
wx.navigateTo({
  url: 'test?user=jartto'
})
```
2.关闭当前页面，跳转到应用内的某个页面。
```js
wx.redirectTo({
  url: 'test?user=jartto'
})
```
3.关闭所有页面，打开到应用内的某个页面。
```js
wx.reLaunch({
  url: 'test?user=jartto'
})
```
4.跳转到 `tabBar` 页面，并关闭其他所有非 `tabBar` 页面
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
5.关闭当前页面，返回上一页面或多级页面。可通过 getCurrentPages()) 获取当前的页面栈，决定需要返回几层。
```js
// 注意：调用 navigateTo 跳转时，调用该方法的页面会被加入堆栈，而 redirectTo 方法则不会。见下方示例代码

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

#### 十一、服务器域名配置
每个微信小程序需要事先设置一个通讯域名，小程序可以跟指定的域名与进行网络通信。包括普通 `HTTPS` 请求（`request`）、上传文件（`uploadFile`）、下载文件（`downloadFile`) 和 `WebSocket` 通信（`connectSocket`），服务器域名请在 小程序后台-设置-开发设置-服务器域名 中进行配置，配置时需要注意：
{% alert warning %}
服务器域名请在小程序后台-设置-开发设置-服务器域名 中进行配置，配置时需要注意：
{% endalert %}

- 域名只支持 https (request、uploadFile、downloadFile) 和 wss (connectSocket) 协议；
- 域名不能使用 IP 地址或 localhost，且不能带端口号；
- 域名必须经过 ICP 备案；
- 出于安全考虑，api.weixin.qq.com 不能被配置为服务器域名，相关API也不能在小程序内调用。开发者应将 appsecret 保存到后台服务器中，通过服务器使用 appsecret 获取 accesstoken，并调用相关 API。
- 对于每个接口，分别可以配置最多 20 个域名；

#### 十二、获取用户输入
能够获取用户输入的组件，需要使用组件的属性 `bindblur` 将用户的输入内容同步到 `AppService`。
```html
<input id="myInput" bindblur="bindBlur" />
```
`js` 部分：
```jsx
let inputContent = {}

Page({
  data: {
    inputContent: {}
  },
  bindBlur: function(e) {
    inputContent[e.currentTarget.id] = e.detail.value;
  }
})
```

#### 十三、总结
文章主要按照自己接触小程序的学习过程来展现，每个阶段我需要学习什么，以及我可能对哪些点比较关注。严格意义上来说，这并不是一篇入门教程，而更像是一个一步步开始小程序开发的教程。
