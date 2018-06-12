---
title: 构建你的 Dva2
date: 2018-05-24 08:58:47
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/dva2.png
thumbnailImagePosition: left
tags: 
- dva2
comments: false
metaAlignment: center
categories: 技术博文
---
一直是 Ant Design 的深度用户，手头有新项目，都会毫不犹豫的选择 Dva＋Roadhog＋Ant Design 。让我眼前一亮的是，Dva 有升级版本了。
<!-- more -->
#### 一、先来看 dva2
2.0 最主要的变化是提取了 `dva-core`，是仅封装了 `redux` 和 `redux-saga` 的纯数据流方案。这使得 `dva` 可以应用在除 `react` 之外的其他领域，比如 `RN`、小程序、游戏、`vue` 等领域；

同时也可满足同一领域的多种实现，比如为 `react` 应用不同的路由方案的 `dva-react-router-3` 和 `dva-no-router`。

{% alert info %}
如上，我们可以理解 dva2 做了抽离，优化，以及部分 bug 修复。
{% endalert %}

#### 二、有哪些改进？
对于新的版本，我们可能会比较关心有哪些改进。电影可能会越拍越烂，框架则不同，每一次更新都会有所改进。那么 `dva2` 具体有哪些优化和改进呢？

1.为了方便在视图层 `dispatch action` 并处理回调， `dispatch` 里 `effect` 类型的 `action` 可以返回 `Promise` 了。
```
dispatch({ type: 'count/addAsync' })
  .then(() => {
    console.log('jartto:done');
  });
```

2.新增 `dva/dynamic` 接口，配合 `react-router@4` 处理组件的按需加载。
```
const Users = dynamic({
  app,
  models: () => [
    import('./models/users'),
  ],
  component: () => import('./routes/Users'),
});

// render
<Route exact path="/users:jartto" component={Users} />
```

3.`take` 自动补全 `namespace` 前缀
{% alert warning %}
注：之前手动加 namespace 的会收到一个 warning。
{% endalert %}
```
{
  namespace: 'count',
  effects: {
    *a(action, { take }) {
      // Before
      yield take('count/b');

      // After
      yield take('b');
    }
  }
}
```

4.`effect` 前后会额外触发 `/@@start` 和 `/@@end` 的 `action`，可利用此约定实现 `put` 的同步执行。
```
yield put({ type: 'addDelay', payload: { amount: 2 } });
yield take('addDelay/@@end');
const count = yield select(state => state.count);
yield put({ type: 'addDelay', payload: { amount: count, delay: 0 } });
```

更多 dva2 的细节，请看[作者介绍](https://github.com/sorrycc/blog/issues/48)。

#### 三、使用了 umi，umi-plugin-dva
`umi` 是什么鬼，看到它，我的内心是拒绝的。但在使用后，被它的简单粗暴所折服，所以决定花点时间学习一下。

`umi` 是 `next.js` 风格的框架，所以沿用了 `pages` 下的文件即路由的方式，这种方式简单直观，而且省去了额外的一份配置，是默认的推荐方式。

这里就不细节深入了，预知详情可查看我的后续文章 [UmiJs 尝鲜](http://jartto.wang/2018/05/24/taste-of-umi/)。

#### 四、检测环境
- node 8.4 或以上
- cnpm 或 yarn

安装 `cnpm`:
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
查看版本：
```
cnpm -v
```

#### 五、安装 dva-cli
```
npm i dva-cli@next -g
```

查看版本号：
```
dva -v
dva-cli version 1.0.0-bea.2
```

#### 六、创建应用
```
dva new jarttoDemo
cd jarttoDemo
```

#### 七、生成 users 路由
新建 `src/pages/users/page.js`，内容如下：
```js
export default () => {
  return (
    <div>
      Users Page
    </div>
  )
}
```

#### 八、构造 users model 和 service
新增 `src/pages/users/models/users.js`，内容如下：
```js
import * as usersService from '../services/users';

export default {
  namespace: 'users',
  state: {
    list: [],
    total: null,
  },
  reducers: {
    save(state, { payload: { data: list, total } }) {
      return { ...state, list, total };
    },
  },
  effects: {
    *fetch({ payload: { page } }, { call, put }) {
      const { data, headers } = yield call(usersService.fetch, { page });
      yield put({ type: 'save', payload: { data, total: headers['x-total-count'] } });
    },
  },
  subscriptions: {
    setup({ dispatch, history }) {
      return history.listen(({ pathname, query }) => {
        if (pathname === '/users') {
          dispatch({ type: 'fetch', payload: query });
        }
      });
    },
  },
};
```
新增 `src/pages/users/services/users.js`：
```js
import request from '../../../utils/request';

export function fetch({ page = 1 }) {
  return request(`/api/users?_page=${page}&_limit=5`);
}
```
由于我们需要从 `response headers` 中获取 `total users` 数量，所以需要改造下 `src/utils/request.js`：
```js
import fetch from 'dva/fetch';

function checkStatus(response) {
  if (response.status >= 200 && response.status < 300) {
    return response;
  }

  const error = new Error(response.statusText);
  error.response = response;
  throw error;
}

/**
 * Requests a URL, returning a promise.
 *
 * @param  {string} url       The URL we want to request
 * @param  {object} [options] The options we want to pass to "fetch"
 * @return {object}           An object containing either "data" or "err"
 */
export default async function request(url, options) {
  const response = await fetch(url, options);

  checkStatus(response);

  const data = await response.json();

  const ret = {
    data,
    headers: {},
  };

  if (response.headers.get('x-total-count')) {
    ret.headers['x-total-count'] = response.headers.get('x-total-count');
  }

  return ret;
}
```

#### 九、添加 layout
添加 `layout` 布局，使得我们可以在首页和用户列表页之间来回切换。`umi` 里约定 `layouts/index.js` 为全局路由，所以我们新增 `src/layouts/index.js` 和 `CSS` 文件即可。

#### 十、处理 loading 状态
dva 有一个管理 effects 执行的 hook，并基于此封装了 dva-loading 插件。通过这个插件，我们可以不必一遍遍地写 showLoading 和 hideLoading，当发起请求时，插件会自动设置数据里的 loading 状态为 true 或 false 。然后我们在渲染 components 时绑定并根据这个数据进行渲染。

umi-plugin-dva 默认内置了 dva-loading 插件。

然后在 src/components/Users/Users.js 里绑定 loading 数据：
```js
function mapStateToProps(state) {
   const { list, total, page } = state.users;
   return {
      list,
      total,
      page,
      loading: state.loading.models.users,
   }
}
```

#### 十一、总结
我们可以照着作者的教程一步步去实践，其实 `dva2` 对开发者还是挺友好的。我们不需要太多额外处理，唯一需要注意的是 `umi`，改变了路由的配置方式。

关于 `umi` ，请看下文：[UmiJs 尝鲜](http://jartto.wang/2018/05/24/taste-of-umi/)。