---
title: Nuxtjs 2.0 升级爬坑
date: 2019-04-23 19:58:32
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/nuxt/nuxt.png
thumbnailImagePosition: left
tags: 
- ppt
- impress
comments: false
metaAlignment: center
categories: 技术博文
---
当我在升级 Nuxt2.0 的过程，遇到了很多问题，逐一查找解决，废了不少时间。回头想想，真希望在升级的时候看到这样一篇文章，让我少走一些弯路。
<!-- more -->
{% alert success %}
这篇文章的意义也在于此，希望对大家能有所帮助。
{% endalert %}

#### 一、为什么要从 `Nuxt1.0` 升级到 `Nuxt2.0`？
我们来看看 `Nuxt2.0` 有哪些[更新](https://www.cnblogs.com/zhuanzhuanfe/p/9101932.html)：

1.支持 `webpack4`
`webpack4` 有很多优化的提升，升级后就可以嗨皮的使用了。

2.弃掉了 `venders`
我们以前一直使用 `vendors chunk`，这次发布后，我们不再使用 `CommonsChunkPlugin`，所以不必明确指定 `vendors`。

`Nuxt` 自动添加了核心的 `packages` (包括 `vue`, `vue-router`, `babel-runtime`...)到 `Cache Group`中。

{% alert info %}
这使得 `webpack` 可以用最合理的方式拆分你的代码。
{% endalert %}

3.`chunk splitting` 的完全控制
尽管 `nuxt` 试图提供最有效的分割，但现在可以使用 `build.splitChunks` 选项完全控制它，并且可以选择禁用每个路由的异步块。

4.`Vue Loader 15 and mini-css-extract-plugin`
`Vue-Loader 15` 进行了完全的重写，它使用了一种完全不同的新架构，能够将 `webpack` 配置中定义的任何规则应用于 `*.vue` 文件内。

对于 `CSS` 抽取，使用一个新的插件 `mini-css-extract-plugin`，它支持 `CSS` 和 `SourceMaps`（`CSS splitting`）的按需加载，并构建在新的 `webpack v4` 特性（`module types`）上。

5.`nuxt es modules`
我们可以在 `nuxt.config.js` 中使用 `import`, `export`, 服务器 `middleware`, `modules` 。

6.`CLI` 改善
`nuxt2.0` 自动检测配置项和测试环境，并将切换到一个称为 `minimalCLI` 的特殊模式，其中包含更少的详细消息。

#### 二、升级 2.0
```
npm install nuxt@latest
```

#### 三、记得重新安装一下依赖
```
npm i
```

#### 四、升级问题
问题一：
```
Module build failed: TypeError: Cannot read property 'eslint' of undefined
```

碰到这个问题，是因为 `isClient` 新版本已经移除了，我们试着[理解一下](https://github.com/nuxt/nuxt.js/issues/3216)：
```
isClient was removed in nuxt-edge, it should be replaced by process.client in your nuxt.config.js as below.
```

`Nuxtjs1.0` 我们是这么用：
```js
extend(config, { isDev, isClient }) {
    if (isDev && isClient) {
        config.module.rules.push({
        enforce: "pre",
        test: /\.(js|vue)$/,
        loader: "eslint-loader",
        exclude: /(node_modules)/
    });
}
```
所以应该修改成：
```js
extend (config, { isDev }) {
  if (isDev && process.client) {
        config.module.rules.push({
          enforce: 'pre',
          test: /\.(js|vue)$/,
          loader: 'eslint-loader',
          exclude: /(node_modules)/
        })
  }
}
```

{% alert info %}
注意 isClient 和 process.client！
{% endalert %}


问题二： 
```bash
ERROR  Failed to compile with 1 errors
error  in ./.nuxt/router.js

Module parse failed: Unexpected token (24:8)
You may need an appropriate loader to handle this file type.
|
| var _5f05608f = function _5f05608f() {
>     return import('../pages/account/index.vue' /* webpackChunkName: "pages/account/index" */).then(function (m) {
|         return m.default || m;
|     });

@ ./.nuxt/index.js 334:14-36
@ ./.nuxt/client.js
@ multi webpack-hot-middleware/client?name=client&reload=true&timeout=30000&path=/__webpack_hmr ./.nuxt/client.js
```

碰到这个问题，应该是某些包不兼容，所以可以先清除 `node_modules`，重新安装：
```bash
rm -rf node_modules/
npm i
```


问题三：
```
Error: Plugin/Preset files are not allowed to export objects, only functions
```

这个问题是因为 `Babel7` 的更新，所以我们需要[更新配置文件](https://stackoverflow.com/questions/47830273/babel-plugin-preset-files-are-not-allowed-to-export-objects-only-functions)：

首先修改 `package.json`：
```json
"devDependencies": {
    "@babel/core": "^7.1.6",
    "@babel/preset-env": "^7.1.6",
    "@babel/preset-react": "^7.0.0",
    "babel-loader": "^8.0.4",
    "webpack": "^4.25.1",
    "webpack-cli": "^3.1.2"
}
```
接着更改 `nuxt.config.js`，如果使用了动态导入，需要注意[如下配置](https://github.com/styleguidist/react-styleguidist/issues/987)：
```js
presets: ['@babel/env', '@babel/react'],
plugins: [
  '@babel/plugin-syntax-dynamic-import'
]
```

问题四：
```
This dependency was not found: vant/lib/vant-css/index.css in ./plugins/vant.js
import 'vant/lib/vant-css/index.css';
```
我们发现升级后，第三方 `UI` 库的样式找不到了，通过查找，发现是文件[引用路径](https://youzan.github.io/vant/#/zh-CN/quickstart)发生了变化：
```js
import Vue from 'vue’;
import Vant from 'vant’;
import 'vant/lib/index.css’;

Vue.use(Vant);
```

问题五：静态资源加载异常
{% alert warning %}
这个异常很明显，图片全部访问不了了，所以一定要注意看看 `Nuxt2.0` 做了[哪些更改](https://zh.nuxtjs.org/guide/assets/)。
{% endalert %}

请注意: 从 `Nuxt2.0` 开始，`~/alias` 将无法在 `CSS` 文件中正确解析。你必须在 `url CSS` 引用中使用 `~assets`（没有斜杠）或 `@` 别名，即 `background:url("~assets/banner.svg")`


问题六： 
```
WARN  Using an Array as build.postcss will be deprecated in Nuxt 3. Please switch to the object declaration
```
{% alert danger %}
意思很明了，postcss 以后需要用[对象声明](https://zh.nuxtjs.org/faq/postcss-plugins/)，不再支持数组方式。
{% endalert %}

我们将：
```js
postcss: [
    require("postcss-px2rem-exclude")({
        remUnit: 75,

        exclude: /node_modules|vant/

    }),
    require('autoprefixer')({
        browsers: ['Android >= 4.0', 'iOS >= 7']

    })
],
```
替换成：
```js
postcss: {
    'postcss-px2rem-exclude': {
        emUnit: 75,
        exclude: '/node_modules|vant/'
    },
    'autoprefixer': {
        browsers: ['Android >= 4.0', 'iOS >= 7’]
    }
},
```

问题七：
```
ReferenceError：regeneratorRuntime is not defined
```
需要增加 babel [运行时编译](https://github.com/nuxt/nuxt.js/issues/934)，配置 `package.json`：
```json
"babel-plugin-transform-runtime": "^6.23.0",
```
修改 `nuxt.config.js` 中的配置：
```js
plugins: [
  '@babel/plugin-syntax-dynamic-import',
  '@babel/transform-runtime'
]
```

问题八：
```
EACCES: permission denied, mkdir '/Users/jartto/Documents/project/primary-station/node_modules/.cache’
```

使用 `sudo` 启动，例如：
```
sudo npm run start
```

问题九：
```
ERROR in Sentry CLI Plugin: Command failed: /apps/srv/instance/test-touch.gaotu100.com/node_modules/@sentry/cli/sentry-cli releases new 2.4.0
error: An organization slug is required (provide with --org)

Add --log-level=[info|debug] or export SENTRY_LOG_LEVEL=[info|debug] to see more output.
Please attach the full debug log to all bug reports.
```
这个问题类似和问题一一样，注意替换 `isClient`。

#### 五、升级前后对比
![diff](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/nuxt/diff.png)

[如上](http://jartto.wang/2019/04/23/update-nuxt2-0/)截图，我们只取了一部分，但是可以看到，文件被更细粒度的进行了拆分。这完全要归功于 `splitChunks`。当然，更多优化点和细节你可以慢慢来体会。