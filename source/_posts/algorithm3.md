---
title: 算法图解3-递归，快排
date: 2018-11-26 11:53:37
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/logo.png
thumbnailImagePosition: left
tags: 
- algorithm
comments: false
metaAlignment: center
categories: 算法实践
---
说起递归，我们很容易就想到了斐波那契数列，这是一道常见的笔试题。可是，为什么面试官都喜欢考察递归的掌握程度呢？这节我们来解开递归的神秘面纱。
<!-- more -->
#### 一、理解递归
我们先来举个小例子：有一把钥匙在一个盒子里，这个盒子里还有盒子，而盒子里的盒子又有盒子，钥匙就在某个盒子里。那么我们如何找到钥匙呢？

{% alert info %}
思路很简单，一直开盒子，如果开到盒子继续再打开盒子，直到开出钥匙。
{% endalert %}

伪代码实现可能如下：