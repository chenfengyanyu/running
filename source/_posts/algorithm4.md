---
title: 算法图解4 - 散列表
date: 2018-11-27 21:53:37
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/logo.png
thumbnailImagePosition: left
tags: 
- algorithm
comments: false
metaAlignment: center
categories: 算法实践
---
听到散列表，第一时间想到 `Python` 中的 `Dict`，可是我们是前端。那么散列表在 JS 中是如何描述的呢？这节，我们来一探究竟。
<!-- more -->
#### 一、散列表
{% alert info %}
使用散列函数和数组创建了一种被称为散列表 hash table 的数据结构。
{% endalert %}

`Python` 提供的散列表实现为字典 ，你可使用函数 `dict` 来创建散列表。
```python
book = dict()
>>> book["jartto"] = 0.67
>>> book["milk"] = 1.49
>>> book["avocado"] = 1.49
>>> print book

{'avocado': 1.49, 'jartto': 0.67, 'milk': 1.49}
```
散列表由键和值组成。在前面的散列表 `book` 中，键为商品名，值为商品价格。散列表将键映射到值。

那么 JS 中如何实现呢？
{% alert success %}
其实 Map 对应的就是散列表，一种 Hash Map 结构。
{% endalert %}
```js
let book = new Map();
book.set('jartto', 0.67);
book.set('milk', 1.49);
book.set('avocado', 1.49);

console.log(book); // {"jartto" => 0.67, "milk" => 1.49, "avocado" => 1.49}

book.get('jartto'); // 0.67
```

#### 二、散列表特性
1.散列函数总是将同样的输入映射到相同的索引。
2.散列函数将不同的输入映射到不同的索引。
3.散列函数知道数组有多大，只返回有效的索引。如果数组包含 5 个元素，散列函数就不会返回无效索引 100。

{% alert warning %}
注意：对于同样的输入，散列表必须返回同样的输出。
{% endalert %}

#### 三、请问下面哪些散列函数是一致的？
1.`f(x) = 1` 　←------无论输入是什么，都返回 1
2.`f(x) = rand()` 　←------每次都返回一个随机数
3.`f(x) = next_empty_slot()` 　←------返回散列表中下一个空位置的索引
4.`f(x) = len(x)` 　←------将字符串的长度用作索引

#### 四、防止重复
假设我们要做一个投票统计，每个人只允许投票一次，为此，首先创建一个散列表，用于记录已投票的人。有人来投票时，检查他是否在散列表中。

{% alert danger %}
书中均为 Python 实现，这里我们都转为 JS 实现。
{% endalert %}
```js
let voted = new Map();
function checkVoter(name) {
    if(voted.has(name)) {
        console.log('kick them out!');
    } else {
        voted.set(name, true);
        console.log('let them vote!');
    }
}
checkVoter('jartto') // let them vote!
checkVoter('jartto') // kick them out!
```

#### 五、总结
这里总结一下，散列表适合用于：
1.模拟映射关系；
2.防止重复；
3.缓存/记住数据，以免服务器再通过处理来生成它们。

注意：
1.散列函数很重要。前面的散列函数将所有的键都映射到一个位置，而最理想的情况是，散列函数将键均匀地映射到散列表的不同位置。
2.如果散列表存储的链表很长，散列表的速度将急剧下降。然而，如果使用的散列函数很好 ，这些链表就不会很长！

#### 六、补充：性能
散列表的性能：
1.查找：平均 O(1)，最糟 O(n)
2.插入：平均 O(1)，最糟 O(n)
3.删除：平均 O(1)，最糟 O(n)

{% alert info %}
在平均情况下，散列表执行各种操作的时间都为 O(1)。
{% endalert %}

#### 七、散列表实现原理
这里，我们需要理解两个概念：
- 装填因子：散列表使用数组来存储数据，因此你需要计算数组中被占用的位置数。
- 调整长度：装填因子大于 1，就意味着有数据是没有存储位置的。

一旦填装因子开始增大，就需要在散列表中添加位置，这被称为调整长度 `resizing`。通常将数组增长一倍。

{% alert success %}
填装因子越低，发生冲突的可能性越小，散列表的性能越高。
{% endalert %}

一个不错的经验规则是：一旦填装因子大于 `0.7`，就调整散列表的长度。良好的散列函数让数组中的值呈均匀分布。糟糕的散列函数让值扎堆，导致大量的冲突。

#### 八、学习目录
- [算法图解1 - 二分查找和大O表示法](http://jartto.wang/2018/11/22/algorithm1/)
- [算法图解2 - 数组和链表](http://jartto.wang/2018/11/25/algorithm2/)
- [算法图解3 - 递归，快排](http://jartto.wang/2018/11/26/algorithm3/)
- [算法图解4 - 散列表](http://jartto.wang/2018/11/27/algorithm4/)
- [算法图解5 - 图和广度优先搜索](http://jartto.wang/2018/11/28/algorithm5/)
- [算法图解6 - 狄克斯特拉算法与贪婪算法](http://jartto.wang/2018/11/29/algorithm6/)
- [算法图解7 - 动态规划](http://jartto.wang/2018/11/29/algorithm7/)