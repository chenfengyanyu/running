---
title: 算法学习思路
date: 2019-04-07 19:06:18
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/logo.png
thumbnailImagePosition: left
tags: 
- algorithm
comments: false
metaAlignment: center
categories: 算法实践
---
很多前端童鞋对算法都有莫名的恐惧，究其原因无非两点：其一，对算法不了解；其二，没有找到实际应用场景。
<!-- more -->
#### 一、为什么学习算法？
这是个很有意思的问题，对算法感兴趣的人，不需要问为什么，算法对他们可能是一种兴趣。曾经有位同事，他刷遍了所有算法题，只要有算法竞赛，他都会去参加。当然，也取得了不错的成绩，甚至有了自己的世界排名。

一次闲聊中，我抛出了这个问题，他笑着说：没有为什么，就是觉得好玩。我一直好奇，为什么有人会觉得算法好玩，兴趣点究竟在哪里？

{% alert info %}
其实兴趣算一部分，而另一部分则是对编程能力的提升以及思维的扩展。
{% endalert %}

编程语言虽然该学，但是学习计算机算法和理论更重要，因为计算机语言和开发平台日新月异，但万变不离其宗的是那些算法和理论。

我们用两张趣图来看看差别：
1.学习算法前，你可能这样：
{% image fancybox left clear group:travel https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/dog2.gif 70% %}

2.学习算法后，你变的机灵了：
{% image fancybox left clear  group:travel https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/dog1.gif 70% %}

#### 二、如何入门？
那么如何学习算法呢？这里我推荐一本书《算法图解》，超薄的小册。书中通过一些简单有趣的例子来阐述算法的应用场景。之所以选它作为入门，是因为我们可以用很短的时间去翻读。

如果你还是觉得很麻烦，不用怕，我已经帮你准备好了学习笔记：
- [算法图解1 - 二分查找和大O表示法](http://jartto.wang/2018/11/22/algorithm1/)
- [算法图解2 - 数组和链表](http://jartto.wang/2018/11/25/algorithm2/)
- [算法图解3 - 递归，快排](http://jartto.wang/2018/11/26/algorithm3/)
- [算法图解4 - 散列表](http://jartto.wang/2018/11/27/algorithm4/)
- [算法图解5 - 图和广度优先搜索](http://jartto.wang/2018/11/28/algorithm5/)
- [算法图解6 - 狄克斯特拉算法与贪婪算法](http://jartto.wang/2018/11/29/algorithm6/)
- [算法图解7 - 动态规划](http://jartto.wang/2018/11/29/algorithm7/)

书中是围绕 `Python` 来写的，为了更适合前端童鞋阅读，我已经将相关代码用 `JS` 实现了一遍。当然，还是有很多的细节，还需要深入的去理解。但是最起码，我不再惧怕算法了，反而慢慢有点喜欢了。

{% alert info %}
算法的重中之重是培养算法思维。如果你能对算法灵活运用，那么就「大成」了。
{% endalert %}

#### 三、后面的路怎么走？
入门从来都不是一件简单的事情，在我们打开这扇神秘大门之后，将会有更多的宝藏需要我们去挖掘。

想要精通算法，在第一本书读完之后，我们还有更多的目标需要去完成。这里由浅入深的列举一些算法经典书籍，供大家参考学习：

1.入门系列：
《算法图解》《大话数据结构》
2.教科书之类：
《数据结构与算法分析》
3.进阶之旅：
《算法导论》
4.针对面试准备：
《剑指 Offer》《编程珠玑》
5.扩展阅读：
《算法之美》《算法帝国》
6.实践操作：
《算法竞赛入门经典》《力扣题库》


算法其实有很多经典书籍，我们没有那么多的时间去逐一翻阅，但是我们可以有一条明确的学习路线。

{% alert success %}
这条学习路线就是：入门 - 进阶 - 实践 - 升华
{% endalert %}

#### 四、配合实践
其实[上面](http://jartto.wang/2019/04/07/learn-algorithm/)已经提到了，在我们掌握算法思维之后，最为重要的一点就是刷题。相信很多童鞋都听过 `LeetCode` ，目前题库大概有 1000＋ 题目，没事了就去刷刷。

{% image fancybox left clear  group:travel https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/leetcode.png 70% %}

当然，刷题也可以由浅入深，先从简单的入手吧！
![leetcode](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/leetcode2.png)

#### 五、推荐学习
[JavaScript 算法与数据结构](https://github.com/trekhleb/javascript-algorithms/blob/master/README.zh-CN.md)
[可视化学习算法的好工具](http://algorithm-visualizer.org/#path=sorting/bucket/basic)
[互联网公司最常见的面试算法题有哪些？](https://www.zhihu.com/question/24964987)
[算法练习，和我一起来刷题吧～](https://github.com/chenfengyanyu/interview)

#### 六、总结
文章陆陆续续说了这么多，大体总结如下：
1.算法很重要，尤其是对于前端童鞋；
2.算法学习最好由浅入深，先了解算法思维，再去理解实际应用；
3.从一本小而薄的书开启，逐步全面的掌握相关知识体系；
4.推荐速成路线：《算法图解》-《剑指 Offer》- LeetCode 刷题 -《算法之美》-《算法导论》；
5.去努力实践，刷刷题库，参加参加竞赛；

好了，就这些吧，祝大家早日搞定算法，带上算法的王冠，去领略算法之美吧。

