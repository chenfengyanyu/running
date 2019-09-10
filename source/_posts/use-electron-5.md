---
title: 使用 Electron5.0 构建你的 React 项目
date: 2019-07-13 13:51:17
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/electron0.png
thumbnailImagePosition: left
tags: 
- electron
comments: false
metaAlignment: center
categories: 技术博文
---
最近要使用 Electron 来构建 React 项目，突然发现之前的 [Demo](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/electron-quick-start) 已经不能正常运行了，有些感概技术发展的迅猛。
<!-- more -->
如果你还不了解 `Electron`，可以看我之前的文章：[初探 Electron - 理论篇](http://jartto.wang/2018/01/03/first-exploration-electron/)，理论知识没有变。如果你想了解 `Electron5.0` 版本如何构建 `React` 项目，不妨继续看本文。

{% alert danger %}
需要注意：网上流传的 1.0 版本的项目应该已经启动不了了。
{% endalert %}

#### 一、为什么要写此文
之前研究的时候，`Electron` 还是 `1.0` 版本，因此写了一系列的文章。
- [初探 Electron - 理论篇](http://jartto.wang/2018/01/03/first-exploration-electron/)
- [初探 Electron - 升华篇](http://jartto.wang/2018/01/04/first-exploration-electron-2/)
- [初探 Electron - 实践篇1](http://jartto.wang/2018/01/14/first-exploration-electron-3/)
- [初探 Electron - 实践篇2](http://jartto.wang/2018/01/21/first-exploration-electron-4/)

经过本次尝试，发现 `5.0` 版本有了更多的变化，所以不想误人子弟，索性更新此系列文章。

{% alert info %}
这次起手，我们就来构建一个 React 项目。
{% endalert %}

#### 二、创建 React 项目
1.首先，全局安装 `cli`:
```bash
npm install --global create-react-app
```

2.创建项目
```bash
create-react-app electron5-react-demo
```

3.启动
```bash
cd electron5-react-demo && yarn start
```

4.访问 `http://localhost:3000/` 如果看到如下页面，说明你的 `React` 项目已经成功启动了。
![react](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/electron/react.png)

#### 三、集成 Electron 环境
1.首先需要安装 `Electron` 和 `Electron-builder`
```bash
yarn add electron electron-builder --dev
```

2.增加相应开发工具
```
yarn add wait-on concurrently --dev
yarn add cross-env electron-is-dev
```

3.项目根目录下新建文件：`electron.js`
```bash
touch public/electron.js
```
此时项目目录如下：
```bash
.
├── README.md
├── main.js
├── node_modules
├── public
│   ├── electron.js
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
├── src
│   ├── App.css
│   ├── App.js
│   ├── App.test.js
│   ├── index.css
│   ├── index.js
│   ├── logo.svg
│   └── serviceWorker.js
└── yarn.lock
```

4.修改代码，可以去[官网](https://github.com/electron/electron-quick-start/blob/master/main.js)上拷贝一份内容，写入 `electron.js`，具体如下：
```js
// Modules to control application life and create native browser window
const {app, BrowserWindow} = require('electron')
const path = require('path')
+ const isDev = require('electron-is-dev');

// Keep a global reference of the window object, if you don't, the window will
// be closed automatically when the JavaScript object is garbage collected.
let mainWindow

function createWindow () {
  // Create the browser window.
  mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })

+  mainWindow.loadURL(
+  isDev
+  ? 'http://localhost:3000'
+  : `file://${path.join(__dirname, "../build/index.html")}`
+  );
    
  // Open the DevTools.
  // mainWindow.webContents.openDevTools()

  // Emitted when the window is closed.
  mainWindow.on('closed', function () {
    // Dereference the window object, usually you would store windows
    // in an array if your app supports multi windows, this is the time
    // when you should delete the corresponding element.
    mainWindow = null
  })
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.on('ready', createWindow)

// Quit when all windows are closed.
app.on('window-all-closed', function () {
  // On macOS it is common for applications and their menu bar
  // to stay active until the user quits explicitly with Cmd + Q
  if (process.platform !== 'darwin') app.quit()
})

app.on('activate', function () {
  // On macOS it's common to re-create a window in the app when the
  // dock icon is clicked and there are no other windows open.
  if (mainWindow === null) createWindow()
})

// In this file you can include the rest of your app's specific main process
// code. You can also put them in separate files and require them here.
```
注意我们做了两处修改（加号位置）：
```js
// 引入环境变量：
const isDev = require('electron-is-dev');
...

// 设置启动文件
mainWindow.loadURL(
  isDev
  ? 'http://localhost:3000'
  : `file://${path.join(__dirname, "../build/index.html")}`
);
```

5.修改 `package.json` 文件，总共两处：
其一：
```bash
{
    "name": "electron5-react-demo",
    "version": "0.1.0",
    "private": true,
+    "main": "public/electron.js",
+    "homepage": "./",
}
```
其二，修改启动项：
```bash
"react-start": "react-scripts start",
"react-build": "react-scripts build",
"electron-start": "electron .",
"electron-build": "electron-builder",
"release": "yarn react-build && electron-builder --publish=always",
"build": "yarn react-build && yarn electron-build",
"start": "concurrently \"cross-env BROWSER=none yarn react-start\" \"wait-on http://localhost:3000 && electron .\""
"test": "react-scripts test",
"eject": "react-scripts eject",
```

#### 四、启动
```bash
yarn start
```
看到如下界面，恭喜你，已经成功启动了：
![Electron](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/electron/electron.png)

{% alert info %}
试着修改一下吧，热修改也已经生效了。
{% endalert %}

#### 五、构建
如果你想构建，可以使用
```bash
yarn build
```
{% alert danger %}
需要注意：构建会同时构建 React 和 Electron 两个项目。
{% endalert %}

构建完成后，项目目录中会出现一个 `dist` 目录：
```
.
├── builder-effective-config.yaml
├── electron5-react-demo-0.1.0-mac.zip
├── electron5-react-demo-0.1.0.dmg
├── electron5-react-demo-0.1.0.dmg.blockmap
├── latest-mac.yml
└── mac
    └── electron5-react-demo.app
```
目录中的 `dmg` 就是 `Mac` 上面的安装程序，双击安装：
![dmg](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/electron/dmg.png)

安装之后去运行吧，到这里，我们已经完成了整个项目。


#### 六、参考
- [Electron 文档](https://electronjs.org/docs?q=react)
- [API](https://github.com/electron/electron-api-demos)
- [How to build a React based Electron app](https://medium.com/@impaachu/how-to-build-a-react-based-electron-app-d0f27413f17f)










