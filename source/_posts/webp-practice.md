---
title: WebP 方案分析与实践
date: 2020-05-02 18:22:43
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/logo/wrk.png
thumbnailImagePosition: left
tags: 
- webp
- web
- optimization
comments: false
metaAlignment: center
categories: 技术博文
---
不管是移动端或者 PC 端，图片一直扮演着重要角色。图片大小直接影响着网络流量，运营成本以及用户体验。因此，减少图片大小成为网站优化最重要的一个环节。
<!-- more -->
#### 一、背景
当我们在做网站性能优化的时候，减少图片大小，意味着减少了网络传输，提升了网站加载速度，而这部分也是性价比最高的。

我们可以通过压缩图片来减少体积，但压缩比例一直是前端开发和设计师争执的焦点。压缩比例大的话，可以有效减少图片体积，对页面加载有利，但是却损失了像素，是设计师无法容忍的。

`WebP` 格式，谷歌开发的一种旨在加快图片加载速度的图片格式。优势在于它具有更优的图像数据压缩算法，在拥有肉眼无法识别差异的图像质量前提下，带来更小的图片体积，同时具备了无损和有损的压缩模式、`Alpha` 透明以及动画的特性，在 `JPEG` 和 `PNG` 上的转化效果都非常优秀、稳定和统一。

#### 二、目标与意义
我们先来看一组数据对比图：


如果你做过 `Gif` 动图，你肯定知道下面这样的处理意义有多大：


在肉眼无法识别差异的前提下，图片大小减少了 88%。

目前 Google、Facebook、阿里、京东的等国内外互联网公司广泛应用了 WebP，超过 70% 的浏览器已经支持 WebP，Safari 和 Foxmail 也在进行支持 WebP 的测试。


