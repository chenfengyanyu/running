---
title: 算法图解笔记1一二分查找和大O表示法
date: 2018-11-22 08:53:28
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/logo.png
thumbnailImagePosition: left
tags: 
- algorithm
- 二分查找
- 大O表示法
comments: false
metaAlignment: center
categories: 算法实践
---
闲来无事，翻了翻《算法图解》，觉得收获颇多，所以会陆续整理成笔记，纪录学习过程。嗯，第一篇先来看看二分查找和大O表示法吧。
<!-- more -->
#### 一、二分查找
二分查找是一种算法，其输入是一个有序的元素列表（必须有序的原因稍后解释）。如果要查找的元素包含在列表中，二分查找返回其位置；否则返回 `null` 。

{% alert info %}
一般而言，对于包含 `n` 个元素的列表，用二分查找最多需要 `log2 n` 步，而简单查找最多需要 `n` 步。
{% endalert %}

很多童鞋可能忘了倒数如何计算，这里我们先复习一下：对数运算是幂运算的逆运算。看看下面的公式，是不是有点回忆起来了。
![log](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/log.png)

我们通过 `JS` 来实现一下思路：
```js
/**
 * @msg: 二分查找
 * @param {Array} 数组
 * @param {String} 数值
 * @return: index
 */
const binarySearch = (arr, val) => {
  let start = 0;
  let end = arr.length - 1;
  let guess;

  while (start <= end) {
    let mid = Math.ceil((start + end) / 2);
    guess = arr[mid];
    if (guess === val) return mid;
    if (guess > val) {
      end = mid - 1;
    } else {
      start = mid + 1;
    }
  }
  return -1;
}

binarySearch([1, 3, 5, 7, 9], 3);
```

{% alert info %}
一般而言，应选择效率最高的算法，以最大限度地减少运行时间或占用空间。
{% endalert %}

按照上面的思路，我们使用二分查找实现了这个函数。嗯，看起来不错，我们来看看他的运行时间。

#### 二、运行时间
先来看一个概念：
线性时间（`linear time`）：最多需要猜测的次数与列表长度相同，这被称为线性时间。
我们举个例子来说：简单查找逐个地检查数字，如果列表包含 `100` 个数字，最多需要猜 `100` 次。如果列表包含 `40` 亿个数字，最多需要猜 `40` 亿次。

二分查找则不同：如果列表包含 `100` 个元素，最多要猜 `7` 次；如果列表包含 `40` 亿个数字，最多需猜 `32` 次。
![binary](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/binary.png)

{% alert success %}
可以看出来，对比线性查找来说，二分查找效率出奇的高。
{% endalert %}

既然提到运行时间，那么我们一般如何来计量呢？

#### 三、大O表示法
{% alert info %}
大O表示法：是一种特殊的表示法，指出了算法的速度有多快。。
{% endalert %}

![大O](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/algorithm/o.png)

通过上图，我们不难看出来：
随着元素数量的增加，二分查找需要的额外时间并不多，而简单查找需要的额外时间却很多。因此，随着列表的增长，二分查找的速度比简单查找快得多。

有鉴于此，仅知道算法需要多长时间才能运行完毕还不够，还需知道运行时间如何随列表增长而增加。这正是大O表示法的用武之地。使用大O表示法，这个运行时间为 `O (n )`。单位秒呢？没有——大O表示法指的并非以秒为单位的速度。

大O表示法让你能够比较操作数，它指出了算法运行时间的增速。

{% alert danger %}
需要注意：大O表示法计算的是操作数。
{% endalert %}


这里有个问题：
如果要查找的是 `Adit` ——电话簿中的第一个人，一次就能找到，无需查看每个条目。考虑到一次就找到了 `Adit`，请问这种算法的运行时间是 `O(n)` 还是 `O(1)` 呢？

{% alert info %}
大O表示法说的是最糟的情形。
{% endalert %}


因此，你可以说，在最糟情况下，必须查看电话簿中的每个条目，对应的运行时间为 `O(n)`。这是一个保证——你知道简单查找的运行时间不可能超过 `O(n)`。