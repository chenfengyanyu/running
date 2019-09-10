---
title: web font 终极解决方案
date: 2016-09-04 17:19:15
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/ani_font.png
thumbnailImagePosition: left
tags: 
- font
- web
comments: true
metaAlignment: center
categories: 技术博文
---
绝大多数情况下，我们在网页开发中都要使用web安全字体。高傲的设计师肯定不局限于安全字体，那么问题来了：我们如何保证客户端对字体的显示一致性，本文对此进行深入探讨。
<!-- more -->
我们从头说起，设计师要求使用苹方字体，可是这并不是一个web安全字体。这意味着，如果客户端没有该字体库，那么我们的用户将看到一些默认字体，这违背了我们最初“显示一致性”的美好愿景。

怎么办，关于这个问题，我做出了以下尝试。

#### 一、查找方案
不是什么大问题，先百度一下，很快我查到了某中文字库，大致用法如下：
先引入js类库
```html
<script type="text/javascript" src="//cdn.webfont.youziku.com/wwwroot/js/wf/youziku.api.min.js"></script>
```
嵌入如下js代码片段：
```html
<script type="text/javascript">
   $youziku.load("body", "e1a2967b916d487f9814ebca91628dc1", "PingFangSC_L");
   $youziku.draw();
</script>
```
效果是达到了，可是当看到要收费的时候，我已经不爱你了👻。

第三方的方案还要收费，显然并不完美。那怎么办，一定还有其他方案，我们继续搜索。

#### 二、下载字体
找个免费的试试？好吧，去下载苹方字体，我自己引入字体包。结果却找不到下载的链接😅，全是广告，广告，广告。。。

内事不决问百度，外事不决问谷歌。果然，捕获到三个网站：
1.http://www.google.com/webfonts
2.http://www.dafont.com
3.http://priguy.com/pingfang-fonts-download/

如愿以偿的找到了苹方的下载地址，天无绝人之路啊！

历尽千辛万苦，总算找到了一个苹方字体库。下载之后发现文件后缀是ttf，浏览器中加载不出来。。。

可以从css的使用方式中看出端倪：
```css
@font-face {font-family: 'iconfont';
    src: url('fonts/iconfont.eot'); /* IE9*/
    src: url('fonts/iconfont.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
    url('fonts/iconfont.woff') format('woff'), /* chrome、firefox */
    url('fonts/iconfont.ttf') format('truetype'), /* chrome、firefox、opera、Safari, Android, iOS 4.2+*/
    url('fonts/iconfont.svg#iconfont') format('svg'); /* iOS 4.1- */
}
```
于是乎，我又想着有没有一个在线工具可以将ttf转化成woff，那这样问题就迎刃而解了。

#### 三、字体转换
继续探索，翻到了一些中文字体提供商，没准有解决方案呢。
https://www.justfont.com/fonts

好吧，都是vip，除了收费，你们还能干啥？

web中font-face中文字体如何截取使用？
http://www.zhihu.com/question/40098697

不知道这哥们对回答满意不，反正我觉得没啥用。

好不容易找到一个可以转化字体的网站，结果告诉我这个：
https://everythingfonts.com/ttf-to-woff
![ttf-to-woff](http://7xvi3w.com1.z0.glb.clouddn.com/ttf2woff.png)
找了一圈，大致问题如下：
1.文件太大的不能转换（下载到的苹方ttf为11M）；
2.只支持英文转换，中文的字体不能转换；
3.可以转换，但需要付费下载的；

显然我并不满意，看来还得继续，到底别人是怎么实现的：[譬如这个网站](https://www.pingxx.com/?from=1&desc=baidu)

#### 四、终级解决方案
真是太失望了，不就是想转换一下字体吗，没一个靠谱的。那我自己来搞，是时候展现真正的技术了～

大致思路如下，这里我借助了开源项目fontmin（[传送门](https://github.com/ecomfe/fontmin)）：
1.加载fontmin；
2.指定转换文件，并指定输出目录，如果没有则创建；
3.转换为eot、woff以及svg格式输出；
4.svg格式文件较大，借助imagemin（[传送门](https://github.com/imagemin/imagemin-svgo)）对其进行压缩处理，之后输出；

我们创建index.js，如下：
```js
var Fontmin = require('fontmin');
var imagemin = require('imagemin');
var svgo = require('imagemin-svgo');

var fontmin = new Fontmin()
    .src('PingFang-Light.ttf')
    .dest('build/')
    .use(Fontmin.ttf2eot())
    .use(Fontmin.ttf2woff({
        deflate: true
    }))
    .use(Fontmin.ttf2svg());

    //压缩svg图片
    imagemin(['build/*.svg'], 'build/optimize', {
        use: [svgo()]
    }).then(() => {
        console.log('Images optimized');
    });

    fontmin.run(function (err, files) {
    if (err) {
        throw err;
    }
    console.log(files[0]);
    // => { contents: <Buffer 00 01 00 ...> }
});
```

嗯，看起来不错，终于实现了。你可以去github上面clone该项目（[传送门](https://github.com/chenfengyanyu/web-font-convert)），最好能给个星哦😍

到这里，我觉得还有瑕疵。ttf大小为11M，转换的woff大小为7M左右，那么我们网页引用，会不会加载不起来呢？
![font_console](http://7xvi3w.com1.z0.glb.clouddn.com/webfont1.png)

看起来好像也不是什么问题了。

那如果我们追求极致的体验呢，如何对字体压缩？也就是精简字体包，那该如何操作？

#### 五、字体精简与压缩
踏破铁鞋无觅处，得来全不费功夫。关于字体压缩，我找到了这个：
[字蛛](https://github.com/aui/font-spider)是一个智能 WebFont 压缩工具，它能自动分析出页面使用的 WebFont 并进行按需压缩。

我们先来安装：
```sh
npm install font-spider -g
```
找到html文件目录，运行：
```sh
font-spider zh/*.html
```
来看看效果：
![font_console](http://7xvi3w.com1.z0.glb.clouddn.com/webfont2.png)
压缩前后，文件大小对比
![font_console](http://7xvi3w.com1.z0.glb.clouddn.com/webfont3.png)

{% alert info %}
注意：字蛛会分析页面使用的webfont，也就意味着他只抓取静态页面的font
{% endalert %}

看起来不错，可是，这又是什么鬼：
![webfont](http://7xvi3w.com1.z0.glb.clouddn.com/webfont4.png)
部分空格被替换成了！号，这肯定是零容忍啊，怎么办?

#### 六、意外收获
解决方案：https://github.com/aui/font-spider/issues/102

正愁字体文件无法编辑呢，结果发现了[某度字体编辑器](http://font.baidu.com/editor/)，来试试这个：（建议使用chrome浏览器，safari浏览器保存会出异常）
![font_console](http://7xvi3w.com1.z0.glb.clouddn.com/webfont5.png)
删除！号，我们重新保存，引入字体，刷新一下：
![font_console](http://7xvi3w.com1.z0.glb.clouddn.com/webfont6.png)
整个世界都豁然明亮了！

#### 八、font资源库
1.字蛛
http://font-spider.org
2.fonts.com
https://www.fonts.com
3.某中文字库
http://www.youziku.com/webfont/index/47066
4.google到的资源
http://www.google.com/webfonts
http://www.dafont.com














