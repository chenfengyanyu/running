---
title: 初探 Electron - 理论篇
date: 2018-01-03 22:17:24
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/electron0.png
thumbnailImagePosition: left
tags: 
- electron
comments: false
metaAlignment: center
categories: 技术博文
---
提起构建桌面应用，最先想到的肯定是 NW.js ，但却一直不温不火。直到 Electron 的出现，将这一切推向了巅峰，前端的触手伸向了更为广阔的系统应用。
<!-- more -->

{% alert danger %}
文章首发于 Jartto's blog ，转载文章请务必以超链接形式标明文章出处。
{% endalert %}

#### 一、Electron 是什么？
`Electron` 是由 `Github` 开发，用 `HTML`，`CSS` 和 `JavaScript` 来构建跨平台桌面应用程序的一个开源库。 `Electron` 通过将 `Chromium` 和 `Node.js` 合并到同一个运行时环境中，并将其打包为 `Mac`，`Windows` 和 `Linux` 系统下的应用来实现这一目的。
![elctron](http://7xvi3w.com1.z0.glb.clouddn.com/electron2.png-blog)

{% alert success %}
`Chromium` 是 `Google` 为发展 `Chrome` 浏览器而启动的开源项目，`Chromium` 相当于 `Chrome` 的工程版或称实验版（尽管 `Chrome` 自身也有 `β` 版阶段），新功能会率先在 `Chromium` 上实现，待验证后才会应用在`Chrome` 上，故 `Chrome` 的功能会相对落后但较稳定。
{% endalert %}

#### 二、快速启动
{% alert info %}
先照着官网的教程快速启动一个项目，这是最简单粗暴的入门步骤。
{% endalert %}
```bash
# 克隆示例项目的仓库
$ git clone https://github.com/electron/electron-quick-start
# 进入这个仓库
$ cd electron-quick-start
# 安装依赖并运行
$ npm install && npm start
```

#### 三、简单示例
项目启动后，你会看到一个 `Hello World!` 应用窗口，很简单，就不细说了。我更感兴趣的是 ` Electron API Demos app` ，大家可以去[官网下载](https://electronjs.org)：

![Electron API Demos app](http://7xvi3w.com1.z0.glb.clouddn.com/electron4.png-blog)

在 ` Electron API Demos app` 中，我们可以查看官方 API 和 一些 Demo 演示，强烈建议去实际感受一下。只有你知道了它的强大之处，你才会兴奋不已，点燃学习的激情。

#### 四、目录结构
好了，言归正传，我们用官方起手项目来做一个简单的说明。先来看看基本目录结构：
```bash
.
├── LICENSE.md
├── README.md
├── index.html
├── main.js
├── node_modules
│   ├── @types
│   ├── ajv
│   ├── ansi-regex
│   ├── ...
│   ├── wrappy
│   ├── xtend
│   └── yauzl
├── package.json
└── renderer.js
```
这里我们主要关注如下这三个文件：
1.`main.js`，也就是整个应用的入口，主要用来创建桌面应用窗口，并监听主进程的事件。
```js
const { app, BrowserWindow } = require('electron')
const path = require('path')
const url = require('url')
let mainWindow

function createWindow () {
  mainWindow = new BrowserWindow({width: 800, height: 600})
  // 启动文件入口，如 index.html
  mainWindow.loadURL(url.format({
    pathname: path.join(__dirname, 'index.html'),
    protocol: 'file:',
    slashes: true
  }))
  // 开启 Chromium DevTools
  mainWindow.webContents.openDevTools()
  // 监听窗口关闭事件
  mainWindow.on('closed', function () {
    mainWindow = null
  })
}
// 加载就绪
app.on('ready', createWindow)

// 监听所有窗口关闭的事件
app.on('window-all-closed', function () {
  if (process.platform !== 'darwin') {
    app.quit()
  }
})
// 激活窗口
app.on('activate', function () {
  if (mainWindow === null) {
    createWindow()
  }
})
```
2.`index.html` ，属于渲染进程，单纯的页面而已。
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
  </head>
  <body>
    <h1>Hello World!</h1>
    <!-- 在渲染进程中可以使用所有的 Node.js APIs -->
    We are using Node.js <script>document.write(process.versions.node)</script>,
    Chromium <script>document.write(process.versions.chrome)</script>,
    and Electron <script>document.write(process.versions.electron)</script>.

    <script>
      // 当然，我们也可以引入其他 js 文件
      require('./renderer.js')
    </script>
  </body>
</html>
```
3.`renderer.js` ，属于渲染进程，也就是我们页面的业务逻辑所要加载的 `JS`，简单的例子可能如下。
```js
let myNotification = new Notification('Jartto', {
  body: 'Hello Everybody!'
})

myNotification.onclick = () => {
  console.log('Click!')
}
```

{% alert warning %}
主进程和渲染进程下文会做具体说明，这里只需理解大致的代码结构即可。
{% endalert %}

#### 五、整装代发
在开始 `Coding` 前，我们需要认识一下桌面应用的基本构成，以及基础模块。这里以 `OS X` 示例：

![base modules](http://7xvi3w.com1.z0.glb.clouddn.com/ps2.png-blog)

上图列出了一个桌面应用应该涵盖的基本特征，当然，还有更多无法视觉感知，但却依然重要的底层模块。下文将一一罗列，并尽量用示例说明。

{% alert success %}
[代码示例](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)都放在 `Github` 上，欢迎 `Star`。
{% endalert %}

#### 六、主进程与渲染进程
{% alert warning %}
学习 Electron 最重要的就是要搞懂主进程和渲染进程，否则某个进程的特有方法很可能会调用失败。
{% endalert %}

`Electron` 中，由 `package.json` 中的 `main.js` 运行出来的进程为主进程(Main Process)。主进程用于创建 `GUI` 界面以便 `web` 页面的展示。`Electron` 由 `Chromium` 负责页面的显示，所以当创建一个页面时，就会对应的创建渲染进程(Renderer Process)。

![Electron Process](http://7xvi3w.com1.z0.glb.clouddn.com/electron6.png)

主进程通过创建 `BrowserWindow` 对象来创建 `web` 显示页面，`BrowserWindow` 运行在他自己的渲染进程中。当 `BrowserWindow` 被销毁时，对应的渲染进程也会终止。

#### 七、进程间通信
既然有两个进程，那么肯定会涉及到进程间相互通讯，这里介绍两个概念：
1.`Electron ipcMain` 模块
`ipcMain` 模块是类 `EventEmitter` 的实例。当在主进程中使用它的时候，它控制着由渲染进程(web page)发送过来的异步或同步消息。从渲染进程发送过来的消息将触发事件。

2.`Electron ipcRenderer` 模块
`pcRenderer` 模块是一个 `EventEmitter` 类的实例。它提供了有限的方法，你可以从渲染进程向主进程发送同步或异步消息。也可以收到主进程的相应。

在主进程中，也就是 `main.js` 中，添加如下代码：
```js
// 主进程
const ipcMain = require('electron').ipcMain;
ipcMain.on('asynchronous-message', function(event, arg) {
  console.log(arg);  // prints "ping"
  event.sender.send('asynchronous-reply', 'pong');
});

ipcMain.on('synchronous-message', function(event, arg) {
  console.log(arg);  // prints "ping"
  event.returnValue = 'pong';
});
```
在渲染进程，也就是 `ipc/index.js` 文件中添加如下代码：
```js
// 渲染进程 (web page).
const ipcRenderer = require('electron').ipcRenderer;
console.log(ipcRenderer.sendSync('synchronous-message', 'sync-ping')); // prints "pong"

ipcRenderer.on('asynchronous-reply', function(event, arg) {
  console.log(arg); // prints "pong"
});
ipcRenderer.send('asynchronous-message', 'async-ping');
```
{% alert info %}
详细代码可以查看[代码示例：ipc ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

还有一种进程间的通讯方式：`Electron remote` 模块。
{% alert success %}
`remote` 模块提供了一种在渲染进程（网页）和主进程之间进行进程间通讯（IPC）的简便途径。
{% endalert %}

通过 `remote` 调用主进程中 `BrowserWindow` 来创建应用窗口：
```js
// 渲染进程
const remote = require('electron').remote;
const BrowserWindow = remote.BrowserWindow;

let win = new BrowserWindow({ width: 100, height: 100 });
win.loadURL('http://www.jartto.wang');
```
{% alert info %}
详细代码可以查看[代码示例：remote ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 八、BrowserWindow 模块
在主进程 `main.js` 中，我们也看到了 `BrowserWindow` 的使用，简单的调用窗口很容易，更复杂的应用可能需要我们去翻阅[文档](https://electronjs.org/docs/api/browser-window)，熟读 `API`。
```js
// 在主进程中.
const {BrowserWindow} = require('electron')

// 或者从渲染进程中使用 `remote`.
// const {BrowserWindow} = require('electron').remote

let win = new BrowserWindow({width: 800, height: 600})
win.on('closed', () => {
  win = null
})

// 加载远程 URL
win.loadURL('http://jartto.wang')

// 或加载本地 HTML 文件
win.loadURL(`file://${__dirname}/app/index.html`)
```

#### 九、使用 Webview
与 `iframe` 不同，`webview` 和你的应用运行的是不同的进程。它不拥有渲染进程的权限，并且应用和嵌入内容之间的交互全部都是异步的，因为这能保证应用的安全性不受嵌入内容的影响。

`webview` 运行在渲染进程中，我们添加如下的 `js` 代码：
```js
window.onload = function() {
  var webview = document.getElementById("foo");
  var indicator = document.querySelector(".indicator");

  var loadstart = function() {
    indicator.innerText = "loading...";
  }
  var loadstop = function() {
    indicator.innerText = "";
  }

  webview.addEventListener("did-start-loading", loadstart);
  webview.addEventListener("did-stop-loading", loadstop);
  webview.addEventListener("dom-ready", function() {
    // webview.openDevTools();
    console.log(webview.getTitle());
  });
}
```
并在 `html` 中添加两个容器：
```html
<div class="indicator"></div>
<webview id="foo" src="http://www.jartto.wang/"></webview>
```
为了避免太过丑陋，我们增加如下 css 代码：
```css
body { margin: 0; }
#foo {
  width: 100%;
  height: 100vh;
}
```

ok，`webview` 的使用就这么简单的搞定了。

{% alert info %}
详细代码可以查看[代码示例：webview ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 十、Dialog 模块
`Dialog` 模块提供了 `API` 来展示原生的系统对话框，例如打开文件框，`alert` 框，这样 `web` 应用可以给用户带来跟系统应用相同的体验。下面给出简单的示例：

1.打开选择文件和目录
```js
var win = ...;  // BrowserWindow in which to show the dialog
const dialog = require('electron').dialog;
console.log(dialog.showOpenDialog({ properties: [ 'openFile', 'openDirectory', 'multiSelections' ]}));
```

2.错误框提示
```js
const dialog = require('electron').dialog;
dialog.showErrorBox('Jartto', 'Hello world');
// 或者传入更多的参数
// console.log(dialog.showMessageBox({
//   browserWindow: mainWindow,
//   options: {
//     type: 'error',
//     title: 'Jartto',
//     message: 'Hello World',
//   }
// }));

```

{% alert info %}
详细代码可以查看[代码示例：main.js ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 十一、Global-Shortcut 模块
`global-shortcut` 模块可以便捷的为您设置(注册/注销)各种自定义操作的快捷键，下面我们来为应用注册一个 `ctrl+x` 的全局快捷键，当我们按下快捷键，系统弹窗消息反馈给我们：
```js
const globalShortcut = require('electron').globalShortcut;
const dialog = require('electron').dialog;

app.on('ready', function(){
  createWindow();
  // Register a 'ctrl+x' shortcut listener.
  var ret = globalShortcut.register('ctrl+x', function() {
    dialog.showErrorBox('Jartto', 'ctrl+x is pressed')
  })

  if (!ret) {
    dialog.showErrorBox('Jartto', 'registration failed')
  }

  // Check whether a shortcut is registered.
  console.log(globalShortcut.isRegistered('ctrl+x'));
})

app.on('will-quit', function() {
  // Unregister a shortcut.
  globalShortcut.unregister('ctrl+x');

  // Unregister all shortcuts.
  globalShortcut.unregisterAll();
});
```

{% alert info %}
详细代码可以查看[代码示例：main.js ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 十二、Menu 模块
`Menu` 分为三种，每种菜单的用法也不一样，下面我们分别举例说明：
1.系统菜单
```js
// 窗口菜单
const remote = require('electron').remote;
const Menu = remote.Menu;

let template = [...]; // 省略参数，具体请查看 demo
let menu = Menu.buildFromTemplate(template);

Menu.setApplicationMenu(menu);
```

2.上下文菜单
```js
const remote = require('electron').remote;
const Menu = remote.Menu;
const MenuItem = remote.MenuItem;

// 右键菜单
var menu = new Menu();
menu.append(new MenuItem({ label: 'MenuItem1', click: function() { console.log('item 1 clicked'); } }));
menu.append(new MenuItem({ type: 'separator' }));
menu.append(new MenuItem({ label: 'MenuItem2', type: 'checkbox', checked: true }));

window.addEventListener('contextmenu', function (e) {
  e.preventDefault();
  menu.popup(remote.getCurrentWindow());
}, false);
```
3.托盘菜单
```js
let appIcon = new Tray(path.normalize(__dirname + '/image/icon.png'));
let contextMenu = Menu.buildFromTemplate([
  { label: '菜单一', type: 'radio' },
  { label: '菜单二', type: 'radio' },
  { label: '菜单三', type: 'radio', checked: true },
  { label: '关于', type: 'radio' }
]);

appIcon.setContextMenu(contextMenu);
```

{% alert info %}
详细代码可以查看[代码示例：menu ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 十三、Electron power-monitor
`power-monitor` 模块是用来监听能源区改变的。只能在主进程中使用。在 `app` 模块的 `ready` 事件触发之后就不能使用这个模块了。
```js
// 能源区变化，系统挂起和恢复，交流电和电池切换时
require('electron').powerMonitor.on('suspend', function() {
  console.log('The system is going to sleep');
});
require('electron').powerMonitor.on('on-ac', function() {
  console.log('The system is using AC power');
});
```
{% alert info %}
详细代码可以查看[代码示例：main.js ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 十四、系统托盘 Tray
在 OS X 中，托盘一般会出现在显示器右上角的位置，一个简单的示例可能如下：
```js
appIcon = new Tray(path.normalize(__dirname + '/image/icon.png'));
var contextMenu = Menu.buildFromTemplate([
  { label: 'Item1', type: 'radio' },
  { label: 'Item2', type: 'radio' },
  { label: 'Item3', type: 'radio', checked: true },
  { label: 'Item4', type: 'radio' }
]);
console.log(appIcon);
appIcon.setToolTip('This is my application.');
appIcon.setContextMenu(contextMenu);
```
如果想同时支持展示不同分辨率的图片，你可以将拥有不同 `size` 的图片放在同一个文件夹下，不用 `DPI` 后缀.例如 :
```
images/
├── icon.png
├── icon@2x.png
└── icon@3x.png
```

{% alert info %}
详细代码可以查看[代码示例：main.js ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 十五、Electron desktopCapturer 模块
`desktopCapturer` 模块可用来获取可用资源，这个资源可通过 `getUserMedia` 捕获得到。这样，我们就可以随便捕获其他的窗口，以及显示屏内容了。这里我们来抓取名称是 `Screen 2` 的屏幕，代码如下：
```js
// 在渲染进程中.
var desktopCapturer = require('electron').desktopCapturer;

desktopCapturer.getSources({types: ['window', 'screen']}, function(error, sources) {
  console.log('sources', sources);
  if (error) throw error;
  for (var i = 0; i < sources.length; ++i) {
    if (sources[i].name == "Screen 2") {
      navigator.webkitGetUserMedia({
        audio: false,
        video: {
          mandatory: {
            chromeMediaSource: 'desktop',
            chromeMediaSourceId: sources[i].id,
            minWidth: 1280,
            maxWidth: 1280,
            minHeight: 720,
            maxHeight: 720
          }
        }
      }, gotStream, getUserMediaError);
      return;
    }
  }
});

function gotStream(stream) {
  console.log(stream, 'stream');
  document.querySelector('video').src = URL.createObjectURL(stream);
}

function getUserMediaError(e) {
  console.log('getUserMediaError');
}
```
在 `html` 中，需要添加 `video` 元素：
```html
<video> desktop capturer </video>
```
学习过程中，我尝试了去捕获一个视频窗口，这样的话，我们就可以抓取不同窗口的内容放在同一个窗口中，是不是有点类似监控系统呢。

`sources` 是一个 `Source` 对象数组, 每个 `Source` 表示了一个捕获的屏幕或单独窗口，并且有如下属性 :

- id String - 在 navigator.webkitGetUserMedia 中使用的捕获窗口或屏幕的 id . 格式为 window:XX 或 screen:XX，XX 是一个随机数.
- name String - 捕获窗口或屏幕的描述名 . 如果资源为屏幕，名字为 Entire Screen 或 Screen <index>; 如果资源为窗口, 名字为窗口的标题.
- thumbnail NativeImage - 缩略图.

{% alert info %}
详细代码可以查看[代码示例：desktopCapturer ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 十六、Electron webFrame 模块
接着继续上面的话题，我们要将所有窗口都捕获在一个窗口显示，那么你可能会用到 `web-frame` ，该模块允许你自定义如何渲染当前网页。例如，放大当前页到 `200%`。
```js
let webFrame = require('electron').webFrame;
webFrame.setZoomFactor(2);
```
{% alert info %}
详细代码可以查看[代码示例：webFrame ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 十七、Electron clipboard 模块
`clipboard` 模块提供方法来供复制和粘贴操作，非常实用的一个功能。下面例子展示了如何将一个字符串写入 `clipboard` :
```js
const clipboard = require('electron').clipboard;
clipboard.writeText('Hello Jartto!');
```
{% alert info %}
详细代码可以查看[代码示例：clipboard ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 十八、Electron crashReporter 模块
`crash-reporter` 模块开启发送应用崩溃报告，这个就没什么好说了，收集反馈信息为了更好的用户体验。
```js
const crashReporter = require('electron').crashReporter;

crashReporter.start({
  productName: 'Jartto',
  companyName: 'Jartto blog',
  submitURL: 'http://jartto.com/url-to-submit',
  autoSubmit: true
});
```

#### 十九、Electron screen 模块
`screen` 模块检索屏幕的 `size`，显示，鼠标位置等的信息。在 `app` 模块的 `ready` 事件触发之前不可使用这个模块。
```js
const electron = require('electron');
const app = electron.app;
const BrowserWindow = electron.BrowserWindow;

var mainWindow;

app.on('ready', function() {
  var electronScreen = electron.screen;
  var size = electronScreen.getPrimaryDisplay().workAreaSize;
  mainWindow = new BrowserWindow({ width: size.width, height: size.height });
});
```

#### 二十、Electron shell 模块
`shell` 模块提供了集成其他桌面客户端的关联功能。
```js
在渲染进程中使用
const { shell } = require('electron')
// 播放声音
shell.beep();
// shell.openItem('/Users/Jartto/Documents/my-demo');
shell.openExternal('http://jartto.wang');
```
{% alert info %}
详细代码可以查看[代码示例：shell ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 二十一、Notification 通知模块
所有三个操作系统都提供了应用程序向用户发送通知的手段。 `Electron` 允许开发者使用 `HTML5 Notification API` 发送通知，并使用当前运行的操作系统的本地通知 `API` 来显示它。
{% alert danger %}
注意: 由于这是一个 HTML5 API，它只能在渲染器进程中使用。 如果你想在主进程中显示通知，请查看 Notification 模块.
{% endalert %}
```js
let myNotification = new Notification('Jartto', {
  body: 'Hello Everybody!'
})

myNotification.onclick = () => {
  console.log('通知被点击')
}
```
{% alert info %}
详细代码可以查看[代码示例：notification ](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start)
{% endalert %}

#### 二十二、在 Dock 中显示
在 `OS X` 中 `Dock` 默认会被打开，我们还可以在主进程中做一些额外的控制，如隐藏、设置菜单等操作。
```js
app.dock.hide();
app.dock.setMenu(menu);
```

#### 二十三、Demo 汇总
所有的示例代码都在这里，为了方便切换启动页面，我们通过不同的目录来区分，你只需要更改 `main.js` 中 `path` 中的路径即可，如下：
```js
mainWindow.loadURL(url.format({
  pathname: path.join(__dirname, '${dir-name}/index.html'),
  protocol: 'file:',
  slashes: true
}))
```
`Demo` 目录结构如下：
```bash
.
├── LICENSE.md
├── README.md
├── clipboard
│   ├── index.html
│   └── index.js
├── desktopCapturer
│   ├── index.css
│   ├── index.html
│   └── index.js
├── file
│   ├── index.html
│   └── index.js
├── image
│   ├── icon.png
│   ├── icon@1x.png
│   └── icon@2x.png
├── index.html
├── ipc
│   ├── index.html
│   └── index.js
├── main.js
├── menu
│   ├── index.html
│   ├── index.js
│   └── index1.js
├── node_modules
│   ├── @types
│   ├── _@types_node@7.0.50@@types
│   ├── _ajv@5.5.2@ajv
│   ├── ...
│   ├── wrappy
│   ├── xtend
│   └── yauzl
├── notification
│   ├── index.html
│   └── index.js
├── offline
│   └── index.html
├── package.json
├── remote
│   ├── index.html
│   └── index.js
├── renderer.js
├── shell
│   ├── index.html
│   └── index.js
├── webFrame
│   ├── index.html
│   └── index.js
└── webview
    ├── index.html
    └── index.js
```

你可以更改 `${dir-name}` 来切换不同的启动项，查看演示。

#### 二十四、代码调试
要开发一个完整的 `Electron` 应用，肯定会用到代码调试，这里暂且分为两部分调试：
1.对 `GUI` 的调试，也就是主进程的调试，可以借助开发工具，这里我用 `VS Code` 来举例说明：
- 在 `.vscode` 目录下添加 `launch.json` 文件，如果有的话，直接第二步；
- 打开 `launch.json`，写入如下配置；
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Electron Main",
      "protocol": "auto",
      "program": "${workspaceFolder}/electron-quick-start/main.js"
    },
  ]
}
```
- 运行 `debug`，就可以了;

2.渲染进程调试，可以开启调试工具：
```js
// 主进程中使用
mainWindow.webContents.openDevTools()
```
之后的调试就向你在 `Chrome` 中使用 `DevTool` 一样，简单而又高效。

#### 二十五、打包发布
{% alert success %}
`Electron` 应用开发完成之后，打包生产相应平台应用，通常推荐的工具有 `electron-packager` 和 `asar`。
{% endalert %}


1.用 `electron-packager` 为所有系统打包你的应用很简单。简单来说，`electron-packager` 帮你完成所有用`Electron` 打包你应用的工作，最终生成你要发布的平台的安装包。

它可以作为 `CLI` 应用或构建过程的一部分，更复杂的构建情况不在本文所涉及范围内，但我们如果能用打包脚本，会使打包更简单。用 `electron-packager` 比较麻烦，打包应用的基本命令是：
```bash
electron-packager <location of project> <name of project> <platform> <architecture> <electron version> <optional options>
```
具体的参数说明如下：
- `location of project` 是你项目文件夹的位置；
- `name of project` 定义你的项目名；
- `platform` 决定要构建的平台（*all* 包括 Windows ，Mac 和 Linux ）；
- `architecture` 决定构建哪个构架下（x86或x64，all表示两者）；
- `electron version` 让你选择要用的 `Electron` 版本；

{% alert warning %}
第一次打包用时比较久，因为要下载平台的二进制文件，随后的打包将会快的多。
{% endalert %}

在 `OS X` 下打包应用的命令如下：
```bash
electron-packager ~/Projects/jartto-demo MyElectron --all --version=0.30.2 --out=~/Desktop --overwrite --icon=~/Projects/jartto-demo/app/img/app-icon.icns
```
命令的选项理解起来都比较简单。为了获得精美的图标，你首先要找一款类似这个软件可以把 `PNG` 文件转换到这些格式的工具，把它转换成 `.icns` 格式（ `Mac` 用）或者 `.ico` 格式（ `Window` 用）。如果在非 `Windows` 系统给`Windows` 平台的应用打包，你需要安装 `wine`（ `Mac` 用户用 `brew` ，`Linux` 用户用 `apt-get`）。

每次都打这么长的命令很不方便，可以在 `package.json` 中加另一个脚本。首先，把 `electron-packager` 作为开发依赖安装：
```bash
npm install --save-dev electron-packager
```
现在我们可以在 `package.json` 中添加脚本：
```json
"scripts": {
  "start": "electron .",
  "package": "electron-packager ./ MyElectron --all --out ~/Desktop/jartto-demo --version 0.30.2 --overwrite --icon=./app/img/app-icon.icns"
}
```
在命令行里执行下面的命令：
```bash
npm run-script package
```
这个打包命令会启动 `electron-packager`，在当前目录下找到目标应用文件，打包，保存到桌面。如果你用的是`Windows` 系统，需要修改脚本，不过改动很小。

2.使用 `asar`
安装 `asar`
```bash
npm i asar -g
npm i asar --save-dev
```
编辑 `package.json`
```json
"scripts": {
    "start": "electron .",
    "build": "electron-packager . MyFirstApp --ignore=node_modules/electron-* --platform=win32 --arch=x64 --version=0.32.3",
    "package": "asar pack MyFirstApp-win32-x64/resources/app MyFirstApp-win32-x64/resources/app.asar && rm -rf MyFirstApp-win32-x64/resources/app"
}
```
然后执行：
```bash
npm run package
```

#### 二十六、Electron 源代码结构
{% alert success %}
当然，如果你需要深入了解 `Electron` ，官方也提供了一些帮助，我们可以通过如下的结构来深入学习。
{% endalert %}

```bash
Electron
├──atom - Electron 的源代码
|  ├── app - 系统入口代码
|  ├── browser - 包含了主窗口、UI 和其他所有与主进程有关的东西，它会告诉渲染进程如何管理页面
|  |   ├── lib - 主进程初始化代码中 JavaScript 部分的代码
|  |   ├── ui - 不同平台上 UI 部分的实现
|  |   |   ├── cocoa - Cocoa 部分的源代码
|  |   |   ├── gtk - GTK+ 部分的源代码
|  |   |   └── win - Windows GUI 部分的源代码
|  |   ├── default_app - 在没有指定 app 的情况下 Electron 启动时默认显示的页面
|  |   ├── api - 主进程 API 的实现
|  |   |   └── lib - API 实现中 Javascript 部分的代码
|  |   ├── net - 网络相关的代码
|  |   ├── mac - 与 Mac 有关的 Objective-C 代码
|  |   └── resources - 图标，平台相关的文件等
|  ├── renderer - 运行在渲染进程中的代码
|  |   ├── lib - 渲染进程初始化代码中 JavaScript 部分的代码
|  |   └── api - 渲染进程 API 的实现
|  |       └── lib - API 实现中 Javascript 部分的代码
|  └── common - 同时被主进程和渲染进程用到的代码，包括了一些用来将 node 的事件循环
|      |        整合到 Chromium 的事件循环中时用到的工具函数和代码
|      ├── lib - 同时被主进程和渲染进程使用到的 Javascript 初始化代码
|      └── api - 同时被主进程和渲染进程使用到的 API 的实现以及 Electron 内置模块的基础设施
|          └── lib - API 实现中 Javascript 部分的代码
├── chromium_src - 从 Chromium 项目中拷贝来的代码
├── docs - 英语版本的文档
├── docs-translations - 各种语言版本的文档翻译
├── spec - 自动化测试
├── atom.gyp - Electron 的构建规则
└── common.gypi - 为诸如 `node` 和 `breakpad` 等其他组件准备的编译设置和构建规则
```

#### 二十七、学习资源
学习过程中，发现了很多不错的资源，分享给大家，共同进步吧：
[Electron 官方文档](https://electronjs.org/docs)
[Electron 中文文档](https://www.w3cschool.cn/electronmanual/wcx31ql6.html)
[Electron 的入门学习](https://segmentfault.com/a/1190000006207600)
[用 Electron 开发桌面应用](http://get.ftqq.com/7870.get)
[Electron-Docs](https://github.com/pfan123/electron-docs)
[vue，electron 实现网易云音乐客户端](https://github.com/eugeneCN/vue-electron-music)
[awesome electron](https://github.com/sindresorhus/awesome-electron)
[使用 Electron 构建跨平台的抓取桌面程序](https://segmentfault.com/a/1190000006919539)
[electron-builder](https://www.npmjs.com/package/electron-builder)
[gulp-electron](https://github.com/mainyaa/gulp-electron)

#### 二十八、写在最后
由于篇幅实在是太长了，所以我打算把开发中遇到的问题留在后续文章中说明。这里先剧透一下，后续应该还会有两篇总结性的文章，标题已经想好了，我会在[ github ](https://github.com/chenfengyanyu/source)持续更新。先留个传送门吧！
- [初探 Electron - 升华篇](http://jartto.wang/2018/01/04/first-exploration-electron-2/)
- [初探 Electron - 实践篇1](http://jartto.wang/2018/01/14/first-exploration-electron-3/)




