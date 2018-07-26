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
因为移动端操作系统分为 `iOS` 和 `Android` 两派，所以本文的调试技巧也会按照不同的系统来区分。

#### 二、iOS 设备
- Safari
- iOS 模拟器

#### 三、通用方案
- spy-debugger
- Charles
- Fiddler
- Chrome Remote Devices

#### 四、HTTPS 安装证书
`Https` 无法抓包，需要安装额外的信任证书，按照下面操作：

#### 五、真机模拟
- localhost 转 ip，扫描二维码手机显示

#### 六、调试工具
这里推荐一款调试工具：[vConsole](#)，一个轻量、可拓展、针对手机网页的前端开发者调试面板。


#### 七、场景分析
{% alert warning %}
既然移动端调试有这么多种方案，那在实际操作中，我该如何取舍？
{% endalert %}


#### 八、白屏处理
- 方案分析 ☆
- 代码注释法 ☆
- 类库异常，兼容问题 ☆
- try catch ☆☆
- Debug 包 ☆☆☆
