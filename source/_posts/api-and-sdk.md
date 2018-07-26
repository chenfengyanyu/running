---
title: API 和 SDK
date: 2018-05-13 22:57:23
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/sdk.png
thumbnailImagePosition: left
tags: 
- api
- sdk
comments: false
metaAlignment: center
categories: 前端分享
---
一直以来，对 API 和 SDK 都有着模模糊糊的认识。突然有一天，这个问题摆在你的面前，你如何去解释呢？
<!-- more -->
#### 一、API 定义
API (Application Programming Interface)应用程式接口：通常由一组函式库所组成，提供用户编程时的接口，是一些预先定义的函数，目的是提供应用程序与开发人员基于某软件或硬件得以访问一组例程的能力，而又无需访问源码，或理解内部工作机制的细节。

{% alert info %}
例如：我要在 自己的网页 上加入 google map网页的功能，就使用 "google map API"。
{% endalert %}

#### 二、SDK 定义
SDK (Software Development Kit)软件开发工具集：是用来帮一个产品或平台开发应用程式的工具组，由产品的厂商提供给开发者使用的。

通常是某一家厂商针对某一平台或系统或硬件所发布出来用以开发应用程式的工具组，在这个工具包里面，可能包含了各式各样的开发工具，模拟器等。

{% alert info %}
例如：给`Android` 平台使用的 `Android SDK` 就是用来开发 `Android` 系统上面的应用程式。
{% endalert %}

这里只说明定义，具体细节请[参考](https://www.zhihu.com/question/21691705/answer/26406216)。

#### 三、通俗易懂的解释
看到[知乎](https://www.zhihu.com/question/21691705)上面一个有意思的回答，可以拿来逗乐：
- 你是个男的
- 你要开发一个 BABY
- SDK 就是房间、床、你老婆等一系列相关的工具、文档
- API 就是你老婆开放的接口

{% alert warning %}
这个答案不是很准确，但是足够你理解 API 和 SDK 在大方向上的不同。
{% endalert %}

#### 四、API 与 SDK 的差异
{% alert success %}
`SDK` 是用来开发某一个平台的程式的工具包，`API` 是让同一平台下的程式取用它的功能的函式库。
{% endalert %}

实际上 `SDK` 包含了 `API` 的定义，`API` 定义一种能力，一种接口的规范，而 `SDK` 可以包含这种能力、包含这种规范。但是 `SDK` 又不完完全全只包含 `API` 以及 `API` 的实现，它是一个软件工具包，它还有很多其他辅助性的功能。为了使用 `API` 函数，我们就要有跟 `API` 所对应的 `.H` 和 `.LIB` 文件，而 `SDK` 正是提供了一整套开发 `Windows` 应用程序所需的相关文件、范例和工具的“工具包”。

更多差异，请[移步](https://www.zhihu.com/question/21691705/answer/149935191)。

