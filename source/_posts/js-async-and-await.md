---
title: 掌握 Async/Await 
date: 2017-10-06 23:42:36
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/async0.png
thumbnailImagePosition: left
tags: 
- es7
- async
- await
comments: false
metaAlignment: center
categories: 技术博文 
---
前端工程师肯定都经历过 JS 回调链狱的痛苦过程，我们在使用 Promise 的时候总是不尽人意。这时候 Async/Await 应运而生，它到底有什么魔力，我们来说道说道。
<!-- more -->
#### 一、回顾 Promise
{% alert info %}
所谓 Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。
{% endalert %}
1.语法
```js
new Promise(executor);
new Promise(function(resolve, reject) { ... });
```
2.参数
带有 resolve 、reject 两个参数的一个函数。这个函数在创建 Promise 对象的时候会立即得到执行（在 Promise 构造函数返回 Promise 对象之前就会被执行），并把成功回调函数（resolve）和失败回调函数（reject）作为参数传递进来。调用成功回调函数（resolve）和失败回调函数（reject）会分别触发 Promise 的成功或失败。

这个函数通常被用来执行一些异步操作，操作完成以后可以选择调用成功回调函数（resolve）来触发 Promise 的成功状态，或者，在出现错误的时候调用失败回调函数（reject）来触发 Promise 的失败。

3.Promise.all
用 `Promise.all` 来执行，`all` 接收一个数组参数，里面的值最终都算返回 `Promise` 对象。这样，三个异步操作的并行执行的，等到它们都执行完后才会进到 `then` 里面。
```js
Promise.all([async1(), async2(), async3()])
.then(function(results){
  console.log(results);
});
```
{% alert info %}
`all` 会把所有异步操作的结果放进一个数组中传给 `then`，就是上面的 `results`。
{% endalert %}

4.race 的用法
`all` 方法的效果实际上是「谁跑的慢，以谁为准执行回调」，那么相对的就有另一个方法「谁跑的快，以谁为准执行回调」，这就是 `race`方法：
```js
Promise.race([requestImg(), timeout()])
.then(function(results){
    console.log(results);
})
.catch(function(reason){
    console.log(reason);
});
```
上述代码演示了 `race` 的基本用法，实现的功能是：请求图片，如果请求成功就返回图片，否则就调用超时函数。

