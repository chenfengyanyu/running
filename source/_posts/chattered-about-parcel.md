---
title: 聊聊 Parcel
date: 2017-12-11 9:53:23
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/parcel2.png
thumbnailImagePosition: left
tags: 
- parcel
- webpack
comments: false
metaAlignment: center
categories: 技术博文 
---
无意间发现了 `Parcel` ，当时 `Github` 上只有不到 2000 星 。几天后，当我重新打开 `Parcel` 地址时，已经 6000+ 星了。是时候学习了，落后注定挨打。
<!-- more -->
#### 一、这是什么？
{% alert info %}
Parcel：极速零配置 Web 应用打包工具。
{% endalert %}

#### 二、能做什么？
1.极速打包时间
- `Parcel` 使用 `worker` 进程去启用多核编译。同时有文件系统缓存，即使在重启构建后也能快速再编译。

2.将你所有的资源打包
- `Parcel` 具备开箱即用的对 `JS, CSS, HTML`, 文件 及更多的支持，而且不需要插件。

3.自动转换
- 如若有需要，`Babel`, `PostCSS`, 和 `PostHTML` 甚至 `node_modules` 包会被用于自动转换代码。

4.零配置代码分拆
- 使用动态 `import()` 语法, `Parcel` 将你的输出文件束(`bundles`)分拆，因此你只需要在初次加载时加载你所需要的代码。

5.热模块替换
- `Parcel` 无需配置，在开发环境的时候会自动在浏览器内随着你的代码更改而去更新模块。

6.友好的错误日志
- 当遇到错误时，`Parcel` 会输出 语法高亮的代码片段，帮助你定位问题。

#### 三、如何使用？
安装 `Parcel`：
```bash
npm install -g parcel-bundler
```
接着我们创建几个简单的项目文件，具体步骤如下：
index.html
```html
<html>
<body>
  <script src="./index.js"></script>
</body>
</html>
```
index.js
```js
// 引入另一个组件
import main from './main';

main();
```
main.js
```js
// 引入一个 CSS 模块
import classes from './main.css';

export default () => {
  console.log(classes.main);
};
```
main.css
```css
.main {
  /* 引用一张图片 */
  background: url('./images/background.png');
  color: red;
}
```
只需要运行 `parcel index.html` 去启动一个开发服务器。引入 `JS`, `CSS`, `images`, 和更多的资源，然后便大功告成！ 

这时候，我们可以看到目录结构如下：
```bash
.
├── .cache
├── dist
│   ├── 57ddef82b59606d39132f926ebbc9b62.png
│   ├── parcel.css
│   └── parcel.js
├── images
│   └── parcel.png
├── index.html
├── index.js
├── main.css
├── main.js
└── package.json
```
{% alert info %}
`.cache` 文件夹就是文件系统缓存，可以保存每一个文件的编译结果，以便后续能够更快的启动。
{% endalert %}

而 `dist` 目录就是我们构建完成的文件，是不是很简单呢，赶快自己试试吧！

#### 四、Parcel 是如何工作的？
`Parcel` 将 资源 树转换成 文件束 树。许多其它的打包工具基本上是基于 `JS` 资源，还有附加在其上的其它格式的资源。例如，在 `JS` 文件中内联成字符串。 `Parcel` 是对文件类型无感知的，它能按你所期待的方式那样与任意类型的资源工作，且毋须配置。`Parcel` 的打包流程共有三个步骤。

1.构建资源树
`Parcel` 接受单个入口资源作为输入，可以是任意类型：一个 `JS` 文件，`HTML`， `CSS`，图片等等。有许多不同的资源类型在 `Parcel` 中被定义，它知道如何去处理特定的文件类型。资源会被解析，资源的依赖会被提取，资源会被转换成最终编译好的形态。此过程创建了一个资源树。

2.构建文件束树
一旦资源树被构建好，资源会被放置在文件束树中。首先一个入口资源会被创建成一个文件束，然后动态的 `import()` 会被创建成子文件束 ，这引发了代码的拆分。

当不同类型的文件资源被引入，兄弟文件束就会被创建。例如你在 `JS` 中引入了 `CSS` 文件，那它会被放置在一个与 `JS` 文件对应的兄弟文件束中。

如果资源被多于一个文件束引用，它会被提升到文件束树中最近的公共祖先中，这样该资源就不会被多次打包。

3.打包
在文件束树被构建之后，每个文件束都会被 `packager` 写到一个特定文件类型的文件中。`packagers` 知道如何从每个资源中将代码合并起来，生成到最终被浏览器加载的文件中。

#### 五、对比传统打包工具有何优势？
1.`Parcel` 拥有更少的配置；
2.监听文件变更能够帮助重新构建，但初始的启动仍然非常慢。`Parcel` 利用工作线程编译你的代码，利用现代的多核处理器能力。这导致了初始构建的速度大大提升。
3.`Parcel` 还具有一个文件系统缓存，可以保存每一个文件的编译结果，以便后续能够更快的启动。
4.`Parcel` 的转换工作在 `AST` 上，因此每个文件只有一个解析，多个转换以及一个代码生成。

#### 六、美中不足
1.零配置
`Parcel` 本身是零配置的，但 `HTML`、`JS` 和 `CSS` 分别是通过 `posthtml`、`babel` 和 `postcss` 处理的，所以我们得分别配 `.posthtmlrc`、`.babelrc` 和 `.postcssrc`。
2.`CSS` 打包路径问题。
3.`async function bundle(main, command) {-SyntaxError: Unexpected token function`
出现该异常问题，你需要将 `node` 版本升级到 `8.x` 以上版本，我本地用的 `n` 来管理 `node` 版本：
```bash
n stable
```
4.`build` 之后 `dist` 目录不会清除，当你切换不同打包文件的时候，容易出现冗余脏文件。
5.`Parcel` 还很年轻，所以你可能会碰到一些麻烦的问题，所以切换项目请慎用。

#### 七、相关文档
[Github 地址](https://github.com/parcel-bundler/parcel)
[Parcel 官方文档](https://parceljs.org/how_it_works.html)
[说说 ParcelJS](https://juejin.im/post/5a288a4951882531d8284c97)

