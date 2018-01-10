---
title: 初探 Electron - 升华篇
date: 2018-01-04 21:33:14
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/electron3.png
thumbnailImagePosition: left
tags: 
- electron
comments: fasle
metaAlignment: center
categories: 技术博文
---
由于[初探 Electron - 理论篇](http://jartto.wang/2018/01/03/first-exploration-electron/)中基础概念太多，导致篇幅过长，影响阅读效果。所以我决定将 Electron 余下部分在本文继续探讨。
<!-- more -->
#### 一、如何引入 jQuery？
在 `Electron` 中，如果引入 `jQuery` 的话，可能会有这样的错误：`jQuery is not defined`，我们需要这样处理：
```js
<script>window.$ = window.jQuery = require('jquery');</script>
```
如果报出 `Cannot find module 'jquery'` 这样的错，请检查你的 jQuery 是否安装：
```js
npm install jquery --save
```

可以参考如下两篇文章：
- [Electron: jQuery is not defined](https://stackoverflow.com/questions/32621988/electron-jquery-is-not-defined)
- [jQuery isn't set globally because "module" is defined](https://github.com/electron/electron/issues/254)

#### 二、使用 scss 或 less
- less：[electron-less](https://www.npmjs.com/package/electron-less)
- node-sass

#### 三、引入构建工具
- webpack：[anuglar and webpack](https://auth0.com/blog/create-a-desktop-app-with-angular-2-and-electron/)
- gulp

#### 四、使用 React 进行开发
如果你想使用 `React` 来开发，请参考[](http://blog.csdn.net/arnozhang12/article/details/51735815)

#### 五、使用 Angularjs 进行开发
同理，一篇用 `Angularjs` 开发 `Electron` 的文章，可以[参考一下](https://auth0.com/blog/create-a-desktop-app-with-angular-2-and-electron/)。

#### 六、如何发送请求？
- 使用 [request](https://github.com/request/request)
- 使用 [SuperAgent](https://github.com/visionmedia/superagent)
- 使用 [bluebird](http://bluebirdjs.com/docs/getting-started.html)

```js
var Promise = require( 'bluebird' );
var request = require( 'superagent-bluebird-promise' );
request
.get( 'http://xxxx.com' )
.then( function ( res ) {
// do something when resolved
}, function ( err ) {
 // do something when rejected
});
```

#### 七、使用 ES6
安装好 `babel` 后，还需要进行配置。我们通过 `babel` 官网了解到，需要在项目目录下放置一个 `.babelrc` 文件，让 `babel` 知道转换哪些代码。我们的 `.babelrc` 文件内容如下：
```json
{
  "presets": [
    "es2015",
    "react"
  ],

  "plugins": [
    "transform-object-rest-spread"
  ]
}
```
通过 `presets` 和 `plugins` 两个子项，我们告知 `babel` 转换 `ES6` 和 `React JSX` 风格的代码，另外还需转换 `ES6` 中的 `spread` 语法。

更多详情，请[参考](http://blog.csdn.net/arnozhang12/article/details/51735815)。

#### 八、监听代码变化：watchify
另外，我们需要在 `package.json` 文件中配置 `watchify`，让其可以自动检测本地代码变化，并且自动转换代码。`package.json` 如下：
```json
// package.json

{
  "name": "demoapps",
  "version": "1.0.0",
  "description": "",
  "author": "arnozhang",
  "main": "index.js",
  "scripts": {
    "start": "electron .",
    "watch": "watchify app/appEntry.js -t babelify -o public/js/bundle.js --debug --verbose",
    "package": "electron-packager ./ DemoApps --overwrite --app-version=1.0.0 --platform=win32 --arch=all --out=../DemoApps --version=1.2.1 --icon=./public/img/app-icon.icns"
  },
  "devDependencies": {
    "babel": "^6.5.2",
    "babel-plugin-transform-es2015-spread": "^6.8.0",
    "babel-plugin-transform-object-rest-spread": "^6.8.0",
    "babel-preset-es2015": "^6.9.0",
    "babel-preset-react": "^6.5.0",
    "babelify": "^7.3.0",
    "browserify": "^13.0.1",
    "electron-packager": "^7.0.3",
    "electron-prebuilt": "^1.2.1",
    "electron-reload": "^1.0.0",
    "watchify": "^3.7.0"
  },
  "dependencies": {
    "material-ui": "^0.15.0",
    "react": "^15.1.0",
    "react-color": "2.1.0",
    "react-dom": "^15.1.0",
    "react-tap-event-plugin": "^1.0.0"
  }
}
```
通过 `package.json` 文件，我们在 `scripts` 下面配置了三个命令：`start`、`watch`、`package`，分别用于启动 `App`、检测并转换代码、打包 `App`。
```json
start： electron . 
watch：watchify app/appEntry.js -t babelify -o public/js/bundle.js –debug –verbose 
package：electron-packager ./ DemoApps –overwrite –app-version=1.0.0 –platform=win32 –arch=all –out=../DemoApps –version=1.2.1 –icon=./public/img/app-icon.icns
```
然后我们在命令行下通过 `npm run xxx` ，可以运行上面定义好的命令。我们看到，通过 `babelify` 将代码转换输出到 `public/js/bundle.js` 目录下，所以我们发布时只需要发布这一个转换好的 `js` 文件即可。

更多详情，请[参考](http://blog.csdn.net/arnozhang12/article/details/51735815)。

#### 九、如何减小 Electron 打包出来的应用的体积
`Electron` 应用体积动辄一百多兆的体积从他发布的第一天起到现在一直被人诟病，而其体积之所以如此之大的原因在于 `Electron` 为其跨平台的支持，在内部打包了整个 `V8` 引擎，相当于一个功能完整的浏览器。[优化方式](https://changkun.us/archives/2017/03/217/)如下：
1. 使用 yarn clean
2. 将应用程序打包后再分发
3. 定制应用的更新功能

#### 十、去除视觉闪烁
在加载页面时，渲染进程第一次完成绘制时，会发出 `ready-to-show` 事件 。 在此事件后显示窗口将没有视觉闪烁：
```js
const {BrowserWindow} = require('electron') 
let win = new BrowserWindow({show: false}) 
win.once('ready-to-show', () => { win.show() })
```

#### 十一、Shell 模块异常之 Cannot find module ‘shell’
如果遇到这样的错，请按照如下操作：
```bash
const electron = require('electron');
const shell = electron.shell;
```
{% alert success %}
请注意：`shell` 不是单独的 `node` 模块，而是 `electron` 中封装的一个 `shell` 对象。
{% endalert %}

#### 十二、vs-code debugger 模式报错：Cannot read property 'on' of undefined
解决方案如下：
```
I just upgraded VS Code and debugging is working fine.
```
如果不行的话，再试试：
```js
const electron = require('electron');
const app = require('app');
const BrowserWindow = require('browser-window')
```

更多详情，请参考如下两篇文章：
- [Cannot debug electron-quick-start in VSCode](https://github.com/electron/electron-quick-start/issues/170)
- [TypeError: Cannot read property 'on' of undefined](https://github.com/electron/electron-quick-start/issues/30)

#### 十三、Electron 会在什么时候升级到最新版本的 Chrome？
通常来说，在稳定版的 `Chrome` 发布后两周内，官方会更新 `Electron` 内的 `Chrome` 版本。

官方只会使用 `stable` 版本的 `Chrome`。但如果在 `beta` 或 `dev` 版本中有一个重要的更新，官方会把补丁应用到现版本的 `Chrome` 上。

#### 十四、Electron 会在什么时候升级到最新版本的 Node.js？
官方通常会在最新版的 `Node.js` 发布后一个月左右将 `Electron` 更新到这个版本的 `Node.js`。官方通过这种方式来避免新版本的 `Node.js` 带来的 `bug`（这种 `bug` 太常见了）。

`Node.js` 的新特性通常是由新版本的 `V8` 带来的。由于 `Electron` 使用的是 `Chrome` 浏览器中附带的 `V8` 引擎，所以 `Electron` 内往往已经有了部分新版本 `Node.js` 才有的崭新特性。

#### 十五、为什么应用的窗口、托盘在一段时间后不见了？
{% alert info %}
这通常是因为用来存放窗口、托盘的变量被垃圾收集了。
{% endalert %}

大家可以参考以下两篇文章来了解为什么会遇到这个问题。
- [内存管理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management)
- [变量作用域](https://msdn.microsoft.com/library/bzt2dkta.aspx)

如果你只是要一个快速的修复方案，你可以用下面的方式改变变量的作用域，防止这个变量被垃圾收集。从
```js
app.on('ready', function() {
  var tray = new Tray('/path/to/icon.png');
})
```
改为
```js
var tray = null;
app.on('ready', function() {
  tray = new Tray('/path/to/icon.png');
})
```

#### 十六、在 Electron 中，我为什么不能用 jQuery、RequireJS、Meteor、AngularJS？
因为 `Electron` 在运行环境中引入了 `Node.js`，所以在 `DOM` 中有一些额外的变量，比如 `module`、`exports` 和 `require`。这导致了许多库不能正常运行，因为它们也需要将同名的变量加入运行环境中。

我们可以通过禁用 `Node.js` 来解决这个问题，用如下的方式：
```js
// 在主进程中
var mainWindow = new BrowserWindow({
  webPreferences: {
    nodeIntegration: false
  }
});
```

假如你依然需要使用 `Node.js` 和 `Electron` 提供的 `API`，你需要在引入那些库之前将这些变量重命名，比如：
```html
<head>
<script>
// 重命名 Electron 提供的 require
window.nodeRequire = require;
delete window.require;
delete window.exports;
delete window.module;
</script>
<script type="text/javascript" src="jquery.js"></script>
</head>
```

#### 十七、为什么 require('electron').xxx 的结果是 undefined？
在使用 `Electron` 的提供的模块时，你可能会遇到和以下类似的错误：
```js
> require('electron').webFrame.setZoomFactor(1.0);
Uncaught TypeError: Cannot read property 'setZoomLevel' of undefined
```
这是因为你在项目中或者在全局中安装了 `npm` 上获取的 `electron` 模块，它把 `Electron` 的内置模块覆写了。

你可以通过以下方式输出 `electron` 模块的路径来确认你是否使用了正确的模块。
```js
console.log(require.resolve('electron'));
```
确认以下它是不是像下面这样的：
```bash
"/path/to/Electron.app/Contents/Resources/atom.asar/renderer/api/lib/exports/electron.js"
```
假如输出的路径类似于 `node_modules/electron/index.js`，那么你需要移除或者重命名 `npm` 上的 `electron` 模块。
```bash
npm uninstall electron
npm uninstall -g electron
```
如果你依然遇到了这个问题，你可能需要检查一下拼写或者是否在错误的进程中调用了这个模块。比如，`require('electron').app` 只能在主进程中使用, 然而 `require('electron').webFrame` 只能在渲染进程中使用。

更多问题，请移步[ Electron 常见问题](https://www.w3cschool.cn/electronmanual/electronmanual-electron-faq.html)

#### 十八、Electron could not be found. No hard resets for you!
如下方案可以[解决](https://github.com/yan-foto/electron-reload/issues/16)：
```js
require('electron-reload')(__dirname, {
  electron: require('${__dirname}/../../node_modules/electron'),
});
```

#### 十九、使用 electron-prebuilt-compile 替换 electron
{% alert info %}
Install electron-prebuilt-compile instead of the electron
{% endalert %}
```bash
npm install electron-prebuilt-compile --save-dev
```
会报出这样的异常：`Path must be a string. Received undefined at assertPath`

#### 二十、打包异常
当我们配置好安装 `electron-packager` 并配置好 `package.json` 启动 `npm run package` 的时候，可能会碰到这样的错误：
{% alert danger %}
Using electron-prebuilt-compile with Electron Packager requires specifying an exact Electron version
{% endalert %}
还有这样的错误：
{% alert danger %}
Unable to determine Electron version. Please specify an Electron version
{% endalert %}
这时候一定要安装 `electron`
```bash 
npm install --save-dev electron
```
出现问题的原因可能是 `electron-prebuilt-compile` 与 `electron` 冲突造成的，我这里就是因为冲突，所以我卸载了 `electron` 包。

#### 二十一、写在最后
陆续写了两篇 `Electron` 文章后，我们需要更实际的去完成一个项目，这样才能融汇贯通。嗯，下一篇文章即将出炉：[初探 Electron - 实践篇](#)