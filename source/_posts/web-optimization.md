---
title: 网站优化实战
date: 2019-02-16 09:17:29
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/logo.jpeg
thumbnailImagePosition: left
tags: 
- web
- optimization
comments: false
metaAlignment: center
categories: 面试笔试
---
网站优化是前端开发的重中之重，但是优化细节却十分繁杂。没有好的思路，优化很难高效的开展。本文将以实际网站来做参考，手把手教你如何一步步做好网站优化。
<!-- more -->
这不是一篇 `基础网站优化` 文章，继续下文前，请确定已经做了如下`基本优化`：
1.图片压缩、合并
2.代码精简、混淆
3.减少 `iframe` 使用
4.避免图片 `src` 为空
5.减少 `HTTP` 请求数
6.避免重定向
7.样式表放页头，脚本放底部
...


#### 一、优化的意义
我们可以从两个角度来看这个问题：
1.用户角度
网站优化能够让页面加载得更快，响应更加及时，极大提升用户体验。

2.服务商角度
优化会减少页面资源请求数，减小请求资源所占带宽大小，从而节省可观的带宽资源。

{% alert info %}
网站优化的目标是：减少网站加载时间，提高响应速度。
{% endalert %}

那么网站加载速度和用户体验又有着怎样的关系呢？我们来看下面这张图：
![speed](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/s.png)

`Google` 和亚马逊的研究表明，`Google` 页面加载的时间从 `0.4` 秒提升到 `0.9` 秒导致丢失了 `20%` 流量和广告收入，对于亚马逊，页面加载时间每增加 `100ms` 就意味着 `1%` 的销售额损失。

{% alert success %}
可见，页面的加载速度对于用户有着至关重要的影响。
{% endalert %}

一个好的交互效果可能是这样的：
![good](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/good.png)

#### 二、分析网站性能瓶颈
1.打包文件大小
![webpack](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/webpack.png)

2.打包文件目录
![tree](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/tree.png)

```
.
├── favicon.ico
├── index.html
├── manifest.json
├── static
│   ├── DIN-Medium.1bbe3460.otf
│   ├── DIN-Regular.799221d7.otf
│   └── logo.c57d38d0.png
├── umi.css
├── umi.css.map
├── umi.js
└── umi.js.map
```

{% alert warning %}
需要注意：生产环境不要开启 SOURCEMAP
{% endalert %}

3.静态资源加载时间
![time](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/time.png)

4.资源瀑布：`Waterfall`
![ttfb](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/ttfb.png)

`TTFB` 全称 `Time To First Byte`：是指网络请求被发起到从服务器接收到第一个字节的这段时间，它包含了 `TCP` 连接时间、发送 `HTTP` 请求时间和获得响应消息第一个字节的时间。

{% alert info %}
Content Download：即下载内容所需要的时间。
{% endalert %}

页面一接口情况：
![wrong](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/wrong.png)

页面二接口情况：
![history](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/history.png)

用户下载内容所需要的时间，受限于服务器的资源、资源的大小以及用户的网络速度。因此，我们暂时`不讨论`这方面的内容。

5.分析工具
通过 `webpack` 打包，分析一下大文件构成。
![bundle](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/bundle.png)

