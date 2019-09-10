---
title: 浏览器存储之争
date: 2018-12-02 22:06:53
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/indexeddb/logo.png
thumbnailImagePosition: left
tags: 
- html5
- indexeddb
comments: false
metaAlignment: center
categories: 技术博文
---
数据存储一直是前端的软肋，从 4KB 左右的 Cookie 到最多 10MB 的 Storage，存储之争从未停止。然而有些问题还是不能解决，这时候我们遇见了她：IndexedDB。
<!-- more -->
#### 一、前端存储困境
{% alert warning %}
随着业务场景的深入，前端对数据存储有了更高的要求，更多的问题也逐渐暴露出来。
{% endalert %}
1.存储空间小
2.无法搜索数据
3.大量数据如何存储
4.无法提供事务支持

#### 二、目前存储方式
1.`Cookie`
- `Cookie` 的大小不超过4KB，且每次请求都会发送回服务器，占用额外的流量；
- 此外，`Cookies` 只能是字符串；
- 浏览器里存储 `Cookies` 的空间有限，很多用户禁止浏览器使用 `Cookies`；
- `Safari` 中 `Cookie` 不能存储中文字符；

{% alert info %}
所以，Cookies 只能用来存储小量的非关键的数据。
{% endalert %}


2.`Storage`
- `LocalStorage` 在 `2.5MB` 到 `10MB` 之间（各家浏览器不同）；
- 不提供搜索功能；
- 不能建立自定义的索引；
- `LocalStorage` 是用 `key-value` 键值模式存储数据，它存储的数据都是字符串形式；
- `Localstorage` 就是专门为小数量数据设计的，它的 `api` 是同步的；

