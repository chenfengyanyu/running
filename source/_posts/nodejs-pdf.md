---
title: 使用 Nodejs 生成网页 PDF
date: 2018-10-13 21:17:30
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/pdf.png
thumbnailImagePosition: left
tags: 
- nodejs
- pdf
comments: false
metaAlignment: center
categories: 技术博文
---
众所周知，Nodejs 可以用来做很多有意义的事情，偶尔来写一款小工具或是一个小插件也别有一番风味。这不，我们来用 Nodejs 写一款工具，将网页保存成 PDF。
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
至于为什么使用 setTimeout，后问会细说这个问题。
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