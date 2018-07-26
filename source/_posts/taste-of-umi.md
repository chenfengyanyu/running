---
title: UmiJs 尝鲜
date: 2018-05-24 09:00:23
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/umijs.png
thumbnailImagePosition: left
tags: 
- umi
- umijs
comments: false
metaAlignment: center
categories: 技术博文
---
前文[构建你的 Dva2](http://jartto.wang/2018/05/24/build-your-dva2/) 中提到了 UmiJs，但并未深入讨论。这节开个专题，主要探讨 UmiJs 如何使用、注意事项、以及问题处理。
<!-- more -->
#### 一、是什么？
`umi` 是 `next.js` 风格的框架，所以沿用了 `pages` 下的文件即路由的方式，这种方式简单直观，而且省去了额外的一份配置，是默认的推荐方式。

另外，我们考虑到有些复杂的场景下约定式路由无法满足，或者就是有人偏爱配置的方式，所以依然提供了配置式的路由。

{% alert info %}
注意：约定式路由和配置式路由是二选一的，并不是 `merge` 的关系，目录下有 `_routes.json` 时会优先用配置式。
{% endalert %}

#### 二、umi 定位
根据作者的描述：umi[工具 + 路由] + dva[数据] + antd(-mobile)[视图]

当然，我们如果不用 `umi` 的话，工具用 `webpack + webpack-dev-server + babel + postcss + ...` ，路由用 `react-router` 也是可以实现的。

{% alert success %}
咦，有没有惊奇的发现，你少了很多工作？
{% endalert %}

引用官网上的描述：这是上一代的使用方式，工具是工具，库是库，泾渭分明。而近来，我们发现工具和库其实可以糅合在一起，工具也是框架的一部分。通过约定、自动生成和解析代码等方式来辅助开发，减少开发者要写的代码量。

`next.js` 如此，`umi` 也如此，`Compilers are the New Frameworks` 。

#### 三、umi 和 dva 以及 roadhog 是什么关系？
相信大家都比较关心这个问题，我们为什么要使用它？简单来说：
* dva 是数据流
* roadhog 是通用工具
* umi 是框架，包含路由和工具，很多的约定，很多的性能优化，以及开发体验优化

然后，`umi` 和 `roadhog` 共用同一个底层 `af-webpack`，他们在功能上有一定重叠，可以说 `umi` 包含 `roadhog`，`react` 项目可以尝试一下 `umi`。

#### 四、约定大于配置
{% alert success %}
既然提到了约定大于配置，这说明目录的结构需要遵守一些约定，所以你在项目中可能找不到 Router 路由文件。
{% endalert %}

首先，使用 `npm` 或 `yarn` 来全局安装：
```bash
npm i umi -g
# yarn global add umi
```
打印版本号：
```
umi -v
```
{% alert warning %}
`Umi` 暂时没有提供脚手架，之后可能会添加。
{% endalert %}

其次，创建并进入应用：
```
mkdir myapp && cd myapp
```
最后，启动应用：
```
umi dev
```

在 `Umi` 中，需要记住的规则就是：约定大于配置。下面我们来看一个约定的目录：
```js
.
├── dist/                          // 构建产物目录
└── src/                           // 源码目录，可选，把里面的内容直接移到外面即可
    ├── layouts/
    │   └── index.js               // 全局布局
    ├── pages/                     // 页面目录，里面的文件即路由
        ├── .umi/                  // dev 临时目录，需添加到 .gitignore
        ├── .umi-production/       // build 临时目录，会自动删除
        ├── document.ejs           // HTML 模板
        ├── 404.js                 // 404 页面
        ├── page1.js               // 页面 1，任意命名
        └── page2.js               // 页面 2，任意命名
    ├── global.css                 // 约定的全局样式文件，自动引入，也可以用 global.less
    ├── _routes.json               // 路由配置，和文件路由二选一
├── test/                          // 测试用例放这里
├── .umirc.js                      // umi 配置
├── .webpackrc                     // webpack 配置
└── package.json
```
还有几条约定，大致如下：
* 约定 _layout.js 目录下路由的父级组件，可通过 props.children 渲染子路由，或通过 route.routes 定制子路由的渲染
* 有 _layout.js 时才会有嵌套，无 _layout.js 则平级渲染
* 目录下有 page.js 时 _layout.js 无效，因为目录路由没有子路由，无嵌套需求

#### 五、目录路由
有些场景下文件路由无法满足需求，比如我们需要组织 `dva` 的 `models`、`services` 等等，所以 `umi` 支持一个目录作为路由的方式。约定是：目录下如果有 `page.js`，则作为路由解析。

```html
<Route exact path="/" component={require('../index.js').default} />
<Route exact path="/users/detail" component={require('../users/detail.js').default}} />
<Route exact path="/list" component={require('../list/page.js').default}} />
```

{% alert warning %}
注意 /users/detail.js 和 /list/page.js 的区别。
{% endalert %}

#### 六、变量路由
那如果我需要传递一些路由参数呢，这时候需要用到变量路由。`umi` 里约定，文件名或目录名里的 `$` 会被替换为 :。
```html
<Route exact path="/" component={require('../index.js').default} />
<Route exact path="/users/:userId" component={require('../users/$userId.js').default}} />
```

比如以下目录结构：
```
+ pages/
  + $post/
    - index.js
    - comments.js
  + users/
    $id.js
  - index.js
```
会生成路由配置如下：
```
[
  { path: '/': exact: true, component: './pages/index.js' },
  { path: '/users/:id': exact: true, component: './pages/users/$id.js' },
  { path: '/:post/': exact: true, component: './pages/$post/index.js' },
  { path: '/:post/comments': exact: true, component: './pages/$post/comments.js' },
]
```

#### 七、配置式路由
`umi` 里约定可通过 `_routes.json` 配置路由，如果有 `_routes.json`，则会忽略 `pages` 下文件即路由的规则。大致如下：
```
[
  { "path": "/", "exact": true, "component": "./components/a" },
  { "path": "/list", "component": "./pages/b", "meta": { "Route": "./routes/PrivateRoute.js" } },
  { "path": "/users", "component": "./pages/users/_layout",
    "routes": [
      { "path": "/users/detail", "exact": true, "component": "./pages/users/detail" },
      { "path": "/users/:id", "exact": true, "component": "./pages/users/id" }
    ]
  }
]
```
这里需要注意几个问题：
- component 为指向文件的相对路径，而非 React 组件
- 支持通过 routes 实现嵌套路由
- 支持通过 meta.Route 实现权限路由

#### 八、嵌套路由
`umi` 里约定目录下有 `_layout.js` 时会以生成嵌套路由，以 `_layout.js` 为该目录的 `layout` 。比如以下目录结构：
```
+ pages/
  + users/
    - _layout.js
    - $id.js
    - index.js
```
会生成路由配置如下：
```
[
  { path: '/users': exact: false, component: './pages/users/_layout.js'
    routes: [
     { path: '/users/', exact: true, component: './pages/users/index.js' },
     { path: '/users/:id', exact: true, component: './pages/users/$id.js' },
   ],
  },
]
```

{% alert info %}
这个就很清晰了，我们可以单独为某个路由应用样式。
{% endalert %}

#### 九、添加 404 页面
`umi` 中约定 `pages` 目录下的 `404.js` 为 `404` 页面，这个文件需要返回 `React` 组件。比如：
```js
export default () => {
  return (
    <div>Jartto: I am a customized 404 page</div>
  );
};
```
{% alert info %}
开发模式下，umi 会添加一个默认的 404 页面，但你仍然可通过精确地访问 /404 来验证 404 页面。
{% endalert %}

#### 十、面包屑
面包屑在网页开发中起着非常重要的作用，有没有简单省心的用法呢？答案是肯定的，在 umi 中，我们可以这样使用：
```
npm install react-router-breadcrumbs-hoc --save
```
然后实现一个 Breakcrumbs.js，比如：
```jsx
import NavLink from 'umi/navlink';
import withBreadcrumbs from 'react-router-breadcrumbs-hoc';

// 更多配置请移步 https://github.com/icd2k3/react-router-breadcrumbs-hoc
const routes = [
  { path: '/', breadcrumb: '首页' },
  { path: '/list', breadcrumb: 'List Page' },
];

export default withBreadcrumbs(routes)(({ breadcrumbs }) => (
  <div>
    {breadcrumbs.map((breadcrumb, index) => (
      <span key={breadcrumb.key}>
        <NavLink to={breadcrumb.props.match.url}>
          {breadcrumb}
        </NavLink>
        {(index < breadcrumbs.length - 1) && <i> / </i>}
      </span>
    ))}
  </div>
));
```

#### 十一、配置 HTML 模板
当然，你也可以配置你的默认模板，新建 `pages/document.ejs`，`umi` 约定如果这个文件存在，会作为默认模板，内容上可以参考 `umi` 内置模板，需要保证出现：
```html
<div id="root"></div>
```
那如果针对某些特定页面使用模版呢，这时候需要用到配置文件，我们来修改 `.umirc.js`：
```
{
  "pages": {
    "/": { document: "./a.ejs" },
    "/list": { document: "./b.ejs" }
  }
}
```
{% alert info %}
优先级是：pages 里指定 > pages/document.ejs > umi 内置模板。
{% endalert %}

#### 十二、按需加载
出于性能的考虑，很多场景我们都需要模块或组件进行按需加载，通过 `umi/dynamic` 接口实现，比如：
```js
import dynamic from 'umi/dynamic';

const delay = (timeout) => new Promise(resolve => setTimeout(resolve, timeout));
const App = dynamic(async function() {
  await delay(/* 1s */1000);
  return () => <div>I will render after 1s</div>;
});
```
此外，也可以通过 `import()` 实现，比如：
```js
import('g2').then(() => { // do something with g2});
```

#### 十三、部署
使用了 `umi` ，部署也很容易，直接运行：
```
umi build 
```
举一个简单的例子，比如像这样的目录结构：
```
./pages
├── index.css
├── index.js
└── list.js
```
build 之后就变成了：
```
./dist
├── index.html
└── static
    ├── pages__index.5c0f5f51.async.js
    ├── pages__list.f940b099.async.js
    ├── umi.2eaebd79.js
    └── umi.f4cb51da.css
```
{% alert info %}
index.html 会加载 umi.{hash}.js 和 umi.{hash}.css，然后按需加载 index 和 list 两个页面的 JS。
{% endalert %}

如果需要部署到 `CDN`，那么大量的使用按需加载，就需要配置 `publicPath`，找到 `.webpackrc`
```
{
  "publicPath": "http://yourcdn/path/to/static/"
}
```
还有一种更简单的方式，使用环境变量，如下：
```
PUBLIC_PATH=http://jarttocdn/path/to/static/ umi build
```
此外，如果你需要部署到非根目录，这时候可能需要用到 `BASE_URL`
```
BASE_URL=/path/to/yourapp/ umi build
```


#### 十四、如何解决 login 页不使用 layout 问题
umi 里约定 src 目录下的 layouts/index.js 为全局路由，返回一个 React 组件，通过 props.children 渲染子组件。比如：
```js
export default function(props) {
  return (
    <div>
      <Header />
      { props.children }
      <Footer />
    </div>
  );
}
```
那如果我的登录页不需要使用全局 layout ，怎么办？虽然 umi 中并不支持，但是我们仍然可以采取一些手段，参考如下：
```js
export default function(props) {
  if (props.location.path === '/login') {
    return <SimpleLayout>{ props.children }</SimpleLayout>
  }
  
  return (
    <div>
      <Header />
      { props.children }
      <Footer />
    </div>
  );
}
```

#### 十五、环境变量
部署的时候，我们要按照不同的服务器环境去打包，这时候就需要使用环境变量了。我们需要先安装 `cross-env`，
```bash
npm install cross-env --save
```
然后配置 `package.json`：
```json
"build-release": "cross-env HOST=http://test.jartto.com umi build",
"build-test": "cross-env HOST=http://pro.jartto.net umi build",
```
这里我配置了一个测试环境和一个正式环境的 `HOST`。接下来，需要 修改 `.webpackrc.js`：
```json
export default {
"define": { "process.env.HOST": process.env.HOST}
}
```

{% alert warning %}
这里一定要配置，否则环境变量并没有什么卵用。
{% endalert %}

最后，创建自己的配置文件 `config.js`
```js
const SELF = {
  url: process.env.HOST ? process.env.HOST : 'http://127.0.0.1'
};
export default SELF;
```

此外，umi 提供了很多环境变量可以直接使用，具体请参考[环境变量](https://umijs.org/docs/zh-Hans/introduction.html)

#### 十六、路由跳转
有两种使用场景可做参考：
1.静态跳转
```js
import Link from 'umi/link';
export default () => ( <Link to="/list">Go to jartto's page</Link> );
```
2.方法跳转
```js
import router from 'umi/router';

function goToListPage() {
  router.push('/list');
}
```

#### 十七、build 访问路由后 404
几个方案供选择：
* 改用 hashHistory，在 .umirc.js 里配 hashHistory: true
* 静态化，在 .umirc.js 里配 exportStatic: true
* 服务端配置路由 fallback 到 index.html

这些在官方文档中都可以看到，也是开发中最容易碰到的问题。

#### 十八、总结
`umijs` 还是挺让人惊喜的，它让我可以专注去写业务，不需要花费太多的经历在配置上。所以，如果手头有一些比较急的项目，不妨拿来试试。

当我知道它借鉴了 `nextjs` 的思想后，越发想去了解一下 `nextjs`。好吧，趁着热劲，去探索一下 `nextjs` 的神秘世界吧！

#### 十九、相关资源
[官网地址](https://umijs.org/docs/en/introduction.html)
[Github](https://github.com/umijs/umi)