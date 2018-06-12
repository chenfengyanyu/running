---
title: Next.js 使用指南1－基本规则
date: 2018-05-27 06:12:45
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/nextjs.png
thumbnailImagePosition: left
tags: 
- nextjs
- react
comments: false
metaAlignment: center
categories: 技术博文
---
提起服务端渲染，大伙肯定并不陌生。不论是前端渲染还是服务端渲染，都是值得我们深入研究的。这节我们来了解一下 React 服务端渲染框架 Next.js。
<!-- more -->
#### 一、服务端渲染
{% alert success %}
服务端渲染，是指页面的渲染和生成由服务器来完成，并将渲染好的页面返回客户端。而客户端渲染是页面的生成和数据的渲染过程是在客户端（浏览器或 APP ）完成。
{% endalert %}

当大家提到服务端渲染(SSR: Server Side Rendering)，首先想到的好处肯定是有利于 `SEO`，但这个并不是唯一的好处。还有更大的好处，譬如：
- 服务器端渲染能更快地显示页面；
- 服务器对浏览器进行响应是在 HTML 页面可以渲染的时候，因此浏览器可以不用等待所有的 JavaScript 被下载和执行就可以开始渲染；
- 当浏览器下载并执行页面所需的 JavaScript 和其他资源时，不会出现 “白屏” 现象；


