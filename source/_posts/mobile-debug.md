---
title: H5 移动调试全攻略
date: 2018-11-01 23:33:09
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/Debug.jpg
thumbnailImagePosition: left
tags: 
- html5
- mobile
- debug
comments: false
metaAlignment: center
categories: 技术博文 
---
随着移动设备的高速发展，`H5` 开发也成为了 `F2E` 不可或缺的能力。而移动开发的重中之重就是掌握调试技巧，定 `Bug`于无形。
<!-- more -->
#### 一、概要
因为移动端操作系统分为 `iOS` 和 `Android` 两派，所以本文的调试技巧也会按照不同的系统来区分。寻找最合适高效的方式，才能让你事半功倍。

{% alert success %}
文章会列举目前适合移动端调试的多种方案，快来选择你的最佳实践吧！
{% endalert %}

#### 二、iOS 设备
{% alert info %}
Safari：iphone 调试利器，查错改样式首选，需要我们做如下设置：
{% endalert %}
- 浏览器设置：Safari - 偏好设置 - 高级 - 勾选「在菜单栏中显示开发」菜单
- iphone 设置：设置 - Safari - 高级 - 打开 Web 检查器

大功告成，这时候通过手机的 `Safari` 来打开 `H5` 页面，我们通过浏览器开发选项可以看到：
![safari1](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/mobile-debug/safari1.png)

{% alert info %}
iOS 模拟器：不需要真机，适合调试 `Webview` 和 `H5` 有频繁交互的功能页面。
{% endalert %}
首先下载 `Xcode` ，运行项目，选择模拟器 `iphonex`，编译后就会打开模拟器，如下：

{% image fancybox left  group:travel https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/mobile-debug/ios1.png 320px %}
{% image fancybox right clear group:travel https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/mobile-debug/ios2.png 320px %}

可以看到 `H5` 已经在「壳子」中运行起来了，下来就可以尝试调用 `Webview` 的方法，和「壳子」交互了。更多的调试技巧可以参考文章：[iOS 模拟器调试](http://imweb.io/topic/5bcd417305c8cb261b76a1f9)。

{% alert success %}
具体的调试功能还是依赖浏览器的开发选项，与上无异，就不赘述了。
{% endalert %}

#### 三、抓包
{% alert info %}
[Charles](https://www.charlesproxy.com/download/)： Mac OS 系统首选的抓包工具，适合查看、控制网络请求，分析数据情况。
{% endalert %}

`Charles` 抓包首先需要配置手机代理，`Wifi` - 配置代理（IP 地址） - 手动，如下图：

{% image fancybox left  group:travel https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/mobile-debug/ip1.jpeg 320px %}
{% image fancybox right clear group:travel https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/mobile-debug/ip2.jpeg 320px %}

配置好手机代理，这时候打开 `Charles` ，就会收到确认提醒，选择允许。接下来就可以捕获手机的请求了，但是这些都是常规操作，我们来点高级的。

{% alert success %}
有意思的是：我们可以用本地文件来替换线上文件，方便调试，远程定位线上问题。
{% endalert %}

选择 `Structure`，找到需要替换的文件，右键菜单 - `Map Local`，如下图：
![map1](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/mobile-debug/map1.png)

这时候就会打开一个弹窗，填写具体的配置：
![map2](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/mobile-debug/map2.png)

OK，大功告成，快去改动本地文件吧，从此再也不怕线上调试了。

{% alert warning %}
与之相应的是 Windows 平台的 Fiddler，功能大致相似，这里就不细说了。
{% endalert %}

#### 四、综合方案
- spy-debugger
- whistle

#### 五、HTTPS 安装证书
`Https` 无法抓包，需要安装额外的信任证书，按照下面操作：

#### 六、真机模拟
- Chrome Remote Devices
- `localhost` 转 `ip`，扫描二维码手机显示。

#### 七、调试工具
这里推荐一款调试工具：[vConsole](#)，一个轻量、可拓展、针对手机网页的前端开发者调试面板。


#### 八、场景分析
{% alert warning %}
既然移动端调试有这么多种方案，那在实际操作中，我该如何取舍？
{% endalert %}


#### 九、白屏处理
- 方案分析 ☆
- 代码注释法 ☆
- 类库异常，兼容问题 ☆
- try catch ☆☆
- Debug 包 ☆☆☆
- ES6 语法兼容
