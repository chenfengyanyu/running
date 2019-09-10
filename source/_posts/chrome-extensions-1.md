---
title: 打造你的专属 Chrome 插件（一）
date: 2018-11-10 22:18:21
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/chrome/logo.png
thumbnailImagePosition: left
tags: 
- chrome
- extensions
comments: false
metaAlignment: center
categories: 技术博文 
---
不得不说 Chrome 给前端带来了无限的可能，Chrome 插件更是如虎添翼，让开发者有了更大的操作权限。这样，我们就可以做很多有趣的事情了。
<!-- more -->
#### 一、写在前面
对于很多开发者来说，通篇的概念可能会让人感到厌烦。所以，咱们先绕开一些预备知识，进行简单的实践。采用「自顶向下」的学习方式，我们先来看一个具型的东西，稍后再逐步深入概念和内在原理。

{% alert success %}
本文将会一步步去搭建一个 Chrome 插件开发环境，采用 React 来实现基本功能。
{% endalert %}

#### 二、简单了解
`Chrome` 插件是一系列文件的集合，这些文件包括 `HTML`文件、`CSS`样式文件、`JavaScript`脚本文件、图片等静态文件以及 `manifest.json`。

说白了就是：`Chrome` 扩展更像是一个运行于本地的网站，只是它可以利用 `Chrome` 平台提供的丰富的接口，获得更加全面的信息，进行更加复杂的操作。

#### 三、创建项目
{% alert info %}
因为选择了采用 React 来开发，所以我们可以使用脚手架来生成最基本的项目结构。
{% endalert %}

首先，安装 `create-react-app`
```
sudo npm install -g create-react-app
```
然后，初始化项目：
```
create-react-app chrome-extension
```
进入 `public` 目录，修改文件：`mainfest.json`：
```json
{
  "manifest_version": 2,
  "name": "Jartto's Demo",
  "description": "My first chrome extension.",
  "version": "1.0.0",
  "icons": {
    "16": "image/icon-16.png",
    "128": "image/icon-128.png"
  },
  "browser_action": {
    "default_icon": {
      "19": "image/icon-38.png",
      "38": "image/icon-19.png"
    },
    "default_title": "Jartto's Demo",
    "default_popup": "index.html"
  },
  "permissions": [
    "alarms", "tabs", "notifications"
  ],
  "background": {
    "persistent": false,
    "scripts": [
      "background.js"
    ]
  },
  "content_security_policy": "script-src 'self' 'unsafe-eval'; object-src 'self'"
}
```
找到几个 `icon`，放入 `image` 目录下。
```
icon-128.png
icon-16.png
icon-19.png
icon-38.png
```

图片就绪，此外我们还需要创建一个脚本 `background.js`：
```js
(function(){
  // 这里调用 chrome api tabs 作为尝试
  chrome.tabs.onUpdated.addListener(function(tabId,changeInfo,tab){
    // 用户切换 tab，会弹出警告框
    alert(tab.url);
  })
})();
```

最终 `public` 下目录结构大概如此：
```
public
├── background.js
├── favicon.ico
├── image
│   ├── icon-128.png
│   ├── icon-16.png
│   ├── icon-19.png
│   └── icon-38.png
├── index.html
└── manifest.json
```

