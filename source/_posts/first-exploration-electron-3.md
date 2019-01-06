---
title: 初探 Electron - 实践篇1
date: 2018-01-14 22:53:49
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/electron0.png
thumbnailImagePosition: left
tags: 
- electron
comments: false
metaAlignment: center
categories: 技术博文
---
经历过[前两篇](http://jartto.wang/2018/01/03/first-exploration-electron/)的洗礼，相信大家已经掌握了 Electron 基本的用法，那么还在等什么，快上手项目试试吧！
<!-- more -->
{% alert info %}
查看[原文](http://jartto.wang/2018/01/14/first-exploration-electron-3/)，获得更好的体验哦～
{% endalert %}

实践篇将以项目主导，我打算由浅入深从零到一去完成该项目，我们来做一个图片文字识别工具 [`Electron-OCR`](https://github.com/)，目标如下：
- 图片上传：拖拽或点击上传
- 图片预览：上传完成，直接进入预览页，同时执行文字识别
- 菜单页：简单的导航列表，持续扩充
- 在线演示功能：提供了更多的图片，便于演示效果
- 繁体字识别：繁体字需要调用付费接口，所以需要单独处理，增加了繁体校准功能
- 大图裁切：为了精准识别，需要将图片化繁为简
- 体验优化：图片识别成功后返回结果，并拷贝入粘贴板，支持语音播报
- ...

涉及到的技术如下：
{% alert success %}
electron, electron-compile, electron-reload, react, react-router@4, react-motion, ES6/ES7, Promise, Surperagent, Less, CSS-Modules, eslint, babelrc, cropper, Material-UI...
{% endalert %}

#### 一、先睹为快
本文的示例代码已经放在了 [`Github`](https://github.com/chenfengyanyu/electron-ocr) 上，效果图如下：

{% image fancybox left fig-33  group:travel http://7xvi3w.com1.z0.glb.clouddn.com/electron7.png %}
{% image fancybox left fig-33  group:travel http://7xvi3w.com1.z0.glb.clouddn.com/electron8.png %}
{% image fancybox left fig-33 group:travel http://7xvi3w.com1.z0.glb.clouddn.com/electron9.png %}
{% image fancybox left fig-33  group:travel http://7xvi3w.com1.z0.glb.clouddn.com/electron10.png %}
{% image fancybox left fig-33  group:travel http://7xvi3w.com1.z0.glb.clouddn.com/electron12.png %}
{% image fancybox left fig-33  group:travel http://7xvi3w.com1.z0.glb.clouddn.com/electron13.png %}

你可以直接 `clone` 项目 `npm i & npm run start` 运行，或者按照本文的思路一步步实现。

#### 二、创建项目
首先，我们本地创建项目 `Electron-OCR-demo`，如下：
```bash
# 找到合适的目录，创建项目
cd demos 
mkdir Electron-OCR-demo
```

#### 三、初始化项目
1.创建 `app` 文件夹，用于存放前端项目主体文件；
```
mkdir app
```
2.创建 `service` 文件夹，存放请求服务文件以及后端文件；
3.创建 `.gitignore` 文件：
```
vi .gitignore
```
并写入规则：
```bash
.vscode
node_modules
.log
```
5.增加 README.md 文件；
```
touch README.md
```
6.初始化 `package.json` 文件：
```bash
npm init
```
ok，大功告成，我们来同步一下此时的项目结构：
```
.
├── README.md
├── app
├── .gitignore
├── package.json
└── service
```

#### 四、快速开始
上面我们做了一些前期的准备工作，既然是用 `ELectron` 来做，那么当然需要安装依赖了：
```
npm install electron --save-dev
```
{% alert warning %}
安装比较慢，建议使用淘宝镜像 `cnpm install electron --save-dev` 来安装。
{% endalert %}

我们先来运行一个简单示例，进入 `app` 目录，添加三个文件，分别是：
1.`main.js`
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
2.`index.html`
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
  </head>
  <body>
    <h1>Hello World!</h1>
    <script>
      // 当然，我们也可以引入其他 js 文件
      require('./renderer.js')
    </script>
  </body>
</html>
```
3.`render.js`
```js
let myNotification = new Notification('Jartto', {
  body: 'Hello Everybody!'
})
myNotification.onclick = () => {
  console.log('Click!')
}
```
{% alert danger %}
如果你不懂这是在干什么，建议先阅读 [初探 Electron - 理论篇](http://jartto.wang/2018/01/03/first-exploration-electron/)
{% endalert %}

最后一步，设置启动项，打开 `package.json`：
将 `"main": "index.js"` 修改为：
```json
"main": "app/main.js",
```
同时增加 `"start": "electron ."` 配置项，指定使用 `Electron` 启动： 
```
"scripts": {
  "start": "electron .",
  "test": "echo \"Error: no test specified\" && exit 1"
},
```
接下来，我们打开命令行，执行：
```bash
npm run start
```
如果你看到了窗口中的 `Hello World!`，那么恭喜你，示例已经正确运行了。

#### 五、继续升级
我们通过手动创建完成了和官网起手项目类似的功能，但路还有些远，我们来做一些升级工作，使用 `react` 来开发项目。
{% alert info %}
`react` 需要额外的一些解析，所以我们借助 [`electron-compile`](https://github.com/electron-userland/electron-compile) 来动态编译。
{% endalert %}

先来安装 `react, react-dom, electron-compile`，如果太慢请考虑使用 `cnpm` :
```
npm install electron-compilers --save-dev
npm install react react-dom electron-compile --save
```
因为要预编译文件，所以我们需要为 `electron-compile` 指定入口文件，在项目根目录下新建 `index.js` 文件，写入：
{% alert info %}
注意，记得将 `package.json` 中，做同步修改 `"main": "index.js"` ！
{% endalert %}
```js
const path = require('path');
let appRoot = path.join(__dirname, '');

require('electron-compile').init(appRoot, require.resolve('./app/main'));
```
{% alert success %}
这里先指定在 `app` 目录下的启动文件 `main.js`，其实也就是该目录下的主进程文件。
{% endalert %}
接下来，我们用 `react` 改写一下 `render.js`，修改如下：
```js
import React from 'react';
import ReactDOM from 'react-dom';

class MainWindow extends React.Component {

  constructor(props) {
    super(props);

    this.state = {
      
    };
  }

  render() {
    return (
      <div>
        Good, React Ready!
      </div>
    );
  }
}

const mainWndComponent = ReactDOM.render(
  <MainWindow/>, document.getElementById('content'));
```
最后一步，去 `index.html` 注册 `react` 组件：
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title> Electron-OCR </title>
  </head>
  <body>
    <div id="content"></div>
    <script>
      require('./render.js');
    </script>
  </body>
</html>
```
基本工作完成，我们试着启动一下：
```bash
npm run start
```

什么，没启动起来？
{% alert danger %}
Uncaught SyntaxError: Unexpected token import
{% endalert %}

不要紧张，我们还需要增加个编译规则文件，这很重要！

#### 六、配置编译规则
大家可能都配置过或者间接使用过 [`babelrc`](https://babeljs.cn/docs/usage/babelrc/) 做转换编译，`electron-compile` 中的 `.compilerc` 如出一辙，我们先创建文件：
```bash
touch .compilerc
```
然后写入我们的规则，如下：
```json
{
  "env": {
    "development": {
      "application/javascript": {
        "presets": ["env", "react"],
        "sourceMaps": "inline",
      },
      "text/less": {
        "dumpLineNumbers": "comments"
      }
    },
    "production": {
      "application/javascript": {
        "presets": ["env", "react"],
        "sourceMaps": "none",
      }
    }
  }
}
```
{% alert info %}
这里记得安装依赖包文件 `babel-preset-env` 和 `babel-preset-react`
{% endalert %}
```bash
npm install babel-preset-env babel-preset-react --save-dev
```
OK，搞定，来看看此时我们的 `package.json` 文件：
```json
{
  "name": "electron-ocr-demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "electron .",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "electron",
    "demo"
  ],
  "author": "jartto",
  "license": "ISC",
  "devDependencies": {
    "babel-preset-env": "^1.6.1",
    "babel-preset-react": "^6.24.1",
    "electron": "^1.7.10",
    "electron-compilers": "^5.9.0"
  },
  "dependencies": {
    "electron-compile": "^6.4.2",
    "react": "^16.2.0",
    "react-dom": "^16.2.0"
  }
}
```
此时的项目结构应该如下：
```
.
├── app
│   ├── index.html
│   ├── main.js
│   └── render.js
├── node_modules
│   ├── ...
├── service
├── .compilerc
├── .gitignore
├── index.js
├── package.json
└── README.md
```
一切就绪，启动 `npm run start`，如果你看到：`Good, React Ready!` ，那么恭喜你，`react` 和 `compile` 文件已经可以正常工作了。

#### 六、热启动
太棒了，美中不足的就是 `react` 不能热加载，没关系，我们还有 `electron-reload`，先安装依赖包：
```bash
npm install electron-reload --save-dev
```
同时，需要在主进程文件 main.js 中增加如下代码：
```js
const isDevelopment = true;
if (isDevelopment) {
  /* eslint-disable */
  require('electron-reload')(__dirname, {
    electron: require('${__dirname}/../../node_modules/electron'),
    ignored: /node_modules|[\/\\]\./
  });
}
```

重新启动，快更新文件试试吧，热更新也已经就绪了！

#### 七、回顾小结
实践篇，我们从零到一搭建了一个用 `react` 来开发 `electron` 的简单框架，完成了支持 `es6` 的编译配置，同时也添加了热加载。到这里，项目已经初具规模，你可以去做任何想做的事情了～

下一节，我们将继续深入，对框架做一些易用性的优化以及补充，逐步完善项目。传送门在此：[初探 Electron - 实践篇2](http://jartto.wang/2018/01/21/first-exploration-electron-4/)。