3.[`WebSQL`](https://www.w3.org/TR/webdatabase/)
`WebSQL` 也是一种在浏览器里存储数据的技术，跟 `IndexedDB` 不同的是，`IndexedDB` 更像是一个 `NoSQL` 数据库，而 `WebSQL` 更像是关系型数据库，使用 `SQL` 查询数据。

```
This document was on the W3C Recommendation track but specification work has stopped. The specification reached an impasse: all interested implementors have used the same SQL backend (Sqlite), but we need multiple independent implementations to proceed along a standardisation path.
```

{% alert danger %}
`W3C` 已经不再支持这种技术。
{% endalert %}

鉴于以上问题，一种新的存储手段是迫切需要的，以此解决现有存储的不足之处。

#### 三、如何解决？
那么既然抛出了这么多问题，我们有没有什么处理的手段呢？

{% alert success %}
`IndexedDB` 提供了一个解决方案。
{% endalert %}

`IndexedDB` 是一种浏览器端文档数据库，可以被网页脚本程序创建和操作。它允许储存大量数据，并且提供查询接口，且可以建立索引。

`IndexedDB` 是一种低级 `API` ，用于客户端存储大量结构化数据(包括, 文件/ `blobs`)。该 `API` 使用索引来实现对该数据的高性能搜索。虽然 `Web Storage` 对于存储较少量的数据很有用，但对于存储更大量的结构化数据来说，这种方法不太有用。

`IndexedDB` 不属于关系型数据库（不支持 `SQL` 查询语句），更接近 `NoSQL` 数据库。

#### 四、兼容性
{% alert success %}
在对某个技术进行尝试前，我们可能最关心的是他的兼容性。
{% endalert %}

![hack](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/indexeddb/hack.png)

上图可以看出，几乎所有浏览器都对 `indexedDB` 有了不错的支持，所以可以放心大胆的去在你的项目中使用。

#### 五、indexedDB 特点
1.键值对储存
`IndexedDB` 内部采用对象仓库 `object store` 存放数据。所有类型的数据都可以直接存入，包括 `JavaScript` 对象。对象仓库中，数据以「键值对」的形式保存，每一个数据记录都有对应的主键。

{% alert info %}
主键是独一无二的，不能有重复，否则会抛出一个错误。
{% endalert %}

2.属于异步操作
`IndexedDB` 操作时不会锁死浏览器，用户依然可以进行其他操作，这与 `LocalStorage` 形成对比，因为后者是同步操作。

{% alert info %}
异步设计是为了防止大量数据的读写，拖慢网页的表现。
{% endalert %}

3.支持事务
`IndexedDB` 支持事务 `transaction`，这意味着一系列操作步骤之中，只要有一步失败，整个事务就都取消，数据库回滚到事务发生之前的状态，不存在只改写一部分数据的情况。

4.同源限制
`IndexedDB` 受到同源限制，每一个数据库对应创建它的域名。

{% alert info %}
网页只能访问自身域名下的数据库，而不能访问跨域的数据库。
{% endalert %}

5.储存空间大
`IndexedDB` 的储存空间比 `LocalStorage` 大得多，一般来说不少于 `250MB`，甚至没有上限。

6.支持二进制储存
`IndexedDB` 不仅可以储存字符串，还可以储存二进制数据 `ArrayBuffer` 对象和 `Blob` 对象。


#### 六、应用场景
当一个技术出现在你眼前，可能大家会有同样的疑问：
{% alert warning %}
他的应用场景是什么，是否适合我的应用程序？
{% endalert %}
1.你的用户通过浏览器访问您的应用程序（浏览器）支持 `IndexedDB API` 吗 ?
2.你需要存储大量的数据在客户端？
3.你需要在一个大型的数据集合中快速定位单个数据点吗？
4.你的架构在客户端需要事务支持吗？

如果你有上面几条提到的需求，`IndexedDB` 可能是你很好的选择。

#### 七、快速开始
1.使用前，需要进行必要的判空处理
```js
if(!window.indexedDB)
{
  console.log('你的浏览器不支持 IndexedDB ');
}
```
2.我们来创建一个请求打开 `IndexedDB` 
```js
let request = window.indexedDB.open('jarttoDB', 2);
```
第一个参数是数据库的名称，第二个参数是数据库的版本号。版本号可以在升级数据库时用来调整数据库结构和数据。当我们增加数据库版本号时，会触发 `onupgradeneeded` 事件，这时可能会出现成功、失败和阻止事件三种情况。
```js
let db;
request.onerror = (event) => {
  console.log('Error', event);
}
request.onupgradeneeded = (event) => {
    console.log('Upgrading');
    db = event.target.result;
    let objectStore = db.createObjectStore('blogs', { keyPath : 'rollNo' });
};
request.onsuccess = (event) => {
    db = event.target.result;
    console.log('Success', db);
}
```
`onupgradeneeded` 事件在第一次打开页面初始化数据库时会被调用，或在当有版本号变化时。所以，我们应该在`onupgradeneeded` 函数里创建存储数据。

如果没有版本号变化，而且页面之前被打开过，这时候就会获得一个 `onsuccess` 事件。如果有错误发生时则触发 `onerror` 事件；如果你之前没有关闭连接，则会触发 `onblocked` 事件;

{% alert info %}
大致套路如此，下面我们来一个完整的数据操作演练一下。
{% endalert %}

#### 八、CRUD
1.增：为了往数据库里新增数据，我们首先需要创建一个事务，并要求具有读写权限。在 `indexedDB` 里任何的存取对象的操作都需要放在事务里执行。
```js
let transaction = db.transaction(['blogs'],'readwrite');
transaction.oncomplete = (event) => {
  console.log('Success');
};

transaction.onerror = (event) => {
  console.log('Error');
};

let objectStore = transaction.objectStore('blogs');
objectStore.add({rollNo: rollNo, name: name});
```

2.删：删除跟新增一样，需要创建事务，然后调用删除接口，通过 `key` 删除对象。
```js
db.transaction(['blogs'],'readwrite').objectStore('blogs').delete(rollNo);
```

3.改：为了更新一个对象，首先要把它取出来，修改，然后再放回去。
```js
let transaction = db.transaction(['blogs'],'readwrite');
let objectStore = transaction.objectStore('blogs');
let request = objectStore.get(rollNo);

request.onsuccess = (event) => {
  console.log(`Updating : ${request.result.name}`);
  request.result.name = name;
  objectStore.put(request.result);
};
```

4.查：往 `get()` 方法里传入对象的 `key` 值，取出相应的对象。
```js
let request = db.transaction(['blogs'],'readwrite').objectStore('blogs').get(rollNo);
request.onsuccess = (event) => {
  console.log(`Name : ${request.result.name}`);
};
```

#### 九、总结
我们从浏览器的储存说起，引出了前端存储的一系列问题。之后通过简单的 `IndexedDB` 介绍，以及实际的 `CRUD` 操作来全面了解 `IndexedDB`。当然，这些还只是皮毛，我们还有很长的路要走。

本篇文章主要起到入门学习作用，如果你只想简单了解一下，那么看到这里就可以了。如果你想要深入学习，那么请移步我的下一篇文章：[深入学习 IndexedDB](#)。

#### 十、参考：
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API)
[浏览器数据库 IndexedDB 入门教程](http://www.ruanyifeng.com/blog/2018/07/indexeddb.html)
[IndexedDB: 浏览器里内置的数据库简介](https://blog.csdn.net/inter_peng/article/details/49133081)
[前端的数据库](http://web.jobbole.com/81793/)
[IndexedDB:浏览器里内置的数据库](http://www.webhek.com/post/indexeddb.html)