6.`YSlow` 或者 `PageSpeed`
我们可以通过 [Google PageSpeed Insights API Extension](https://developers.google.com/speed/pagespeed/insights/) 来对 `网站整体性能` 做一下评估，按照建议去做一些高效优化。

加载时间概况：
![speed1](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/speed1.png)

影响网站加载因素：
![speed2](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/speed2.png)

缓存策略问题：
![speed3](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/speed3.png)

`DOM` 节点：
![speed4](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/speed4.png)

关键路径：
![speed5](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/speed5.png)

主线程情况：
![speed6](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/speed6.png)


#### 三、通过策略解决问题
1.`favicon.ico` 404 问题；✓
2.去除调试工具代码：`eruda`，线上环境是不需要的；✓
3.图片合并或者多个 `svg`；✓
建议使用 [`webpack-spritesmith`](https://github.com/mixtur/webpack-spritesmith)，简单使用如下：
```js
plugins: [
  new SpritesmithPlugin({
    src: {
        cwd: path.resolve(__dirname, 'src/ico'),
        glob: '*.png'
    },
    target: {
        image: path.resolve(__dirname, 'src/spritesmith-generated/jartto.png'),
        css: path.resolve(__dirname, 'src/spritesmith-generated/jartto.styl')
    },
    apiOptions: {
        cssImageRef: "~jartto.png"
    }
  })
]
```

4.大文件拆分 ✓
![bundle](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/bundle.png)

从上图分析得出，大文件主要包含：`dist.js`，`lottie.js`，`lodash.js`，`loading.json` 等文件。所以我们从这几个文件入手，逐个优化：
- `moment.js`：配置 `moment` 忽略本地化，可减少 `70kb`
  ```js
  ignoreMomentLocale: true
  ```
- `dist.js`：在给单页应用做按需加载优化时，一般采用以下原则：
* 把整个网站划分成一个个小功能，再按照每个功能的相关程度把它们分成几类。
* 把每一类合并为一个 `Chunk`，按需加载对应的 `Chunk`。
* 对于用户首次打开你的网站时需要看到的画面所对应的功能，不要对它们做按需加载，而是放到执行入口所在的 `Chunk` 中，以降低用户能感知的网页加载时间。
* 对于个别依赖大量代码的功能点，例如依赖 `Chart.js` 去画图表、依赖 `flv.js` 去播放视频的功能点，可再对其进行按需加载。

- `lottie.js`：分离减少 `60kb`
```js
externals: {
  lottie : 'react-lottie',
}
```
- `lodash.js`
```js
externals: {
  lodash : {
    commonjs: 'lodash',
    amd: 'lodash',
    root: '_' // indicates global variable
  }
}
```
- `动态导入以及文件拆分`
```js
dynamicImport: {
  webpackChunkName: true,
  loadingComponent: './components/Loading/jartto.js',
}
```
按照上面我们一步步处理后，重新打包分析一下文件构成：
![bundle1](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/bundle1.png)

这里为什么没有继续拆分 `dist.js` ，是因为目前阶段没有好的方案，需要对代码做很多调整，所以暂且保留。相关信息可以在 `Ant-Design Issuse` [Svg icons make bunlde size too large ](https://github.com/ant-design/ant-design/issues/12011) 中查看解决方案。

5.存放 `CDN`
- `loading.json`，大小54kb ✓
- `svg` 替换 2 倍图 ✓
- 删除项目冗余图片 ✓

6.优化 `TTFB`
- 减少 `DNS` 查询
- 使用 `CDN`
- 提早 `Flush`
- 添加周期头

7.移除阻塞渲染的资源
- `css` 预加载 `preload`
```
<link rel="preload">
```

- 异步加载第三方资源：
```
<script async src="https://cdn.jartto.wang/fastclick.js"></script>
```
没有 `async` 属性，`script` 将立即获取（下载）并执行，期间阻塞了浏览器的后续处理。如果有 `async` 属性，那么`script` 将被异步下载并执行，同时浏览器继续后续的处理。

8.确保文本在网页字体加载期间保持可见状态
利用 `font-display` 这项 `CSS` 功能，确保文本在网页字体加载期间始终对用户可见。
```css
@font-face {
    font-family: 'Arvo';
    font-display: auto;
    src:local('Arvo'),url(https://fonts.jartto.wang/fonts/temp.woff2)format('woff2');
}
```

9.采用高效的缓存策略提供静态资源
{% alert info %}
延长缓存期限可加快重访网页的速度。
{% endalert %}

`DNS TTL(Time-To-Live)`，简单的说它表示一条域名解析记录在 `DNS` 服务器上缓存时间.

当各地的 `DNS` 服务器接受到解析请求时，就会向域名指定的 `DNS` 服务器发出解析请求从而获得解析记录；
在获得这个记录之后，记录会在 `DNS` 服务器中保存一段时间，这段时间内如果再接到这个域名的解析请求，`DNS` 服务器将不再向 `DNS` 服务器发出请求，而是直接返回刚才获得的记录；

{% alert info %}
而这个记录在 `DNS` 服务器上保留的时间，就是 `TTL` 值。
{% endalert %}

所以一般更新域名解析的步骤如下：
1. 先查看域名当前的 `TTL` 值。
2. 修改 `TTL` 值为可设定的最小值，建议为 60 秒。
3. 等待一天，保证各地的 `DNS` 服务器缓存都过期并更新了记录。
4. 设置修改 `DNS` 解析到新的记录，这个时候各地的 `DNS` 就能以最快的速度更新到新的记录。
5. 确认各地的 `DNS` 已经更新完成后，再 `TTL` 值设置成常用的值(如: `TTL=86400`)。

如下图，`TTL` 值设置的最佳实践，可供参考：
![ttl](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/ttl.jpg)

{% alert success %}
后文我们会详细介绍 `DNS` 相关内容，欢迎各位童鞋关注。
{% endalert %}


10.避免 `DOM` 规模过大
网页包含的 `DOM` 节点最好少于 `1500` 个左右。理想状况是，树深度少于 `32` 个元素，且少于 `60` 个子/父元素。大型 `DOM` 可能会增加内存使用量、导致样式计算用时延长并产生高昂的布局重排费用。

11.最大限度地缩短关键请求深度
{% alert info %}
`关键请求链` 显示了以高优先级加载的资源。
{% endalert %}
我们可以通过：缩短链长、缩减资源的下载文件大小，或者推迟下载不必要的资源，从而提高网页加载速度。
![parse](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/parse.png)

当 `HTML` 解析过程中遇到一个 `script` 标记时，它会暂停 `DOM` 构建，将控制权移交给 `JavaScript` 引擎，等`JavaScript` 引擎运行完毕，浏览器再从中断的地方恢复 `DOM` 构建。

{% alert danger %}
也就是说，执行内联的 `JavaScript` 会阻塞页面的首次渲染。
{% endalert %}

在关键渲染路径中，我们通常要关注三个点：
- 页面首次渲染需要的关键资源数量
- 关键资源的大小
- 关键渲染路径的往返次数（Roundtrip）

{% alert success %}
我们的策略也非常简单，就是减少关键资源数量，降低资源大小，减少关键路径的往返次数。
{% endalert %}

优化关键渲染路径的常规步骤如下：
a. 对关键路径进行分析和特性描述：资源数、字节数、长度。
b. 最大限度减少关键资源的数量：删除它们，延迟它们的下载，将它们标记为异步等。
c. 优化关键字节数以缩短下载时间（往返次数）。
d. 优化其余关键资源的加载顺序：您需要尽早下载所有关键资源，以缩短关键路径长度。

更多详情，请参考[前端性能优化—关键渲染路径](https://segmentfault.com/a/1190000013767948)。

12.最大限度地减少主线程工作
考虑减少为解析、编译和执行 `JS` 而花费的时间。我们可以提供较小的 `JS` 负载来实现此目标。

13.最优配置 `nginx`
a. `gzip` 配置
```
gzip  on;
gzip_min_length  1k;
gzip_buffers     4 8k;
gzip_http_version 1.1;
gzip_comp_level 4;
gzip_types text/plain text/css application/json image/png image/x-icon application/javascript application/x-javascript text/javascript text/xml application/xml application/xml+rss text/cache-manifest application/octet-stream;
gzip_vary on;
```
b. `nginx` 开启缓存
如果你对浏览器缓存还不太清楚，欢迎移步[聊一聊浏览器缓存机制](http://jartto.wang/2019/02/14/web-cache/)。
```
location ~.*\.(html|htm|js|css|gif|jpg|jpeg|png|bmp|swf|ico|json|otf)$ {
  root /var/www/jartto_web/;
  index index.html;
  expires 1d;
}
```
{% alert info %}
Nginx 能非常有效地直接处理静态内容。在静态文件和 Nginx 在同一主机的情况下，这种特性尤为有用。
{% endalert %}

#### 四、效果如何？
{% alert warning %}
优化前：网站评分 27 ，首次内容绘制 6.9 秒
{% endalert %}
1.网站评分
![opt1](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/opt1.png)

2.加载概况：
![speed1](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/speed1.png)

{% alert info %}
优化后：网站评分 70 ，首次内容绘制 1.6 秒
{% endalert %}
1.网站评分
![opt2](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/opt2.png)

2.加载概况：
![speed7](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/speed7.png)

{% alert success %}
当然，优化还可以做更多，我们尽量让网站的评分接近 100 分，譬如：
{% endalert %}

1.网站评分
![opt3](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/opt3.png)

2.加载概况：
![speed8](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/web-optimization/speed8.png)

#### 五、总结
我们从头优化下来，做了不少代码改动，也达到了不错的效果。但是有几点还是需要注意：
1.尽可能减少白屏出现时间
骨架图解决 `webview` 加载页面过长的白屏过程。

2.关注整站性能，如 `TTFB`
服务端接口也需要同步优化，而不要仅仅依赖前端单方面优化。

3.按照使用情况加载优先使用的资源
- css 预加载
- font 预加载
- js 预加载
- 图片懒加载

4.请高效利用 `DNS` 和 `CDN`
- 增加缓存时间
- `DNS` 预解析

网站优化从来不是一蹴而就，需要不断的去优化细节，不断的摸索尝试。从我的角度来看，其实优化更像是在网站性能和加载速度之间找到一个平衡点。譬如，[文中](http://jartto.wang/2019/02/16/web-optimization/)我们为了优化文件打包大小，进行了大文件拆分。随之而来的问题就是拆分后的文件可能还会对某些文件有依赖，那么就影响到了关键渲染路径。

所以，优化不存在什么奇技淫巧，不断的去尝试，找到这个最佳优化点，这才是根本。