{% alert info %}
稍后我会对参数做[详细说明](#)，所以这里我们只实现插件的最小闭环。
{% endalert %}


#### 四、构建
按照上面的步骤，我们开发了一个极其简单的功能，打个包先：
```
yarn build
```
{% alert danger %}
注意观察 `dist` 目录下的文件变化。
{% endalert %}

#### 五、如何运行
`Chrome` 浏览器打开：`chrome://extensions`，选择「加载已解压的扩展程序」。看到如下图：
![chrome 插件安装](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/chrome/extnsions.png)

{% alert warning %}
需要注意：将插件目录指向我们上文构建出的 `dist` 目录。
{% endalert %}

安装完之后，页面和页头工具栏都会出现我们新开发的插件，如下图：
![chrome 插件演示](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/chrome/show.png)

#### 六、异常
奇怪的是，插件导入后，并没有运行，我们看到了如下的警告：
```
Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self' blob: filesystem: chrome-extension-resource:". Either the 'unsafe-inline' keyword, a hash ('sha256-GgRxrVOKNdB4LrRsVPDSbzvfdV4UqglmviH9GoBJ5jk='), or a nonce ('nonce-...') is required to enable inline execution.
```
大概意思是违反了 `CSP` 原则，这里大概提一下，详细请看另一篇文章：[CSP 概要](http://jartto.wang/2018/11/12/outline-of-CSP/) 

`CSP（Content Security Policy）`，内容安全策略，`CSP` 通常是在 `Header` 或者 `HTML` 的 `Meta` 标签中定义的，它声明了一系列可以被当前网页合法引用的资源，如果不在 `CSP` 声明的合法范围内，浏览器会拒绝引用这些资源，`CSP` 的主要目的是防止跨站脚本攻击（`XSS`）。

{% alert info %}
说白了其实 CSP 是一种安全策略，Chrome 插件不允许如此操作。
{% endalert %}

默认情况下，内容安全策略将不会加载内联脚本，只能加载本地脚本。所以我们可以放宽默认政策：
1.内联脚本
看看官方指南，通过在策略中指定源代码的 `base64` 编码散列，可以将内联脚本列入白名单。但最好的方法是将这个逻辑提取到一个单独的脚本，而不是使用内联脚本。

2.远程脚本
我们可以设置白名单脚本资源
```json
manifest.json "content_security_policy":"script-src 'self' https://apis.google.com; object-src 'self'"
```
当然，下载远程 `jartto.js` 并将其包含为本地脚本，这才是最好的方式。

{% alert danger %}
请注意，根据内联脚本的说明，unsafe-inline 不再有效。
{% endalert %}

到这里其实已经明确了，使用 `Create-React-App` 创建的项目，在我们构建的时候，默认将 `js` 打包为内联，所以违反了 `CSP` 原则，我们修改一下打包命令：
```
"scripts": {
  "start": "react-scripts start",
  "build": "INLINE_RUNTIME_CHUNK=false react-scripts build",
  "test": "react-scripts test",
  "eject": "react-scripts eject"
},
```
{% alert warning %}
请注意：我们增加了 INLINE_RUNTIME_CHUNK=false 用来控制打包的 js 文件。
{% endalert %}

好了，现在再次安装 `Chrome` 插件就不会有任何问题了。细节问题，可以查看我的 [Demo](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/chrome-extension)。

#### 七、补充说明
上面我们完成了一个简单的 `Chrome` 插件，细心的童鞋可能发现了，文件中创建了 `background.js`，这是什么意思呢？这里大概提一下：

1.`background` 层，`chrome` 插件底层，控制整个 `chrome` 插件的大脑，具有最高权限；

2.`content` 层，`content-script` 脚本工作平台，本质上是 `chrome` 插件脚本注入层，权限最低只能影响页面`DOM` ，不能跨域操作，但是可以通过 `chrome.runtime.sendMessage` 与 `background` 层交互；

3.`popup/option` 层，`UI` 交互层，权限中等，可以直接使用 `background` 的实例对象，也可以通过消息与 `content` 层交互。

{% alert info %}
这是开发 Chrome 插件中最重要的三个层概念，[后面](#)我们还会着重去讲解。
{% endalert %}

#### 八、关于调试
插件的开发过程，我们需要不断调试。因为插件的独特性，所以不同的层，调试方式是不同的。
- `popup.html` 调试： 右键点击 `popup` 图标，选择「审查弹出内容」；
- `content_script` 调试：`devtool -> sources -> Content scripts`；
- `background` 调试： `chrome://extensions/` 激活开发者模式，点击对应插件「检查视图」后的「背景页」；

#### 九、验证，打包
1.验证
打开网址：`chrome://extensions/`，选择开发者模式，选择加载已解压的扩展程序...，选择我们编写的根目录即可，如果出现类似文章开头展示的最终效果，并能实现想要实现的功能，表示验证成功；如果失败，请修改代码，重新执行此验证流程。

2.打包
打开网址：`chrome://extensions/`，选择开发者模式，选择打包扩展程序...，选择根目录，下面的 `key` 为空即可，最后选择打包扩展程序即可；如果打包失败，请根据错误提示信息修正，如果打包成功，会在与根目录同级的目录中生成 `CRX` 插件文件和 `PEM 秘钥` 文件。

#### 十、发布
发布过程就稍微麻烦一些，我也没有去尝试。好在不少开发者都摸索了整个过程，我们可以参考：[如何发布一款Chrome App](https://segmentfault.com/a/1190000000354014)。大致思路如下：

1.准备项目素材
- 4 种大小的图标（16px, 32px, 48px, 128px）
- 打包成 `zip`（不是 `crx`），访问开发者中心既可上传应用
- 一套 1280x800 大小的应用运行屏幕截图
- 一套 3 张（大中小）的宣传图
- 一段说明文字

2.开发者账号
如果你之前没有发布过，需要注册开发者账户，和 `Android`， `iOS` 开发者一样的是，需要付费。花费 `5$` ，需要 `Visa` 信用卡。

如果觉得自己的插件还不错，不妨贡献出来，为 `Chrome` 插件库做一些贡献吧。

#### 十一、总结
文章主要从项目构建，插件运行，异常处理，调试，打包发布等方面出发，完整的梳理了开发 `Chrome` 插件的过程。一个完整的示例，可以很快帮你入门。在知道了整个轮廓之后，我们需要进一步的摸索，所以[后续](#)文章会增加一些深度。
