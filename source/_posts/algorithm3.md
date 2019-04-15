---
title: 算法图解3 - 递归，快排
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
```js
const look_for_key = (box) => {
  for (let item in box) {
    if (item.is_a_box()) {
      look_for_key(item);
    } else if (item.is_a_key()) {
      console.log('Jartto: you found the key!');
    }
  }
}
```
如果使用循环，程序的性能可能更高；如果使用递归，程序可能更容易理解。如何选择要看什么对你来说更重要。

#### 二、基线条件和递归条件
{% alert info %}
由于递归函数调用自己，因此编写这样的函数时很容易出错，进而导致无限循环。
{% endalert %}

编写递归函数时，必须告诉它何时停止递归。正因为如此，每个递归函数都有两部分：
- 基线条件 （base case）
- 递归条件 （recursive case）

递归条件指的是函数调用自己，而基线条件则指的是函数不再调用自己，从而避免形成无限循环。
回到我们文章开头说的那个斐波那契函数：`1,1,2,3,5,8...`

```js
function fabnc(n){
    if(n === 1 || n === 2) return 1;
    return fabnc(n-1) + fabnc(n-2);
}
fabnc(5);
```
像上面这样，`if` 条件就是基线条件，调用 `fabnc(n-1) + fabnc(n-2)` 就是递归条件。

#### 三、栈和队列
为了更好的理解递归，我们需要先引入栈（`stack`）的概念。最早是在数据结构的线性表中接触到队列（`Queue`）与栈（`Stack`），我们先来回顾一下：

1.栈的插入和删除操作只允许在表的尾端进行（在栈中成为“栈顶”），满足 `FIFO：First In Last Out`；
2.队列只允许在表尾插入数据元素，在表头删除数据元素,满足 `First In First Out`。

更多细节，可以看之前这篇文章：[掌握 JS Stack Trace](http://jartto.wang/2017/12/09/grasp-js-stack-trace/)。

我们举个简单的例子：
```js
function c() {
    console.log('c');
    console.trace();
}
function b() {
    console.log('b');
    c();
}
function a() {
    console.log('a');
    b();
}
a();
```
对上面的代码做一下简短的分析：
1.当调用 `a` 的时候，它会被压到栈顶；
2.然后，当 `b` 在 `a` 中被调用的时候，它会被继续压入栈顶，当 `c` 在 `b` 中被调用的时候，也一样；
3.在运行 `c` 的时候，栈中包含了 `a`，`b`，`c`，并且其顺序也是 `a`，`b`，`c`；
4.当 `c` 调用完毕时，它会被从栈顶移出，随后控制流回到 `b`。当 `b` 执行完毕后也会从栈顶移出，控制流交还到 `a`。最后，当 `a` 执行完毕后也会从栈中移出。
5.每当有一个函数调用，就会将其压入栈顶。在调用结束的时候再将其从栈顶移出。

#### 四、递归调用栈
{% alert success %}
存储详尽的信息可能占用大量的内存。
{% endalert %}

每个函数调用都要占用一定的内存，如果栈很高，就意味着计算机存储了大量函数调用的信息。在这种情况下，我们有两种选择。
1.重新编写代码，转而使用循环；
2.使用尾递归 。需要注意的是，并非所有的语言都支持尾递归。

编写涉及数组的递归函数时，基线条件通常是数组为空或只包含一个元素。陷入困境时，请检查基线条件是不是这样的。

#### 五、小练习
1.利用递归求和：
```js
function sum(n) {
    if(n===0 || n===1) return n;
    return sum(n-1) + n;
}

sum(0) //0
sum(3) //6
sum(100) //5050
```
2.递归实现数组求和：
```js
function sum2(arr, n) {
    if(n <= 0) return 0;
    return sum2(arr, n-1) + arr[n-1];
}
sum2([1,2,3], 3); // 6
```
3.编写一个递归函数来计算列表包含的元素数：
```js
function arrLength(arr) {
    if(JSON.stringify(arr) === '[]') return 0;
    arr.splice(0,1);
    return 1 + arrLength(arr);
}
arrLength([1,2,3]) // 3
```
4.找出列表中最大的数字：
```js
function max(arr, n) {
    if(n <= 0) return 0;
    return arr[n-1] > max(arr, n-1) ? arr[n-1] : max(arr, n-1);
}
max([1,2,3], 3); // 3
max([-4,7,9,11], 4); // 11
```
5.递归求字符串长度
```js
function strLength(str) {
    if(str.length === 0) return 0;
    return 1 + strLength(str.substring(0, str.length - 1));
}
strLength('jartto'); // 6
```
6.快速排序
```js
function quicksort(arr)
{
    if (arr.length == 0) return [];
    let left = [];
    let right = [];
    let pivot = arr[0];
    for (let i = 1; i < arr.length; i++) {
        if (arr[i] < pivot) {
           left.push(arr[i]);
        } else {
           right.push(arr[i]);
        }
    }
    return quicksort(left).concat(pivot, quicksort(right));
}
console.log(quicksort([2,4,5,49,63,4,5,55,2,4,43])); // [2, 2, 4, 4, 4, 5, 5, 43, 49, 55, 63]
```

#### 六、总结
1.递归指的是调用自己的函数。
2.每个递归函数都有两个条件：基线条件和递归条件。
3.栈有两种操作：压入和弹出。
4.所有函数调用都进入调用栈。
5.调用栈可能很长，这将占用大量的内存。

#### 七、学习目录
- [算法图解1 - 二分查找和大O表示法](http://jartto.wang/2018/11/22/algorithm1/)
- [算法图解2 - 数组和链表](http://jartto.wang/2018/11/25/algorithm2/)
- [算法图解3 - 递归，快排](http://jartto.wang/2018/11/26/algorithm3/)
- [算法图解4 - 散列表](http://jartto.wang/2018/11/27/algorithm4/)
- [算法图解5 - 图和广度优先搜索](http://jartto.wang/2018/11/28/algorithm5/)
- [算法图解6 - 狄克斯特拉算法与贪婪算法](http://jartto.wang/2018/11/29/algorithm6/)
- [算法图解7 - 动态规划](http://jartto.wang/2018/11/29/algorithm7/)