更多资源，请查看：
- [js promise](http://www.jianshu.com/p/063f7e490e9a)
- [白话 promise](http://www.cnblogs.com/lvdabao/p/es6-promise-1.html)
- [promise 对象-阮一峰](http://javascript.ruanyifeng.com/advanced/promise.html)
- [promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

#### 二、Promise 为何不完美？
乍一看，`Promise` 还不错，帮我们解决了回调链狱的问题。当然这只是简单使用，碰到复杂的业务也有很鸡肋的场景，比如：

1.错误处理
在下面的 `Promise` 示例中，`Try/Catch` 不能处理 `JSON.parse` 的错误，因为它在 `Promise` 中。我们需要使用 `catch`，这样错误处理代码非常冗余。并且，在我们的实际生产代码会更加复杂。
```js
const makeRequest = () => {
  try {
    getJSON().then(result => {
      // JSON.parse可能会出错
      const data = JSON.parse(result)
      console.log(data)
    })
    // 取消注释，处理异步代码的错误
    // .catch((err) => {
    //   console.log(err)
    // })
  } catch (err) {
    console.log(err)
  }
}
```
`Async/Await` 让 `Try/Catch` 可以同时处理同步和异步错误。使用 `Async/Await` 的话，`Catch` 能处理 `JSON.parse` 错误:
```js
const makeRequest = async () => {
  try {
    // this parse may fail
    const data = JSON.parse(await getJSON())
    console.log(data)
  } catch (err) {
    console.log(err)
  }
}
```
{% alert success %}
`Async/Await` 最让人舒服的一点是代码看起来是同步的。
{% endalert %}

2.条件语句
下面示例中，需要获取数据，然后根据返回数据决定是直接返回，还是继续获取更多的数据。
```js
const makeRequest = () => {
  return getJSON()
    .then(data => {
      if (data.needsAnotherRequest) {
        return makeAnotherRequest(data)
          .then(moreData => {
            console.log(moreData)
            return moreData
          })
      } else {
        console.log(data)
        return data
      }
    })
}
```
这些代码看着就头痛。嵌套（6层），括号，`return` 语句很容易让人感到迷茫，而它们只是需要将最终结果传递到最外层的`Promise`。如果换成 `Async/Await` 呢：
```js
const makeRequest = async () => {
  const data = await getJSON();
  if (data.needsAnotherRequest) {
    const moreData = await makeAnotherRequest(data);
    console.log(moreData);
    return moreData;
  } else {
    console.log(data);
    return data;  
  }
}
```
所以，这才是真正摆脱回调链狱的正确做法。

3.中间值
你很可能遇到过这样的场景，调用 `promise1`，使用 `promise1` 返回的结果去调用 `promise2`，然后使用两者的结果去调用`promise3`。你的代码很可能是这样的：
```js
const makeRequest = () => {
  return promise1()
    .then(value1 => {
      return promise2(value1);
        .then(value2 => {        
          return promise3(value1, value2);
        })
    })
}
// 或者：
const makeRequest = () => {
  return promise1()
    .then(value1 => {
      return Promise.all([value1, promise2(value1)])
    })
    .then(([value1, value2]) => {      
      return promise3(value1, value2)
    })
}
```
怎么写都会觉得很复杂，那如果 `Async/Await` 用来实现呢，表现可能如下：
```js
const makeRequest = async () => {
  const value1 = await promise1();
  const value2 = await promise2(value1);
  return promise3(value1, value2);
}
```
是不是很 6 ，将复杂的场景简化，这样的代码就很有灵性了。

4.错误栈
调用了多个 `Promise`，假设 `Promise` 链中某个地方抛出了一个错误，`Promise` 链中返回的错误栈没有给出错误发生位置的线索。更糟糕的是，它会误导我们；错误栈中唯一的函数名为 `callAPromise`，然而它和错误没有关系。(文件名和行号还是有用的)。
```js
const makeRequest = () => {
  return callAPromise()
    .then(() => callAPromise())
    .then(() => callAPromise())
    .then(() => callAPromise())
    .then(() => callAPromise())
    .then(() => {
      throw new Error("oops");
    })
}

makeRequest().catch(err => {
  console.log(err);
  // output
  // Error: oops at callAPromise.then.then.then.then.then (index.js:8:13)
})
```
然而，`Async/Await` 中的错误栈会指向错误所在的函数：
```js
const makeRequest = async () => {
  await callAPromise();
  await callAPromise();
  await callAPromise();
  throw new Error("oops");
}

makeRequest().catch(err => {
  console.log(err);
  // output
  // Error: oops at makeRequest (index.js:7:9)
})
```

5.调试
调试 `Promise` 有两个问题：
- 不能在返回表达式的箭头函数中设置断点；
- 如果你在 `then` 代码块中设置断点，调试器不会跳到下一个 `then`，因为它只会跳过异步代码;

而使用 `Await/Async` 时，你不再需要那么多箭头函数，这样你就可以像调试同步代码一样跳过 `Await` 语句。

这里只简单的列出问题，详细请查看原文：[Async/Await 替代 Promise 的 6 个理由](http://cnodejs.org/topic/58e4914e43ee7e7106c13541)

#### 三、新时代的曙光 Async/Await
简单介绍：
- Await/Async 是写异步代码的新方式，以前的方法有回调函数和 Promise。
- Await/Async 是基于 Promise 实现的，它不能用于普通的回调函数。
- Await/Async 与 Promise 一样，是非阻塞的。
- Await/Async 使得异步代码看起来像同步代码，这正是它的魔力所在。

使用 `Promise` 是这样的:
```js
const jarttoDemo = () =>
getJSON().then(data => {
  return data;
})

jarttoDemo();
```

使用 `Async/Await` 是这样的:
```js
const jarttoDemo = async () => {
  let data = await getJSON();
  return data;
}

jarttoDemo();
```

基本规则：
- Async 表示这是一个 Async 函数，Await 只能用在这个函数里面。
- Await 表示在这里等待 Promise返回结果了，再继续执行。
- Await 后面跟着的应该是一个 Promise 对象，当然，其他返回值也没关系，只是会立即执行，不过那样就没有意义了。

#### 四、更多用法示例
1.简单示例
```js
var sleep = function (time) {
  return new Promise(function (resolve, reject) {
    setTimeout(function () {
        resolve();
    }, time);
  })
};

var start = async function () {
    // 在这里使用起来就像同步代码那样直观
    console.log('start');
    await sleep(3000);
    console.log('end');
};

start();
```
2.获得返回值
```js
var sleep = function (time) {
  return new Promise(function (resolve, reject) {
    setTimeout(function () {
        // 返回 ‘ok’
        resolve('ok');
    }, time);
  })
};

var start = async function () {
    let result = await sleep(3000);
    console.log(result); // 收到 ‘ok’
};
```
3.错误捕获
```js
const makeRequest = async () => {
  try {
    // this parse may fail
    const data = JSON.parse(await getJSON())
    console.log(data)
  } catch (err) {
    console.log(err)
  }
}
```
既然 `then` 不用写了，那么 `catch` 也不用写，可以直接用标准的 `try catch` 语法捕捉错误。
```js
var sleep = function (time) {
  return new Promise(function (resolve, reject) {
    setTimeout(function () {
      // 模拟出错了，返回 ‘error’
      reject('error');
    }, time);
  })
};

var start = async function () {
  try {
    console.log('start');
    await sleep(3000); // 这里得到了一个返回错误
    
    // 所以以下代码不会被执行了
    console.log('end');
  } catch (err) {
    console.log(err); // 这里捕捉到错误 `error`
  }
};
```
4.条件语句
`Promise` 写法：
```js
const makeRequest = () => {
  return getJSON()
    .then(data => {
      if (data.needsAnotherRequest) {
        return makeAnotherRequest(data)
          .then(moreData => {
            return moreData;
          })
      } else {
        return data;
      }
    })
}
```
`Async/Await` 写法：
```js
const makeRequest = async () => {
  const data = await getJSON();
  if (data.needsAnotherRequest) {
    const moreData = await makeAnotherRequest(data);
    return moreData;
  } else {
    return data;  
  }
}
```
5.循环多个 `Await`
```js
var start = async function () {
  for (let i = 1; i <= 10; i++) {
    console.log(`当前是第 ${i} 次等待..`);
    await sleep(1000);
  }
};
```
{% alert success %}
需要注意的是，`Await` 必须在 `Async` 函数的上下文中的。
{% endalert %}

6.在 `forEach` 中使用
```js
async function printFiles () {
  const files = await getFilePaths();

  for (let file of files) {
    const contents = await fs.readFile(file, 'utf8');
    console.log(contents);
  }
}

async function printFiles () {
  const files = await getFilePaths();

  await Promise.all(files.map(async (file) => {
    const contents = await fs.readFile(file, 'utf8')
    console.log(contents)
  }));
}
```

示例参考如下文章：
[Async/Await替代Promise的6个理由](http://cnodejs.org/topic/58e4914e43ee7e7106c13541)
[ES7 的 Async/Await](http://cnodejs.org/topic/5640b80d3a6aa72c5e0030b6)
[Using async/await with a forEach loop](https://stackoverflow.com/questions/37576685/using-async-await-with-a-foreach-loop)

#### 五、总结
我们一直在强调代码的可读性和可维护性，对我来说，Async/Await 更加易懂和易用。所以，不管是 Promise 还是 Async/Await ，能解决实际问题的技术就是好技术。

当然，Async/Await 也是基于 Promise 概念的，技术上我们也可以求同存异，不必太过较真。一句话，选择权在你！
