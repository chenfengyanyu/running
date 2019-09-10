---
title: Next.js 使用指南2－路由与加载
date: 2018-06-01 22:13:01
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/nextjs.png
thumbnailImagePosition: left
tags: 
- nextjs
- react
comments: false
metaAlignment: center
categories: 技术博文
---
书接上文 [Next.js 使用指南1-基本规则](http://jartto.wang/2018/05/27/nextjs-1/)，这节我们将过渡到一些高级的功能。涉及到的内容包含：路由、高阶组件、预加载、动态路由等等，快来开始吧！
<!-- more -->
#### 一、路由拦截
```jsx
import Router from 'next/router'

Router.beforePopState(({ url, as, options }) => {
  // I only want to allow these two routes!
  if (as !== "/" || as !== "/other") {
    // Have SSR render bad routes as a 404.
    window.location.href = as
    return false
  }

  return true
});
```
`Router` 有如下的属性和方法，我们可以根据实际情况来使用：
* route - 返回当前路由
* pathname - 返回当前路径包含 query 部分
* query - 返回 query 对象，默认为 {}
* asPath - 浏览器中的实际路径，包含 query 部分
* push(url, as=url) - performs a pushState call with the given url
* replace(url, as=url) - performs a replaceState call with the given url
* beforePopState(cb=function) - intercept popstate before router processes the event.

#### 二、路由事件
为了满足更多的业务场景，我们可能需要监听路由事件，这时候，有一些事件是可以用的：
* onRouteChangeStart(url) - Fires when a route starts to change
* onRouteChangeComplete(url) - Fires when a route changed completely
* onRouteChangeError(err, url) - Fires when there's an error when changing routes
* onBeforeHistoryChange(url) - Fires just before changing the browser's history

```jsx
Router.onRouteChangeStart = url => {
  console.log('App is changing to: ', url)
}
```
如果不需要监听了，请释放它：
```
Router.onRouteChangeStart = null
```
如果路由载入取消，就会触发`routeChangeError`,参数 `err` 的属性 `cancelled` 就被设置成了 `true`
```
Router.onRouteChangeError = (err, url) => {
  if (err.cancelled) {
    console.log(`Route to ${url} was cancelled!`)
  }
}
```

#### 三、浅路由模式
所谓浅路由模式，其实就是不再初始化 `getInitalProps`，但是我们依然可以得到更新的 `pathname` 和 `query`。使用方法如下：
```jsx
// Current URL is "/"
const href = '/?counter=10'
const as = href
Router.push(href, as, { shallow: true })
```
更多的使用场景，可能类似与分页数据，我们只改变 `page` ，而不需要整个初始化。当然，我们可以监听：
```jsx
componentWillReceiveProps(nextProps) {
  const { pathname, query } = nextProps.url
  // fetch data based on the new query
}
```
{% alert danger %}
需要注意的是：浅路由模式只支持相同的 URL，如果页面路由变化，还是会触发 getInitialProps。
{% endalert %}

#### 四、使用高阶组件
```jsx
import { withRouter } from 'next/router'

const ActiveLink = ({ children, router, href }) => {
  const style = {
    marginRight: 10,
    color: router.pathname === href? 'red' : 'black'
  }

  const handleClick = (e) => {
    e.preventDefault()
    router.push(href)
  }

  return (
    <a href={href} onClick={handleClick} style={style}>
      {children}
    </a>
  )
}

export default withRouter(ActiveLink)
```
{% alert info %}
这时候，router 中就会有我们需要的所有参数，使用起来还是蛮方便的。
{% endalert %}

#### 五、预加载页面
通过预取 `Next.js` 只下载 `JS` 代码，当页面开始渲染，可能需要等待数据。
```jsx
import Link from 'next/link'

// example header component
export default () =>
  <nav>
    <ul>
      <li>
        <Link prefetch href="/">
          <a>Home</a>
        </Link>
      </li>
      <li>
        <Link prefetch href="/about">
          <a>About</a>
        </Link>
      </li>
      <li>
        <Link prefetch href="/contact">
          <a>Contact</a>
        </Link>
      </li>
    </ul>
  </nav>
```
当然，这并不是预加载的妙用，真正的作用请看下面的例子：
```jsx
import Router from 'next/router'

export default ({ url }) =>
  <div>
    <a onClick={() => setTimeout(() => url.pushTo('/dynamic'), 100)}>
      A route transition will happen after 100ms
    </a>
    {// but we can prefetch it!
    Router.prefetch('/dynamic')}
  </div>
```
我们即将跳转 `dynamic`，可以进行预加载。

#### 六、禁用默认路由
如果想禁用默认路由，需要添加文件 `next.config.js` ，并增加配置：
```js
// next.config.js
module.exports = {
  useFileSystemPublicRoutes: false
}
```

#### 七、动态前缀
动态修改路径前缀，适合有 `CDN` 配置的静态资源部署：
```js
const next = require('next')
const micro = require('micro')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare().then(() => {
  const server = micro((req, res) => {
    // Add assetPrefix support based on the hostname
    if (req.headers.host === 'my-app.com') {
      app.setAssetPrefix('http://jartto.cdn.com/myapp')
    } else {
      app.setAssetPrefix('')
    }

    handleNextRequests(req, res)
  })

  server.listen(port, (err) => {
    if (err) {
      throw err
    }

    console.log(`> Ready on http://localhost:${port}`)
  })
})
```
#### 八、动态引入
- 简单用法：
```jsx
import dynamic from 'next/dynamic'

