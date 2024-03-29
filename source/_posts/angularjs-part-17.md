---
title: Angularjs学习笔记16~总结
date: 2014-05-25 21:28:21
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
到这里，起初学习angular的一些总结笔记，基本也就整理完了。从刚开始接触angularjs到现在，大概也有大半年时间了。新的项目还在继续，不过用法万变不离其宗，掌握好基础，后续才能大刀阔斧的前行。
<!-- more -->
angularjs学习曲线很陡，所以一定要耐下心来，多敲敲代码。说来好笑，本来以为自己已经掌握了angularjs的基础用法，但是现实却远非想象般美好。当看了Ari Lerner的《AngularJs 权威教程》之后，陡然发现，我所了解的angularjs仅仅只是冰山一角。想要完全掌握它，真的很难，很难。

想起了一句话：每一个终点将会是更远处的一个起点！

好了，话不多说，一句话共勉吧：码农的世界充满了挑战与创新，相信自己，你可以改变未来！

接下来我们来做个简单的概括吧，主要从以下三方面来入手：
1.优缺点一览√
2.实际开发遇到的问题√
3.还需要怎么学习？√

#### 一、优缺点一览
优点：
①模板功能强大丰富，并且是声明式的，自带了丰富的Angular指令；
②是一个比较完善的前端MV*框架，包含模板，数据双向绑定，路由，模块化，服务，依赖注入等所有功能；
③自定义Directive，需要深入了解Directive的一些特性，简单的封装比较容易。
④ ng模块的依赖注入，能够很容易的写出可复用的代码，实际开发中UI变化很大，但是js的代码基本上很少改动。
⑤ Angular支持单元测试和端到端测试。

缺点：
①适合处理简单的CRUD，复杂的应用场景将会很难下手；
②内容网站，需要SEO的。angularjs不利于此类网站；
③交互频繁的网站，angularjs效率将会大大降低，详情请查看angularjs事件循环；
④兼容的话，1.3版本已经放弃IE8了。

#### 二、实际开发遇到的问题
①ng-repeat重复项问题；
②双向绑定频繁失效？
③ng-select子项循环用ng-repeat还是ng-option；
④全局变量如何使用，以及应用场景；
⑤$watch与$apply;
⑥事件分发；
⑦手动初始化；
⑧一个页面两个ng-app如何处理？
。。。

当然，有很多细节问题。毕竟我是新手嘛，这里请允许我买个关子。后续我会一一总结，罗列出来，分享给大家。

#### 三、还需要怎么学习？
当然，这只是我个人的学习路线。粗浅的觉得效果还是很明显，所以如果看到这里你还是很茫然，那么不妨试试这个套路。简单来说，分四个阶段吧：

首先，当然是先得死啃一本书了；
其次，大量实践总结，看看总结性博客（一定要看高手总结的），官方API；
再次，找一本高层次的angularjs书籍，还是死啃；
最后，熟读源码，了解原理，出关！

参考文章：
http://www.zhihu.com/question/22284218/answer/20893162
http://www.queyang.com/blog/archives/425