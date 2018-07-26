---
title: Next.js 使用指南3－高级配置
date: 2018-06-08 07:13:11
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/nextjs.png
thumbnailImagePosition: left
tags: 
- nextjs
- react
comments: false
metaAlignment: center
categories: 技术博文
---
书接上文 [Next.js 使用指南2-路由与加载](http://jartto.wang/2018/06/01/nextjs-2/)，到这里，恭喜你已经完成了大部分的学习，基本使用已经足够。何不锦上添花，顺势拿下最难啃的部分呢？
<!-- more -->
#### 一、关于组件<document>
同 `App` 组件一样，`document` 组件也可以被覆写，这时我们需要创建 `.pages/_document.js` 文件：
```jsx
// _document is only rendered on the server side and not on the client side
// Event handlers like onClick can't be added to this file

// ./pages/_document.js
import Document, { Head, Main, NextScript } from 'next/document'

export default class MyDocument extends Document {
  static async getInitialProps(ctx) {
    const initialProps = await Document.getInitialProps(ctx)
    return { ...initialProps }
  }

  render() {
    return (
      <html>
        <Head>
          <style>{`body { margin: 0 } /* custom! */`}</style>
        </Head>
        <body className="custom_class">
          <Main />
          <NextScript />
        </body>
      </html>
    )
  }
}
```
{% alert info %}
Pages in Next.js skip the definition of the surrounding document's markup. For example, you never include <html>, <body>, etc.
{% endalert %}

#### 二、异常处理
`Nextjs` 中有默认的 `error.js` 文件，如果 `404` 或者 `500` 都会加载 `error` 组件。当然，该组件也可以被覆写。例如：
```jsx
// 创建_error.js 文件：
import React from 'react'

export default class Error extends React.Component {
  static getInitialProps({ res, err }) {
    const statusCode = res ? res.statusCode : err ? err.statusCode : null;
    return { statusCode }
  }

  render() {
    return (
      <p>
        {this.props.statusCode
          ? `An error ${this.props.statusCode} occurred on server`
          : 'An error occurred on client'}
      </p>
    )
  }
}
```
这样，我们就可以 `happy` 的写出漂亮的 `404` 页面了。此外，我们也可以重用内置的错误页，如下：
```jsx
import React from 'react'
import Error from 'next/error'
import fetch from 'isomorphic-unfetch'

export default class Page extends React.Component {
  static async getInitialProps() {
    const res = await fetch('https://api.github.com/repos/zeit/next.js')
    const statusCode = res.statusCode > 200 ? res.statusCode : false
    const json = await res.json()

    return { statusCode, stars: json.stargazers_count }
  }

  render() {
    if (this.props.statusCode) {
      return <Error statusCode={this.props.statusCode} />
    }

    return (
      <div>
        Next stars: {this.props.stars}
      </div>
    )
  }
}
```

#### 三、自定义配置文件
如果对默认配置不满意的话，我们可以在根目录下增加 `next.config.js` 文件：
- 方式一：
```js
// next.config.js
module.exports = {
  /* config options here */
}
```

- 方式二：
```js
module.exports = (phase, {defaultConfig}) => {
  //
  // http://jartto.wang/
  return {
    /* config options here */
  }
}
```
  {% alert success %}
  那我们都可以设置哪些选项呢，不着急，咱们一个个过一遍。
  {% endalert %}

- 定义构建目录，这样就会创建一个 `build` 文件夹，而不是 `.next` 文件夹了。
```js
// next.config.js
module.exports = {
  distDir: 'build'
}
```
  {% alert warning %}
  需要注意的是，如果之前没有配置，后续增加 next.config.js 文件，这时候 .next 文件可能需要手动清理了。
  {% endalert %}
  
- 禁止生成etag
```js
// next.config.js
module.exports = {
  generateEtags: false
}
```
  You can disable etag generation for HTML pages depending on your cache strategy. If no configuration is specified then Next will generate etags for every page.

- 配置 onDemandEntries
Next exposes some options that give you some control over how the server will dispose or keep in memories pages built:
```js
module.exports = {
  onDemandEntries: {
    // period (in ms) where the server will keep pages in the buffer
    maxInactiveAge: 25 * 1000,
    // number of pages that should be kept simultaneously without being disposed
    pagesBufferLength: 2,
  }
}
```
- 允许您在解析页面时配置在页面目录中查找的扩展。
```js
// next.config.js
module.exports = {
  pageExtensions: ['jsx', 'js']
}
```
- 增加构建id
```js
// next.config.js
module.exports = {
  generateBuildId: async () => {
    // For example get the latest git commit hash here
    return 'my-build-id'
  }
}
```
  Next.js uses a constant generated at build time to identify which version of your application is being served. This can cause problems in multi-server deployments when next build is ran on every server. In order to keep a static build id between builds you can provide the generateBuildIdfunction:

#### 四、配置 Webpack
```js
module.exports = {
  webpack: (config, { buildId, dev, isServer, defaultLoaders }) => {
    // Perform customizations to webpack config

    // Important: return the modified config
    return config
  },
  webpackDevMiddleware: config => {
    // Perform customizations to webpack dev middleware config

    // Important: return the modified config
    return config
  }
}
```
next提供了一些可供使用的模块：
* @zeit/next-css
* @zeit/next-sass
* @zeit/next-less
* @zeit/next-preact
* @zeit/next-typescript

此外，多个配置项也是可以合并处理的，如下
```js
const withTypescript = require('@zeit/next-typescript')
const withSass = require('@zeit/next-sass')

module.exports = withTypescript(withSass({
  webpack(config, options) {
    // Further custom configuration here
    return config
  }
}))
```
#### 五、配置 Babel
如果需要扩展 `Babel`，这样的话，就需要额外增加 `.babelrc` 文件了：
```json
{
  "presets": ["next/babel"],
  "plugins": []
}
```
#### 六、区别客户端和服务端配置
The config key allows for exposing runtime configuration in your app. All keys are server only by default. To expose a configuration to both the server and client side you can use the public key.

```js
// next.config.js
module.exports = {
  serverRuntimeConfig: { // Will only be available on the server side
    mySecret: 'secret'
  },
  publicRuntimeConfig: { // Will be available on both server and client
    staticFolder: '/static'
  }
}
```

```jsx
// pages/index.js
import getConfig from 'next/config'
// Only holds serverRuntimeConfig and publicRuntimeConfig from next.config.js nothing else.
const {serverRuntimeConfig, publicRuntimeConfig} = getConfig()

console.log(serverRuntimeConfig.mySecret) // Will only be available on the server side
console.log(publicRuntimeConfig.staticFolder) // Will be available on both server and client

export default () => <div>
  <img src={`${publicRuntimeConfig.staticFolder}/jartto.png`} alt="logo" />
</div>
```

#### 七、CDN 前缀支持
```js
const isProd = process.env.NODE_ENV === 'production'
module.exports = {
  // You may only need to add assetPrefix in the production.
  assetPrefix: isProd ? 'https://cdn.jartto.com' : ''
}
```

#### 八、输出静态 HTML 文件
如果我们不使用 `node server`，也是可以使用 `Next.js` 的，譬如动态 `URL`，预加载，动态导入等。
```js
// next.config.js
module.exports = {
  exportPathMap: function(defaultPathMap) {
    return {
      '/': { page: '/' },
      '/about': { page: '/about' },
      '/readme.md': { page: '/readme' },
      '/p/hello-nextjs': { page: '/post', query: { title: 'hello-nextjs' } },
      '/p/learn-nextjs': { page: '/post', query: { title: 'learn-nextjs' } },
      '/p/deploy-nextjs': { page: '/post', query: { title: 'deploy-nextjs' } }
    }
  }
}
```
这时候，`package.json` 中需要做如下修改：
```json
{
  "scripts": {
    "build": "next build && next export"
  }
}
```
然后运行：
```bash
npm run build
```
{% alert info %}
这时候就会导出一个 out 目录，这样的操作很可能让你更容易的构建出一个 Github Pages 项目。
{% endalert %}

#### 九、完结
陆陆续续写了三篇关于 `Next.js` 的文章，如果你想学习的话，建议从一开始看，由浅入深。结构目录如下：
- [Next.js 使用指南1-基本规则](http://jartto.wang/2018/05/27/nextjs-1/)
- [Next.js 使用指南2-路由与加载](http://jartto.wang/2018/06/01/nextjs-2/)
- [Next.js 使用指南3-高级配置](http://jartto.wang/2018/06/08/nextjs-3/)

文中大多都是自己的一些理解，难免会有偏差。如果有哪些不够严谨的地方，欢迎大家提宝贵意见。总而言之，互相学习吧！

