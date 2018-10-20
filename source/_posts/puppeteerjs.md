---
title: Puppeteer 异常处理
date: 2018-10-23 09:10:52
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/puppeteer.png
thumbnailImagePosition: left
tags: 
- puppeteer
- nodejs
comments: false
metaAlignment: center
categories: 技术博文
---
从一个生成 PDF 的需求中接触到了 Puppeteer ，随着使用场景的深入，异常问题也越发难以处理。索性纪录一下摸索过程，供新手参考学习。
<!-- more -->
#### 一、Chromium revision is not downloaded
```html
UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 1): Error: Chromium revision is not downloaded. Run "npm install" or "yarn install”
```

遇到这个问题，说明你并没有成功下载 `Chromium`，[手动下载](https://download-chromium.appspot.com/)（需要翻墙）可以解决你的问题。

#### 二、Unhandled promise rejection
需要使用 `catch` 处理异常，所以你需要这么处理：
```js
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch({
    executablePath: 'chromium/Chromium.app/Contents/MacOS/Chromium',
    headless: true
  });
  const page = await browser.newPage();
  await page.goto('http://jartto.wang');
  await page.screenshot({path: 'temp/blog.png'});
  await browser.close();
})().catch(error => console.log('error: ', error.message));
```
重点关注最后一行即可。

#### 三、spawn EACCES
没有权限，需要执行：
```bash
sudo chmod -R a+rwx chromium/Chromium.app
```

之后请设置正确的引用路径：
```js
executablePath: 'chromium/Chromium.app/Contents/MacOS/Chromium',
```

#### 四、PrintToPDF is not implemented
出现这个问题，是因为 `headless: false`：
```js
const browser = await puppeteer.launch({
  headless: false
});
```
官方文档中是这么说的：
{% alert info %}
NOTE Generating a pdf is currently only supported in Chrome headless.
{% endalert %}

#### 五、生成 PDF 乱码问题
小技巧：如果你发现无头打印异常，可以将设置改掉 `headless: false`，通过调试工具打开。

#### 六、在 Docker 中使用
在 `Docker` 中使用 `Puppeteer` 稍显复杂，因为其 `node` 库中自带的 `Chromium` 缺少一些依赖。这些依赖在桌面环境中一般都已自带，但是在 `Docker` 的 `node` 源镜像例如 `node-alpine` 或者 `node-slim` 是缺失的。所以，在 `Docker` 中使用 `Puppeteer` 需要首先安装这些缺失的依赖。

具体细节，请移步[在 Docker 中使用](https://www.meiwen.com.cn/subject/xkwsbftx.html)。

#### 七、更改默认视口
```js
await page.setViewport({width: 1024, height: 880});
```

#### 八、设置 `cookie`
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

#### 九、模拟点击
```js
// 输入框 id，搜索关键字 jartto
await page.type('#index-kw', 'jartto');
// 模拟点击提交按钮
await page.click('#index-bn');
// 跳转等待时间
await page.waitForNavigation({ timeout: 3000 });
```
