---
title: 掌握排序算法
date: 2016-07-15 18:00:24
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/sort_F2B2EEC0-781B-4EA3-81D7-1F04DE54F7F9.png
thumbnailImagePosition: left
tags: 
- js
- 排序
- 算法
comments: true
metaAlignment: center
categories: 技术博文
---
偶然间翻出了大学时的课本数据结构，毕业那会，好多书都丢掉了，唯有c语言和数据结构，我放进了行李箱。这是我程序生涯的开始，我想，我将会一直珍藏它们。
<!-- more -->
好了，不再自我感动了。作为前端工程师，技能一直被低估，但又有着全栈的要求。看似矛盾的话语，或多或少透露着一丝无奈。既然如此，那么F2E们，奔跑起来吧，也许你需要的只是一本数据结构！

咳～咳（多么生硬的过渡😏），那么咱们就从排序算法说起吧！
#### 一、js中sort排序
首先，我们不得不提一下js中的sort排序：
```js
function systemSort(array) {
    return array.sort(function(a, b) {
        return a - b;
    });
}

systemSort([4,2,6,9,1,11,3,3]);
```
应用了数组的sort方法，很简单，这里就不啰嗦了，继续往下走～
#### 二、插入排序
{% alert success %}
每次将一个待排序的记录，按其关键字大小插入到前面已经排好序的文件中的适当位置，直到全部记录插入完成为止。
{% endalert %}
1.直接插入排序；
```js
var a = [2,4,5,49,63,4,5,55,2,4,43];
function insertSort(arr) {
  for(var i = 1, j; i < arr.length; i++) {
    j = i;
    v= arr[j];
    while(arr[j-1] > v) {
        arr[j] = arr[j-1];
        j--;
        if(j === 0) {
            break;
        }
    }
    arr[j] = v;
  }
  return arr;
}
insertSort(a)//[2,2,4,4,4,5,5,43,49,55,63]
```
2.希尔排序：缩小增量排序；
```js
function shellSort(arr) {
    var N = arr.length;
    var h = 1;
    var i, j, v;
    while (h < N / 3) {
        h = 3 * h + 1; // ①
    }
    while (h >= 1) {
        for (i = 1; i < arr.length; i++) {
            j = i;
            v = arr[j];
            while (j > 0 && arr[j - 1] > v) {
                arr[j] = arr[j - 1];
                j--;
            }
            arr[j] = v;
        }
        h = (h - 1) / 3; // 从①可以保证，肯定能除尽的
    }
    return arr;
}      
```
#### 三、交换排序
{% alert success %}
两两比较待排序记录的关键字，发现两个记录的次序相反时即进行交换，直到没有反序的记录为止。
{% endalert %}
1.起泡排序；
```js
function bubbleSort(arr) {
    var n = arr.length,
        i = 0,
        temp;
    while(--n) {
        while (i < n) {
            if(arr[i] > arr[i+1]) {
                temp = arr[i];
                arr[i] = arr[i+1];
                arr[i+1] = temp;
            }
            i++;
        }
        i = 0;
    }
    return arr;
}
```
2.快速排序；
```js
function quickSort(arr){
    //如果数组<=1,则直接返回
    if(arr.length<=1){return arr;}
    var pivotIndex=Math.floor(arr.length/2);
    //找基准，并把基准从原数组删除
    var pivot=arr.splice(pivotIndex,1)[0];
    //定义左右数组
    var left=[];
    var right=[];

    //比基准小的放在left，比基准大的放在right
    for(var i=0;i<arr.length;i++){
        if(arr[i]<=pivot){
            left.push(arr[i]);
        }
        else{
            right.push(arr[i]);
        }
    }
    //递归
    return quickSort(left).concat([pivot],quickSort(right));
}                
```
偶然间看到了这一例（[参考地址](http://www.stoimen.com/blog/2010/06/11/friday-algorithms-quicksort-difference-between-php-and-javascript/)），也挺不错：
```js
var a = [2,4,5,49,63,4,5,55,2,4,43];
function quicksort(arr)
{
    if (arr.length == 0)
        return [];
    var left = new Array();
    var right = new Array();
    var pivot = arr[0];
    for (var i = 1; i < arr.length; i++) {
        if (arr[i] < pivot) {
           left.push(arr[i]);
        } else {
           right.push(arr[i]);
        }
    }
    return quicksort(left).concat(pivot, quicksort(right));
}
console.log(quicksort(a));
```

#### 四、选择排序
{% alert success %}
每一趟从待排序的记录中选出关键字最小的记录，顺序放在已排好序的子文件的最后，直到全部记录排序完毕。
{% endalert %}
1.直接选择排序
```js
function selectionSort(arr) {
    var ret = [],
        min,
        i;
    while(arr.length) {
        min  = Math.min.apply(null, arr);
        ret.push(min);
        arr.splice(arr.indexOf(min), 1);
    }
    return ret;
}
```
2.归并排序
```js
function mergetSort(arr) {
    if(arr.length === 1) {
        return arr;
    }
    var leftArr = arr.slice(0, Math.floor(arr.length / 2));
    var rightArr = arr.slice(leftArr.length);
    // 递归
    return merge(mergetSort(leftArr), mergetSort(rightArr));
    // 合并有序序列
    function merge(arrLeft, arrRight) {
        var indexLeft = 0,
            indexRight = 0,
            sl = arrLeft.length,
            sr = arrRight.length,
            ret = [];
        while(true) {
            if(indexLeft < sl && indexRight < sr) {
                if(arrLeft[indexLeft] < arrRight[indexRight]) {
                    ret.push(arrLeft[indexLeft]);
                    indexLeft++;
                     } else {
                    ret.push(arrRight[indexRight]);
                    indexRight++;
                }
            } else {
                if(indexLeft < indexRight) {
                    ret = ret.concat(arrLeft.slice(indexLeft));
                } else {
                    ret = ret.concat(arrRight.slice(indexRight));
                }
                break;
            }
        }
        return ret;
    }
}
```

说了这么多，最后送大伙一个排序动画，体验一下这些排序算法的迷人魅力。
[排序动画演示，猛戳这里！！！](http://jun-lu.github.io/SortAnimate/index.html)。
![sort](http://7xvi3w.com1.z0.glb.clouddn.com/sort_1301A029-9C7B-4BBC-A25A-B57561AFC6CC.png)


参考文章：
http://www.2cto.com/kf/201412/364945.html
http://www.stoimen.com/blog/2010/06/11/friday-algorithms-quicksort-difference-between-php-and-javascript/
http://www.cnblogs.com/idche/archive/2011/02/16/1956397.html































