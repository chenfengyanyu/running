---
title: 关于Hbuilder
date: 2015-02-13 20:45:50
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_11730409-B1D3-4C7C-88BF-211C8BA8921F.png
thumbnailImagePosition: left
tags: 
- hbuilder
- cordova
comments: true
metaAlignment: center
categories: 技术博文
---
如今，hybrid app也就是混合应用，如日中天。这是前端开发的一次机会，因为我们已经不再局限于pc和手机wap了，app我们也占有一席之地了。所以，hybrid app技术重要性可见一斑。
<!-- more -->
俗话说，好马配好鞍，今天我就来介绍一个编辑器。灰常好用，不信你看～
#### 一、下载安装
http://www.dcloud.io/
从官网下载文件后，简单安装即可使用。首次打开应用会有帮助文档，如下图所示，大致了解即可。
![hbuilder](http://7xvi3w.com1.z0.glb.clouddn.com/blog_9FF2D0D5-A886-4A15-8FFA-BD4AC90541E1.png)
Hbuilder里面的代码提示真的很强大，下面用动图大概演示一下：
![hbuilder](http://7xvi3w.com1.z0.glb.clouddn.com/blog_showjs.gif)
更多的快捷键，请参看lession1.text和lession2.test，这里就不细说了。

#### 二、创建项目
其实你完全可以把它当做一款前端开发编辑器，当然，这里我想演示的是如何创建“移动App”。
打开菜单→文件→新建→移动App，我们先来创建一个空模板：
![hbuilder](http://7xvi3w.com1.z0.glb.clouddn.com/blog_90CDD136-AA04-4689-8E58-58FC3A4774ED.png)

这样，移动应用就创建好了。

这里我们引入一个概念，HTML5+到底是什么呢？
> HTML5 Plus移动App，简称5+App，是一种基于HTML、JS、CSS编写的运行于手机端的App，这种App可以通过扩展的JS API任意调用手机的原生能力，实现与原生App同样强大的功能和性能。
通过HTML5开发移动App时，会发现HTML5很多能力不具备。为弥补HTML5能力的不足，在W3C中国的指导下成立了www.html5plus.org组织，推出HTML5+规范。
HTML5+规范是一个开放规范，允许三方浏览器厂商或其他手机runtime制造商实现。
HTML5+扩展了JavaScript对象plus，使得js可以调用各种浏览器无法实现或实现不佳的系统能力，设备能力如摄像头、陀螺仪、文件系统等，业务能力如上传下载、二维码、地图、支付、语音输入、消息推送等。

#### 三、项目结构
像上面这样，我们创建了一个空的移动App，目录结构如下图：
![hbuilder](http://7xvi3w.com1.z0.glb.clouddn.com/blog_68732442-D752-468B-9E3B-72F4834AD719.png)
css、img、js和index文件意思很明了，这里我就不费唇舌了。我们来着重看一下manifest.json文件。
manifest.json文件有两个视图模式：可视化视图和代码视图。

manifest.json分为4部分，我们用可视化视图来做说明：
①应用信息：设置应用名称，版本号以及入口页面。此外包含重力感应（应用启动时横屏或竖屏设置）和平台选择（ios，iphone，ipad，android）。

②iPad设置：设置iPad设备应用图标和启动页面，点击即可更换图片。如下图所示：
![hbuilder](http://7xvi3w.com1.z0.glb.clouddn.com/blog_3CD72F48-69EC-4E7C-80B4-62F2D19AA99B.png)

{% alert info %}
注意：这里的图片尺寸千万不要搞错了，一一对应就ok了。
{% endalert %}

③iPhone设置：设置iPhome设备上面应用图标和启动页面，与上面类似。
④Android设置：设置Android设备应用图标和启动页面，与上面类似。

#### 四、Web端调试
菜单栏中，我们找到如下图标，可以自由选择运行应用的浏览器。
![hbuilder](http://7xvi3w.com1.z0.glb.clouddn.com/blog_8399CBCA-1DEC-4F1B-A099-31DB2BA74F52.png)
直接点击就在所选浏览器中打开了。很简单，这里就不细说了。

#### 五、真机调试
首先，你需要将移动设备用数据线连接到电脑。
![hbuilder](http://7xvi3w.com1.z0.glb.clouddn.com/blog_2A341319-FC6E-43E1-8B0A-5516DEE6DC74.png)
接下来，点击在设备上运行。
这样，应用就在手机上面启动了。我们可以从Hbuilder的控制台中看到如下信息：
![hbuilder](http://7xvi3w.com1.z0.glb.clouddn.com/blog_6F7A3763-938A-46F7-87B0-57AF9D0825B5.png)
如上图所示，这里将成为以后我们调试、查看控制台输出信息的地方。

#### 六、Android和ios本地打包
到这里，我们的移动App已经可以运行了，但是这并不是最终版本。因为要在手机上面安装，ios系统需要的文件格式为ipa，而android系统需要的文件格式是apk。显然，我们当前的文件并不满足要求。
下面，我来说说如何打包App。在菜单中，选择发行→Android本地打包
![hbuilder](http://7xvi3w.com1.z0.glb.clouddn.com/blog_E752B405-19AD-4497-8928-7C4FFBC97337.png)

我们发现此时已然跳转到一个页面，对，这就是Hbuilder官网中介绍的Android本地打包方式。当然，android开发需要安装SDK。文章介绍很详细，请查看链接：http://ask.dcloud.net.cn/article/38
ios情况类似，但具体操作有所差异，ios开发需要用到XCode。请查看文章：http://ask.dcloud.net.cn/article/41

当你操作到这一步时，整个移动App基本就开发完成了。我们借助Hbuilder来开发，同时完成了打包过程。

#### 七、与cordova打包的异同
之前我总结过cordova开发以及打包方式（详见U9移动开发指南），本文主要介绍用Hbuilder来开发移动App。这种场景下，我来对两种方式做一些对比，分析分析它们之间的异同。

{% alert success %}
相同点：
{% endalert %}
①相同的打包环境
开发android都需要SDK以及Java运行环境，开发ios都需要XCode；
②开发过程类似
过程如下：首先开发web项目，然后将该web项目打包成移动设备可访问的文件格式。

{% alert success %}
不同点：
{% endalert %}
①访问设备的能力
cordova访问设备的能力依赖于它强大的插件，而Hbuilder的开发方式则更多的依赖了HTML5+来实现。
②展现方式
cordova借助命令行完成设备调试和打包操作，Hbuilder则做了更多可视化的东西。
③打包方式
cordova需要手动将web文件拷入下载好的cordova“壳子”中，从而通过指令打包。
Hbuilder需要借助Eclipse来完成打包，其过程类似于原生应用开发。
④后续项目更改
cordova会把文件融合为一个整体，后续更改需要手动分离web文件。
Hbuilder更大意义上类似一个文本编辑器，所以web文件和打包都是分离操作的，更改也就相对容易了。

暂时就想到这些，后续想到了再进行补充吧。

#### 八、总结
对比完cordova和Hbuilder的异同点，开发过程已经很明朗了。无非就是，取其精华去其糟粕。我们可以通过文本编辑器或 Hbuilder来开发web项目，至于打包阶段，我更偏向于使用cordova来操作，调试和打包一体化。

我们结合了Hbuilder的思想和Cordova的打包操作，从而使整个移动开发过程简洁、清晰、可控。

注意：Hbuilder建立移动应用会生成manifest.json文件，这在cordova打包中是不需要的。所以，如果结合两种方式来开发移动应用，你需要手动添加或者移除一些特性文件。
恩，其实开发移动App就是这么简单。