#### 二、Next.js 是什么？
[Next.js](https://nextjs.org/docs/#setup) 是一个基于 `React` 实现的服务端渲染框架。主要有如下功能：
* 服务器端渲染(默认)
* 自动代码切分, 加速页面加载
* 简单的客户端路由(基于页面)
* 基于 Webpack 的开发环境, 支持热模块替换(HMR: Hot Module Replacement)
* 使用 React 的 JSX 和 ES6 的 module，模块化和维护更方便
* 可以使用 Express 或其他 Node.js 服务器实现
* 使用 Babel 和 Webpack 配置定制

#### 三、安装与配置
先来执行安装操作：
```bash
npm install --save next react react-dom
```
{% alert warning %}
注意：Next.js only supports React 16.
{% endalert %}

为方便使用，我们写入 `package.json` 配置：
```json
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

#### 四、快速开始
{% alert success %}
通过上面的操作，我们已经安装和配置好 Nextjs 的使用环境，是时候表演真正的技术了。
{% endalert %}

首先，在根目录下建立 `pages` 文件夹，并新增子文件 `index.js`，写入：
```js
export default () => <div>Welcome to jartto's blog!</div>
```
然后，启动服务：
```
npm run dev
```
{% alert info %}
如果需要修改端口号：`npm run dev -- -p 8888`
{% endalert %}

最后，打开 `http://localhost:3000` ，看到 `Welcome to jartto's blog!` 就表明你已经成功了。什么，这么简单，我不需要配置路由吗？

是的，`Next.js` 的思想就是约定大于配置，这也就是我们需要将页面放到 `pages` 文件夹中的原因。如果你觉得 `Next.js` 上手比较难的话，可以先了解一下国产版 [UmiJs 尝鲜](http://jartto.wang/2018/05/24/taste-of-umi/)。

#### 五、修改 CSS 使用方式
`Next.js` 默认使用 `styled-jsx`：
```jsx
export default () =>
  <div>
    Hello world
    <p>scoped!</p>
    <style jsx>{`
      p {
        color: blue;
      }
      div {
        background: red;
      }
      @media (max-width: 600px) {
        div {
          background: blue;
        }
      }
    `}</style>
    <style global jsx>{`
      body {
        background: black;
      }
    `}</style>
  </div>
```
当然，我们也可以扩展 `css`，`sass`，`less`，`stylus`：
- next-css
- next-sass
- next-less
- next-stylus

#### 六、静态资源如何引用？
首先，我们需要在项目根目录创建 `static` 文件夹：
```
mkdir static
```
然后，在 `pages` 目录下新建：`demo.js`：
```
export default () => <img src="/static/img/jartto.png" alt=“jartto's blog" />
```
{% alert info %}
注意上面图片的引用方式：/static/img/jartto.png
{% endalert %}

#### 七、使用页头组件：<head>
我们修改 `index` 文件：
```jsx
import Head from 'next/head'

export default () =>
  <div>
    <Head>
      <title>Jartto's Nextjs Demo</title>
      <meta name="viewport" content="initial-scale=1.0, width=device-width" />
    </Head>
    <p>Hello world!</p>
  </div>
```

那如果有两个 `Head` 组件呢？如下代码所示：
```jsx
import Head from 'next/head'
export default () => (
  <div>
    <Head>
      <title>My page title</title>
      <meta name="viewport" content="initial-scale=1.0, width=device-width" key="viewport" />
    </Head>
    <Head>
      <meta name="viewport" content="initial-scale=1.2, width=device-width" key="viewport" />
    </Head>
    <p>Hello world!</p>
  </div>
)
```
{% alert warning %}
注意：这时候只有第二个 meta 是生效的。
{% endalert %}


#### 八、关于热更新
当我们在做修改的时候，页面自动进行了刷新，`HMR` 已经默认支持了。

#### 九、获取数据
注意：`getInitialProps` 不能使用在子组件中，只能使用在 `pages` 中。
```jsx
import React from 'react'

export default class extends React.Component {
  static async getInitialProps({ req }) {
    const userAgent = req ? req.headers['user-agent'] : navigator.userAgent
    return { userAgent }
  }

  render() {
    return (
      <div>
        Hello World {this.props.userAgent}
      </div>
    )
  }
}
```
Notice that to load data when the page loads, we use getInitialPropswhich is an async static method. It can asynchronously fetch anything that resolves to a JavaScript plain Object, which populates props.

Data returned from getInitialProps is serialized when server rendering, similar to a JSON.stringify. Make sure the returned object from getInitialProps is a plain Object and not using Date, Mapor Set.

For the initial page load, getInitialProps will execute on the server only. getInitialProps will only be executed on the client when navigating to a different route via the Link component or using the routing APIs.

也可以使用在无状态组件中：
```jsx
const Page = ({ stars }) =>
  <div>
    Next stars: {stars}
  </div>

Page.getInitialProps = async ({ req }) => {
  const res = await fetch('https://api.github.com/repos/zeit/next.js')
  const json = await res.json()
  return { stars: json.stargazers_count }
}

export default Page
```

需要注意的是：`getInitalProps` 返回的上下文对象包括如下属性：
* pathname - path section of URL
* query - query string section of URL parsed as an object
* asPath - String of the actual path (including the query) shows in the browser
* req - HTTP request object (server only)
* res - HTTP response object (server only)
* jsonPageRes - Fetch Response object (client only)
* err - Error object if any error is encountered during the rendering

#### 十、页面跳转
- 静态跳转：
```jsx
// pages/index.js
import Link from 'next/link'

export default () =>
  <div>
    <Link href="/about">
      <a>here</a>
    </Link>
    to read more
  </div>
```
- 静态传参
```jsx
// 这样，跳转后就会携带参数：http://localhost:3000/image?name=Jartto
import Link from 'next/link'

export default () =>
  <div>
    <Link href={{ pathname: '/image', query: { name: 'Jartto' } }}>
      <a>here</a>
    </Link>
    to read more
  </div>
```

- 可以带参数 replace
```jsx
// 这样会覆盖之前的路由
import Link from 'next/link'

export default () =>
<div>
  <Link href={{ pathname: '/image', query: { name: 'Jartto' } }} replace>
    <a>here</a>
  </Link>
  to read more
</div>
```

- 支持 `onClick`
```jsx
// 添加 onclick事件，不需要额外提供 a 标签
import Link from 'next/link'

export default () =>
<div>
  Click{' '}
  <Link href={{ pathname: '/image', query: { name: 'Jartto' } }} replace>
    <a> here </a>
  </Link>
  to read more
</div>
```


- 方法跳转
```jsx
import Router from 'next/router'

export default () =>
  <div>
    Click <span onClick={() => Router.push('/about')}>here</span> to read more
  </div>
```
  或者这样：
  ```jsx
  import Router from 'next/router'

  const handler = () =>
    Router.push({
      pathname: '/about',
      query: { name: 'Jartto' }
    })

  export default () =>
    <div>
      Click <span onClick={handler}>here</span> to read more
    </div>
  ```
#### 十一、阻止默认链接滚动
默认链接滚动，也就是我们常说的锚定位。我们可以添加 `scroll` 来控制：
```
<Link scroll={false} href="/?counter=10"><a>Disables scrolling</a></Link>
<Link href="/?counter=10"><a>Changes with scrolling to top</a></Link>
```

{% alert warning %}
注意：我们设置了 scroll={false} 来阻止默认链接滚动。
{% endalert %}

#### 十二、总结
到这里，基础知识已经都讲完了。接下来，我们来增加一下难度，欢迎关注后两篇文章：
- [Next.js 使用指南2-路由与加载](http://jartto.wang/2018/06/01/nextjs-2/)
- [Next.js 使用指南3-高级配置](http://jartto.wang/2018/06/08/nextjs-3/)

最近搞了一个 [F2E-Awesome](https://github.com/f2e-awesome/knowledge)，主要收集前端需要掌握的知识图谱。对前端发展感兴趣的童鞋，可以了解一下。