const DynamicComponent = dynamic(import(‘./jartto'))

export default () =>
<div>
<DynamicComponent />
<p>HOME PAGE is here!</p>
</div>
```
- 增加自定义组件
```jsx
import dynamic from 'next/dynamic'

const DynamicComponentWithCustomLoading = dynamic(
  import('../components/jartto'),
  {
    loading: () => <p>...</p>
  }
)

export default () =>
  <div>
    <DynamicComponentWithCustomLoading />
    <p>HOME PAGE is here!</p>
  </div>
```
- 禁止服务端渲染
```jsx
import dynamic from 'next/dynamic'

const DynamicComponentWithNoSSR = dynamic(import('./image'), {
ssr: false
})

export default () =>
<div>
<DynamicComponentWithNoSSR />
<p>Hello Jartto!</p>
</div>
```
- 加载多个模块
```jsx
import dynamic from 'next/dynamic'

const HelloBundle = dynamic({
  modules: props => {
    const components = {
      Hello1: import('../components/jartto1'),
      Hello2: import('../components/jartto2')
    }

    // Add remove components based on props

    return components
  },
  render: (props, { Hello1, Hello2 }) =>
    <div>
      <h1>
        {props.title}
      </h1>
      <Hello1 />
      <Hello2 />
    </div>
})

export default () => <HelloBundle title="Dynamic Bundle" />
```
#### 九、自定义<App>
在 `Next.js` 使用 `App` 组件来初始化页面，当然，我们也可以覆写它，从而控制页面的初始化。具体实例如下：
- 在页面变化之间保持布局
- 浏览页面时保持状态
- 自定义异常处理
- 注入附加数据

```jsx
import App, {Container} from 'next/app'
import React from 'react'

export default class MyApp extends App {
  static async getInitialProps ({ Component, router, ctx }) {
    let pageProps = {}

    if (Component.getInitialProps) {
      pageProps = await Component.getInitialProps(ctx)
    }

    return {pageProps}
  }

  render () {
    const {Component, pageProps} = this.props
    return <Container>
      <Component {...pageProps} />
    </Container>
  }
}
```
{% alert warning %}
注意，我们需要创建 ./pages/_app.js，来覆写 App 类。需要重启服务才能生效！
{% endalert %}

#### 十、总结
这节内容主要集中在路由和加载方面，如果我们需要更大的控制权，应该会用到高级配置。请查看：[Next.js 使用指南3-高级配置](http://jartto.wang/2018/06/08/nextjs-3/)。
