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
UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 1): Error: Chromium revision is not downloaded. Run "npm install" or "yarn install”

遇到这个问题，说明你并没有成功下载 `Chromium`

#### 二、Unhandled promise rejection
需要使用 `catch` 处理异常

#### 三、spawn EACCES
没有权限，需要执行：
```bash
sudo chmod -R a+rwx chromium/Chromium.app
```

#### 四、PrintToPDF is not implemented
https://github.com/GoogleChrome/puppeteer/issues/576

#### 五、生成 PDF 乱码问题
小技巧：如果你发现无头打印异常，可以将设置改掉 `headless: false`,，通过调试工具打开。

#### 六、在 Docker中使用
https://www.meiwen.com.cn/subject/xkwsbftx.html

#### 七、更改默认视口
```js
await page.setViewport({width: 1024, height: 880});
```

#### 八、设置 `cookie`：
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
