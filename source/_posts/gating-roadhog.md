---
title: 探路 Roadhog
date: 2017-04-25 23:13:20
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/roadhog0.png
thumbnailImagePosition: left
tags: 
- roadhog
- antd
comments: false
metaAlignment: center
categories: 技术博文 
---
为了紧跟 ant design 的步伐，前不久将项目从 dora 切换到了 roadhog。当然，问题也如雨后春笋一般，陆续冒出来，总结一下，也不负折腾。
<!-- more -->
#### 一、Roadhog 是什么？
`dva` 是基于 `redux`，`redux-saga`，`react-route` 的框架，`roadhog` 是基于 `create-react-app` 脚手架优化简便的脚手架。
{% alert info %}
我们可以这样理解：`dva` 相当于 `react + redux`，而 `roadhog` 则是 `webpack`
{% endalert %}

官网是这么描述的：由于 create-react-app 的默认配置不能满足需求，而他又不提供定制的功能，于是基于他实现了一个可配置版。所以如果既要 create-react-app 的优雅体验，又想定制配置，那么可以试试 roadhog 。

#### 二、如何使用？
首先，全局安装 `roadhog`
```sh
npm i roadhog -g
```
其次，在项目根目录下创建 .roadhogrc 文件，并添加如下代码（[配置项请参看官网文档](https://github.com/sorrycc/roadhog)）：
```js
{
  "entry": "src/index.js",
  "disableCSSModules": false,
  "publicPath": "/",
  "theme": {
    "@primary-color": "#108ee9",
    "@link-color": "#1DA57A",
    "@border-radius-base": "2px",
    "@font-size-base": "16px",
    "@line-height-base": "1.2"
  },
  "externals": {
    "XLSX": "window.XLSX",
    "jquery": "jQuery"
  },
  "autoprefixer": null,
  "extraBabelPlugins": [
    "transform-runtime",
    ["import", { "libraryName": "antd", "style": "css" }]
  ],
  "env": {
    "development": {
      "extraBabelPlugins": [
        "dva-hmr"
      ]
    }
  }
}
```
然后，我们就可以通过启动项目了：
```sh
roadhog server
```
最后，在需要构建发布的时候，执行：
```
roadhog build
```
此外，启动测试，默认会跑 ./test 目录下的所有文件
```
roadhog test
```
#### 三、从 atool-build + dora 转换到 roadhog
此处参考了作者的[说明文档](https://www.tuicool.com/articles/nEreu2e)，大致过程如下：

1.修改 `package.json`，删除 `atool-build` 和 `dora` 相关依赖，加上 `roadhog` 依赖。
```
npm install roadhog --save-dev
```
2.修改 `scripts` 部分，让 `start` 和 `build` 走 `roadhog`：
```
"start": "roadhog server"
"build": "roadhog build"
```
3.新增 .roadhogrc
如果是用 `dva + antd` 的组合，`babel` 插件部分通常这么配：
```
"extraBabelPlugins": [
  "transform-runtime",
  ["import", { "libraryName": "antd", "style": "css" }]
],
"env": {
  "development": {
    "extraBabelPlugins": [
      "dva-hmr"
    ]
  }
}
```
4.把 `webpack.config.js` 中的配置迁移到 `.roadhogrc` 中。
5.删除 `webpack.config.js`

{% alert info %}
最后一定要删除 `webpack.config.js`，否则可能会出现异常。
{% endalert %}

#### 四、全局安装 rodehog，启动 roadhog server 异常处理
按照步骤当运行 `roadhog server` 的时候，报出了如下的错误：
```
Failed to compile.
Error in ./src/index.js
Module build failed: Error: /Users/fox/Documents/ceb-admin/src/index.js: [babel-plugin-dva-hmr error] You must set the `entries` option.
@ multi index
```

这时候，需要看一下 babel-plugin-dva-hmr 升级到最新的版本 0.3.2，再次运行 roadhog server
系统默认打开 http://localhost:8000/

不错，项目启动起来了。

#### 五、接着，我们需要打包发布：
执行
```
roadhog build 
```
可以看到生成了。

#### 六、打包完，启动服务器模拟一下部署环境
你可以使用 `ngnix`，或者 `http-server` 启动：

ok，访问链接，页面正常打开，没有问题。

#### 七、其他异常：
1.Error in Cannot find module 'less'
初步怀疑并没有添加 `less loader`，执行
```
npm install less —save
```
2.使用 `public` 目录
我们约定 `public` 目录下的文件会在 `server` 和 `build` 时被自动 `copy` 到输出目录（默认是 ./dist）下。所以可以在这里存放 `favicon`, `iconfont`, `html`, `html` 里引用的图片等。

3.图片路径访问不到，奇怪
需要放到 `public` 目录下，且访问不需要带 `public` 目录

4.Cannot find module 'webpack/lib/removeAndDo'
出现如上提示，需要局部安装 `webpack`，执行
```
npm i webpack --save-dev
```

[详情](https://github.com/webpack/webpack/issues/2131)

5.Chunk.entry was removed. Use hasRuntime()
执行：
```
npm install webpack-extract-text-plugin —save
```
还有：
```
npm install extract-text-webpack-plugin@^2.0.0-beta --save-dev
```
[详情](https://github.com/webpack/webpack/issues/2764)

#### 八、打包慢怎么办
```
roadhog build --debug
roadhog build --watch
roadhog build -h
```
此外，我们可以分析一下构建后文件大小：
```
roadhog build --analyze
```
当出现如下提示，说明已经成功生成了分析文件：
```
Analyze result is generated at dist/stats.html.
```
让然，我们还可以查看 build 时的编译时间：
```
time roadhog build
```
结果可能如下：
```
roadhog build  79.98s user 3.38s system 99% cpu 1:23.95 total
```

{% alert info %}
这里指包括开发时的编译时间，修改代码重编译时间。
{% endalert %}

#### 九、参考：
1. [roadhog](https://github.com/sorrycc/roadhog)
2. [从 dora 转到 roadhog](http://www.tuicool.com/articles/nEreu2e)
3. [webpack](https://github.com/webpack/webpack/issues/2131)