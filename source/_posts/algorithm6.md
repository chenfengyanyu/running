---
title: 算法图解6 - 狄克斯特拉算法与贪婪算法
date: 2018-11-29 20:35:11
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/logo.png
thumbnailImagePosition: left
tags: 
- algorithm
comments: false
metaAlignment: center
categories: 算法实践
---
从这节开始，我们来看看一些有名的算法。学习算法最重要的就是扩展思维模式，虽然短期用不上，但是长远来说，我们的思维会逐渐打开乃至升华。
<!-- more -->
#### 一、狄克斯特拉算法
{% image fancybox left clear group:travel https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/dks.png 300px %}
在狄克斯特拉算法中，你给每段都分配了一个数字或权重，因此狄克斯特拉算法找出的是总权重最小的路径。
1.找出最便宜的节点，即可在最短时间内前往的节点。
2.对于该节点的邻居，检查是否有前往它们的更短路径，如果有，就更新其开销。
3.重复这个过程，直到对图中的每个节点都这样做了。
4.计算最终路径。

{% alert info %}
要计算加权图中的最短路径，可使用狄克斯特拉算法。
{% endalert %}

#### 二、明确几个概念
1.狄克斯特拉算法用于每条边都有关联数字的图，这些数字称为权重 `weight`；
2.带权重的图称为加权图 `weighted graph`；
3.不带权重的图称为非加权图 `unweighted graph`；
4.狄克斯特拉算法只适用于有向无环图 `directed acyclic graph，DAG`。
5.如果有负权边，就不能使用狄克斯特拉算法 。

{% alert info %}
要计算非加权图中的最短路径，可使用广度优先搜索。
{% endalert %}


#### 三、场景复现
{% image fancybox left clear group:travel https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/sence.png 80% %}
这个图中的节点是大家愿意拿出来交换的东西，边的权重是交换时需要额外加多少钱。拿海报换吉他需要额外加 `30` 美元，拿黑胶唱片换吉他需要额外加 `15` 美元。`Rama` 需要确定采用哪种路径将乐谱换成钢琴时需要支付的额外费用最少。

为此，可以使用狄克斯特拉算法！别忘了，狄克斯特拉算法包含四个步骤。在这个示例中，我们将完成所有这些步骤，因此也将计算最终路径。

#### 四、代码实现
p394-p409


#### 五、贪婪算法
假设有如下课程表，你希望将尽可能多的课程安排在某间教室上：
{% image fancybox left clear group:travel https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/class.png 300px %}
我们没法让这些课都在这间教室上，因为有些课的上课时间有冲突。所以我们希望在这间教室上尽可能多的课。
{% alert warning %}
那么如何选出尽可能多且时间不冲突的课程呢？
{% endalert %}

这个问题好像很难，不是吗？实际上，算法可能简单得让你大吃一惊。具体做法如下：
1.选出结束最早的课，它就是要在这间教室上的第一堂课。
2.接下来，必须选择第一堂课结束后才开始的课。同样，你选择结束最早的课，这将是要在这间教室上的第二堂课。

{% alert success %}
贪婪算法很简单：每步都采取最优的做法。在这个示例中，你每次都选择结束最早的课。
{% endalert %}

用专业术语说，就是你每步都选择局部最优解 ，最终得到的就是全局最优解。

#### 六、NP 完全问题
`NP` 完全问题的简单定义是，以难解著称的问题，如旅行商问题和集合覆盖问题。很多非常聪明的人都认为，根本不可能编写出可快速解决这些问题的算法。

`NP` 完全问题无处不在！如果能够判断出要解决的问题属于 `NP` 完全问题就好了，这样就不用去寻找完美的解决方案，而是使用近似算法即可。

{% alert danger %}
但要判断问题是不是NP完全问题很难，易于解决的问题和 `NP` 完全问题的差别通常很小。
{% endalert %}

简言之，没办法判断问题是不是 `NP` 完全问题，但还是有一些蛛丝马迹可循的。
1.元素较少时算法的运行速度非常快，但随着元素数量的增加，速度会变得非常慢。
2.涉及所有组合的问题通常是 `NP` 完全问题。
3.不能将问题分成小问题，必须考虑各种可能的情况。这可能是 `NP` 完全问题。
4.如果问题涉及序列（如旅行商问题中的城市序列）且难以解决，它可能就是 `NP` 完全问题。
5.如果问题涉及集合（如广播台集合）且难以解决，它可能就是 `NP` 完全问题。
6.如果问题可转换为集合覆盖问题或旅行商问题，那它肯定是 `NP` 完全问题。

#### 七、总结
1.贪婪算法寻找局部最优解，企图以这种方式获得全局最优解。
2.对于 `NP` 完全问题，还没有找到快速解决方案。
3.面临 `NP` 完全问题时，最佳的做法是使用近似算法。
4.贪婪算法易于实现、运行速度快，是不错的近似算法。

#### 八、学习目录
- [算法图解1 - 二分查找和大O表示法](http://jartto.wang/2018/11/22/algorithm1/)
- [算法图解2 - 数组和链表](http://jartto.wang/2018/11/25/algorithm2/)
- [算法图解3 - 递归，快排](http://jartto.wang/2018/11/26/algorithm3/)
- [算法图解4 - 散列表](http://jartto.wang/2018/11/27/algorithm4/)
- [算法图解5 - 图和广度优先搜索](http://jartto.wang/2018/11/28/algorithm5/)
- [算法图解6 - 狄克斯特拉算法与贪婪算法](http://jartto.wang/2018/11/29/algorithm6/)
- [算法图解7 - 动态规划](http://jartto.wang/2018/11/29/algorithm7/)