---
title: 有趣的border-image
date: 2016-07-07 23:29:58
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/css3_EF5FA1AA-59F5-47A7-9C65-E07DD1496954.png
thumbnailImagePosition: left
tags: 
- css3
comments: true
metaAlignment: center
categories: 技术博文
---
最近项目要做一个h5聊天页面，原本，我打算用定位小三角的方式实现聊天气泡的小尾巴，可是同事提出了一个问题：“你这么写，如果气泡要换肤怎么处理？”
<!-- more -->
嗯，这个，我竟无言以对。

那么问题来了，譬如qq中的聊天气泡，可以任意换肤，究竟是怎么实现的呢？这就非常有意思了，我们为何不去尝试和探索呢。于是乎，本文的keyword闪亮登场了，就是它：border-image

#### 一、先睹为快
![border](http://7xvi3w.com1.z0.glb.clouddn.com/border_border001.png)

#### 二、如何实现
我们直接忽略前戏，咳～咳，我就是这么直接。

方式一：制作小三角定位实现气泡；
{% image fancybox left clear group:travel http://7xvi3w.com1.z0.glb.clouddn.com/border_8D04C142-E3F1-4D4D-8A02-E5A899AAEA42.png 200px %}
html:
```html
<div id="arrow">小三角制作的气泡</div>
```
css:
```css
#arrow{
    border-radius: 2px;
    background-color: #ff8851;
    color: #fff;
    position: relative;
    margin-left: 2%;
    height: 45px;
    line-height: 45px;
    padding: 0 2%;
    display: inline-block;
}
#arrow:before{
    position: absolute;
    content: "\00a0";
    width: 0px;
    height: 0px;
    border-width: 15px;
    border-style: solid;
    border-color: transparent #ff8851 transparent transparent;
    top: 50%;
    margin-top: -15px;
    left: -25px;
}
```
这里在啰嗦一下，关于如何绘制三角箭头：
{% image fancybox left clear group:travel http://7xvi3w.com1.z0.glb.clouddn.com/border_9148A664-713F-4BD3-8CC5-FB95FAAB98A3.png 100px %}
html:
```html
<div id="triangle"></div>
```
css:
```css
#triangle{  
    height:0px;  
    width:0px;  
    border-width:25px;  
    border-style:solid;  
    border-color:red yellow blue green;  
}  
```
{% alert info %}
思路很简单，除了要显示色块的箭头，其他border-color均设置为transparent
{% endalert %}
方式二：旋转矩形实现气泡尾巴；
{% image fancybox left clear group:travel http://7xvi3w.com1.z0.glb.clouddn.com/border_1A0783EC-D3CB-4144-BB39-A2FEAF4E3903.png 250px %}
html:
```html
<div id="rectangle">旋转矩形制作的气泡</div>
```
css:
```css
#rectangle{
	border-radius: 2px;
    background-color: #ff8851;
    color: #fff;
    position: relative;
    margin-left: 2%;
    height: 45px;
    line-height: 45px;
    padding: 0 3%;
    display: inline-block;
}
#rectangle:before{
    position: absolute;
    content: "\00a0";
    width: 15px;
    height: 15px;
    top: 50%;
    left: -7px;
    background: #ff8851;
    transform: rotate(-45deg);
    margin-top: -10px;
    border-radius: 2px;
}
```
{% alert info %}
为了让箭头垂直居中，我们通过：正方形斜边长除以2，也就是(15*√2)/2≈10
{% endalert %}
方式三：border-image实现；
{% image fancybox left clear group:travel http://7xvi3w.com1.z0.glb.clouddn.com/border_418F043C-03FC-490A-9C7B-E80029195402.png 200px %}
html:
```html
<div id="stretch">使用border-image来实现</div>
```
css:
```css
#stretch{
    border: 15px solid transparent;
    display: inline-block;
    border-image:url(pic2.png) 15 30 stretch;
    -moz-border-image:url(pic2.png) 15 30 stretch;	/* Old Firefox */
    -webkit-border-image:url(pic2.png) 15 30 stretch;	/* Safari and Chrome */
    -o-border-image:url(pic2.png) 15 30 stretch;	/* Opera */
    color: #fff;
}
```

#### 三、探索
拼凑或旋转定位法：
- 思路简单，容易实现
- 部分安卓手机刷新加载的时候，会先加载出气泡的小尾巴，此时就略显尴尬😅

border-image法：
- 简易切换皮肤，多样化
- 相对麻烦，裁切数值需要手动调至最优

引深：点9切图；
{% alert info %}
即 .9 ，是andriod平台的应用软件开发里的一种特殊的图片形式，文件扩展名为：.9.png
智能手机中有自动横屏的功能,同一幅界面会在随着手机(或平板电脑)中的方向传感器的参数不同而改变显示的方向,在界面改变方向后,界面上的图形会因为长宽的变化而产生拉伸,造成图形的失真变形。
{% endalert %}
这里的border-image原理其实和点9图片原理十分相似。

#### 四、发现
我们先来看一张图，理解一下：
{% image fancybox left clear group:travel image2.png http://7xvi3w.com1.z0.glb.clouddn.com/border_8A6EAD2D-A017-483E-9383-1174ED11D0AC.png 100px %}

如上图所示：我们将图片分成了9个区域，1-3-7-9区域作为边框的四个角，2-4-5-6-8则为填充部分，qq中实现的换肤，奥秘就藏在这里。

border-image 属性是一个简写属性，用于设置以下属性：
- border-image-source：用在边框的图片的路径
	- url(temp.png)
- border-image-slice：图片边框向内偏移，类似于margin，分别表示上右下左
	- 15 15 15 15 
	- 10 fill(fill为可选属性，假如指定了，那么中间的区域就会有，否则则为透明)
- border-image-width：图片边框的宽度
- border-image-outset：边框图像区域超出边框的量
- border-image-repeat：图像边框是否应平铺(repeated)、铺满(rounded)或拉伸(stretched)

如果省略值，会设置其默认值。

举个🌰：
```css
border-image-slice:27 27 27 27
```
![border](http://7xvi3w.com1.z0.glb.clouddn.com/border_46176269_8.gif)
可以看到，我们按照上－右－下－左的顺序对图片进行了偏移，[动图来自这里](http://www.360doc.com/content/14/1016/13/2792772_417403574.shtml)。

再举个🌰，我们来个图片分析：
![border](http://7xvi3w.com1.z0.glb.clouddn.com/border_46176269_9.png)


#### 五、关于repeat和round
看完上面的参数介绍，你可能会和我一样，产生这样的疑惑：“repeat和round有啥区别？平铺or铺满？”
这就对了，我觉得还是有必要了解一下，于是，找到了这样的解释：
{% alert info %}
repeat是将四个角做水平和垂直方向的复制，如果角的图案太小，可能会铺出半截图片的情况；round则不同，将四个角等分，每个角图填充铺满空隙；
{% endalert %}

可能不是很直观，我们来用示例结合图片来说明：
{% image fancybox left clear group:travel http://7xvi3w.com1.z0.glb.clouddn.com/border_93A23A7C-CDE0-4E21-8995-852DA896E5E6.png 500px %}

#### 六、收尾
在使用border-image之前，一直有些顾虑。众所周知，移动端的h5兼容问题层出不穷，我们要照顾好广大android用户。值得欣慰的是，该css3属性对移动端的兼容非常好，经测试，完美通过。

so，大胆的使用border-image去做一些有创造性而又有趣的事情吧～

参考：
http://www.w3school.com.cn/cssref/pr_border-image.asp
http://www.360doc.com/content/14/1016/13/2792772_417403574.shtml
http://baike.baidu.com/









