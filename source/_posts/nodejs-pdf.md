---
title: 抓取网页生成 PDF
date: 2018-10-13 21:17:30
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/pdf.png
thumbnailImagePosition: left
tags: 
- nodejs
- pdf
- phantomjs
- puppeteer
comments: false
metaAlignment: center
categories: 技术博文
---
看到自己喜欢的在线文档，是不是总想保存下来慢慢学习。可是苦于没有现成的工具，这里我来介绍两个 JS 类库，只需要简单封装一下，从此想抓哪里抓哪里。
<!-- more -->
#### 一、使用 Phantomjs
1.简单使用
```js
const phantom = require('phantom');
(async function() {
  const instance = await phantom.create();
  const page = await instance.createPage();
  await page.on('onResourceRequested', function(requestData) {
    console.info('Requesting', requestData.url);
  });

  const status = await page.open('http://jartto.wang');

  await page.render('jarttoTest.pdf');

  await instance.exit();
})();
```
上面是一个完整的示例，我们来看看最核心的部分，不妨放大一下：
```js
page.open(address, function (status) {
  if (status !== 'success') {
      // --- Error opening the webpage ---
      console.log('Unable to load the address!');
  } else {
      // --- Keep Looping Until Render Completes ---
      window.setTimeout(function () {
          page.render(output);
          phantom.exit();
      }, 200);
  }
});
```
嗯，结合起来，就完美了。下面就是最佳实践：
```js
var page = require('webpage').create();
page.open('http://jartto.wang', function(status) {
  setTimeout(function() {
      if ( status === 'success' ) {
        page.render('test.png');
        phantom.exit(); 
      } else {
        console.log('Page failed to load.'); 
      }
  }, 200);
});
```

{% alert info %}
因为 open 操作会有响应时间，所以需要使用 setTimeout 来确保流程。
{% endalert %}

这里需要额外补充一点，如果需要检测 `UA`，那么你需要这么使用：
```js
var page = require('webpage').create();
page.userAgent = 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/46.0.2490.86 Safari/537.36';

page.open('https://securelearning.in', function() {
  page.render('image.png');
  phantom.exit();
});
```

这里就不再细说了，更多细节可以查看 [Demo](https://github.com/chenfengyanyu/my-web-accumulation/tree/master/phantomjs-demo)。

#### 二、使用 Puppeteer
{% alert info %}
既然 Phantomjs 这么好用，为啥还要了解 Puppeteer ？
{% endalert %}
提到这个问题，我们不得不来说一下 [`Puppeteer`](https://github.com/GoogleChrome/puppeteer) 强大的官方背景：Puppeteer（木偶） 英[ˌpʌpɪˈtɪə(r)]，美[ˌpʌpɪˈtɪr]，是 `Google Chrome` 团队官方的无界面 `（Headless）Chrome` 工具。所以不管从以后的发展还是完整性方面，`Puppeteer` 都是我们的首选。

那么，`Puppeteer` 能用来干啥，有哪些功能：
- 生成页面的截图和 `PDF`
- 爬取网站内容
- 模拟登陆、自动提交表单，`UI` 测试，键盘输入等
- 使用最新的 `JavaScript` 和浏览器功能，直接在最新版本的 `Chrome` 中运行测试。
- 捕获网站的时间线跟踪，以帮助诊断性能问题
- 测试 `Chrome` 扩展程序


{% alert success %}
说了这么多，让我们快速开始，迅速的体验一把。
{% endalert %}

1.安装
```bash
npm i puppeteer
```

2.注意事项
{% alert warning %}
安装并不会那么顺利，因为要依赖 Chromium ，所以你可能会遇到这样的异常。
{% endalert %}
Note: When you install Puppeteer, it downloads a recent version of Chromium (~170MB Mac, ~282MB Linux, ~280MB Win) that is guaranteed to work with the API. To skip the download, see Environment variables.

正确姿势：
```bash
set PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=1
npm i puppeteer

# 或者忽略依赖脚本安装 jartto's demo
npm i --save puppeteer --ignore-scripts
```

如果不能翻墙下载，那么就手动去官网下载 [`Chromium`](https://download-chromium.appspot.com/) 吧。当然，坑远不止这些，感兴趣的童鞋可以戳这里 [Puppeteer 异常处理](http://jartto.wang/2018/10/23/puppeteerjs/)。

3.下载核心包
```bash
npm i puppeteer-core
```
从 1.7 版本开始发布 `puppeteer-core` ，他默认不会下载 `Chromium` 。`puppeteer-core` 使用已安装的浏览器 或远程浏览器。具体可以查看官网文档 [`puppeteer vs puppeteer-core`](https://github.com/GoogleChrome/puppeteer/blob/v1.9.0/docs/api.md#puppeteer-vs-puppeteer-core).

4.示例
生成网站快照：
```js
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('http://jartto.wang');
  await page.screenshot({path: 'blog.png'});

  await browser.close();
})();
```
通过网站地址，生成 `pdf`：
```js
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('https://news.ycombinator.com', {waitUntil: 'networkidle2'});
  await page.pdf({path: 'hn.pdf', format: 'A4'});

  await browser.close();
})();
```
设置视口：
```js
await page.setViewport({width: 1024, height: 880});
```
设置 `cookie`：
```js
const COOKS =[
  {
  'domain': 'jartto.wang',
  'name': 'user',
  'value': 'jartto',
  }
]

await page.setCookie(...COOKS);
```

如果觉得例子还不够，这里有个[在线演示](https://try-puppeteer.appspot.com/)，可以试一试。


5.相关资源
{% alert success %}
学习资源总是很多，由浅入深的学习将会事半功倍，下面我稍微整理一下。
{% endalert %}
- 第一阶段
  - [在线 DEMO](https://try-puppeteer.appspot.com/)
  - [了解 Puppeteer](https://juejin.im/post/5a8151a05188257a7349c6db)
  - [Puppeteer入门及实践](https://www.jianshu.com/p/56babda610f9)
  - [Chromium 下载跳坑 ](https://blog.csdn.net/u010142437/article/details/79126564?utm_source=blogxgwz0)
  - [通过 Puppeteer 生成 PDF 文件](http://jartto.wang/2018/10/13/nodejs-pdf/)
- 第二阶段
  - [深入 Puppeteer](https://github.com/zhentaoo/puppeteer-deep)
  - [Puppeteer 异常处理](http://jartto.wang/2018/10/23/puppeteerjs/)
- 第三阶段
  - [中文文档](https://www.bookstack.cn/read/puppeteer-api-zh_CN/class-Page.md)
  - [Puppeteer API ](https://github.com/GoogleChrome/puppeteer/blob/v1.9.0/docs/api.md#environment-variables)

#### 三、总结
文章提供了两种方式，在线抓取 `URL` 生成 `PDF`，当然插件的功能并不简单如此，你还可以做更多有趣的事情。嗯，慢慢摸索、积累，相信我，总有一天你会用上的。