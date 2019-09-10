---
title: bootstrap之affix附加导航
date: 2016-08-07 10:42:11
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/bs_bootstrap.png
thumbnailImagePosition: left
tags: 
- bootstrap
- affix
comments: true
metaAlignment: center
categories: 技术博文
---
手头项目需要开发一个API文档页面，怎么样的交互才是好的呢？于是迅速的在大脑中搜索了一下，嗯，何不直接使用affix效果，简单粗暴而又实用🙈
<!-- more -->
想象总是很美好，很简单的东西却让我栽了大跟头，一起来看看我的心酸历程吧！
#### 一、查阅文档
首先，打开官网[传送门](http://v3.bootcss.com/javascript/#affix)，查看affix文档：
![affix](http://7xvi3w.com1.z0.glb.clouddn.com/bs_40342EE3-73F5-4353-B8DD-4AD5E93F43E6.png)
看起来很简单嘛，我们直接上手。

#### 二、用法简述
虽然是现成的类库，但是还是有很多细节，大概整理如下：
1.引入类库文件
```html
<!-- 如果还用到bootstrap其他组件，建议加在bootstrap.min.js -->
<script src="lib/bootstrap.min.js"></script>
<!-- 附加导航类库文件 -->
<script src="lib/affix.min.js"></script>
```
2.添加控制区域
```html
<body id="#top" data-spy="scroll" data-target="#myScrollspy">
  <nav  id="myScrollspy">
    <ul class="nav menu" id="myAffix">
        <li class="active"><a href="#section-1">大规模部署指南</a></li>
        <li><a href="#section-2">微信摇一摇周边</a></li>
        <li><a href="#section-3">IOS SDK</a></li>
    </ul>
  </nav>
</body>

<section id="section-1">大规模部署指南</section> 
<section id="section-2">微信摇一摇周边</section> 
<section id="section-3">IOS SDK</section> 
```
3.两种激活方式
其一：向元素添加附加导航（Affix）行为，只需要向需要监听的元素添加 data-spy="affix" 即可。
```html
<body id="#top" data-spy="scroll" data-target="#myScrollspy">
	<nav  id="myScrollspy">
        <ul class="nav menu" data-spy="affix" data-offset-top="125">
          <li class="active"><a href="#section-1">大规模部署指南</a></li>
          <li><a href="#section-2">微信摇一摇周边</a></li>
          <li><a href="#section-3">IOS SDK</a></li>
        </ul>
      </nav>
</body>
```
请注意这一行：
```html
<ul class="nav menu" data-spy="affix" data-offset-top="125">
```
{% alert info %}
如果html中添加了data-spy="affix"，则不再需要js激活，两种方式任选其一。
{% endalert %}
其二：通过js为元素附加导航（Affix）行为。
```js
$(document).ready(function(){ 
    $("#myAffix").affix({ 
        offset: { 
            top: 25,
            bottom:250
     } 
    }); 
}); 
```

#### 三、碰到问题
文档很清晰，照着撸了一遍，结果如下图：
![bug](http://7xvi3w.com1.z0.glb.clouddn.com/bs_6F3B1C26-DAD9-41FA-A0E0-66325045B8B5.png)
左侧菜单的红条（active类）并没有切换，那么滚动是如何触发active类在菜单项中切换的呢？我们来继续寻找问题。

#### 四、解决问题
查找了好些文档，大致描述都是如“用法简述”中一样，那么问题究竟出在哪里了，是什么触发了active的切换？黄天不负有心人，我找到了这样的描述：
{% alert info %}
"To easily add scrollspy behavior to your topbar navigation, add data-spy="scroll" to the element you want to spy on (most typically this would be the <body>). Then add the data-target attribute with the ID or class of the parent element of any Bootstrap .nav component." 
{% endalert %}
请留心nav类，这就是问题的所在。你需要为你的元素添加nav类，否则acitve滚动监听肯定无效。

#### 五、继续深究
到这里，问题基本就解决了。我可不是那么容易就会善罢甘休的，新问题来了：那导航菜单如果包含二级菜单呢，又该如何操作？
```html
<nav id="affix-nav" class="sidebar col-md-4">
    <ul class="nav sidenav" data-spy="affix" data-offset-top="10">
        <li class="active"><a href="#cupcake">Cupcake Lorem Ipsum</a>
	        <ul class="nav">
		        <li><a href="#sweetjelly">Sweet Jelly</a></li>
		        <li><a href="#tiramisu">Tiramisu</a></li>
		        <li><a href="#pie">Pie</a></li>
	        </ul>
        </li>
        <li><a href="#veggie">Veggie Lorem Ipsum</a>
	        <ul class="nav">   
		        <li><a href="#coriander">Coriander</a></li>
		        <li><a href="#kohlrabi">Kohlrabi</a></li>
		        <li><a href="#amaranth">Amaranth</a></li>
		        <li><a href="#soybean">Soybean</a></li>        
	        </ul>
        </li>
    </ul>
</nav>
```
看完代码，想必我就不用多说了吧！核心思想就是嵌套，请用心体会；

到这里你以为完了？图样，图森破！简单使用场景到这里就可以了，但是affix给我们提供了一些事件处理方法，你不想试试？细心的我已经帮你拿来了，如下：
![event](http://7xvi3w.com1.z0.glb.clouddn.com/bs_02C9DA45-EF47-4AAE-AA08-726F096AC388.png)
用法简单赘述一下：

```js
$(document).ready(function(){
    $('#myAffix').on('affixed.bs.affix', function(){
        console.log('The navigation menu has been affixed. Now it doesn't scroll with the page.');
    });
});
```
#### 六、修复bug
本来要高高兴兴的去睡觉，结果来回滚动几次，发现滚动到顶部的时候，菜单的active效果丢失了。查看源码之后，发现active类返回顶部被移除了。好吧，你能移除，我就能加回来，刚学的事件方法派上用场了。
```js
$(document).ready(function(){
    $("#myAffix").affix({
        offset: {
            top: 25,
            bottom:250
     }
    });

    $("#myAffix").on('affixed-top.bs.affix', function(){
        console.log('arrive to the top');
        $(this).children().first().addClass('active');
    });
});
 ```
 加上代码，上下滚动了无数次，嗯，效果还不错～😊

#### 七、完美收场
啰啰嗦嗦说了这么些，我觉得还是很有必要的，这里其实我想提供的是一种解决问题的思路。从“拿到需求－查阅文档－亲自尝试－遇到问题－查找问题－解决问题－认真反思－深入了解－解决bug”，整个过程自己觉得受益匪浅，所以分享出来，希望能够帮到遇到同样问题的你。


参考文档：
http://v3.bootcss.com/javascript/#affix
http://tutsme-webdesign.info/bootstrap-3-affix/
http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/bootstrap-affix.php
http://stackoverflow.com/questions/20526728/bootstrap-3-scrollspy-not-working-with-affix-and-panel











