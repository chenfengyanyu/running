---
title: Angularjs学习笔记14~项目演示（一）
date: 2014-05-20 10:19:48
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
不知不觉已经接近尾声了，更新的有点慢。但是随着时间的推移，我对angularjs有了更深的了解。下一步计划，搞个angularjs开发总结，细数angular中的一些陷阱以及技巧，肯定会相当有趣。
<!-- more -->
前面13篇笔记已经对angularjs中的一些重点做了简单说明，这节我们来运用前面所学的知识，构建一个简单的CRUD项目。确实很简单，我的目的无非是想用实例来展示一下angularjs在实际项目中的用法。当然，如果你是高手，那么这个例子可能不适合你。

大概的功能点罗列如下：
![project](http://7xvi3w.com1.z0.glb.clouddn.com/angular_Image.png)
1.用户输入动态统计，以及按钮的置灰；
2.filter展示，也就是搜索功能；
3.列表的添加和删除效果；
4.任务状态的修改；

#### 一、用户输入动态统计，以及按钮置灰
![project](http://7xvi3w.com1.z0.glb.clouddn.com/angular_1.gif)
html代码：
```html
 <div class="col-sm-3">
      <input type="text" class="form-control" id="inputName" ng-model="name" maxlength="20" placeholder="请输入..."/>
       <span>还可以输入<span class="red"> {{inputRemaining()}} </span>个字符！</span>
 </div>
 <button type="button" ng-click="addNew()" ng-disabled="!name" type="button" class="btn btn-default">添 加</button>
```

js代码：
```js
var max_len=20;
$scope.name="";
$scope.inputRemaining=function(){
       return max_len-$scope.name.length;
}
```
{% alert info %}
说明：代码很简单，字符统计通过inputRemaining的返回值来进行计算。按钮置灰用到了ng-disabled属性，当用户输入为空时，ng-disabled返回true，则按钮不可用。
{% endalert %}

#### 二、filter搜索功能
![project](http://7xvi3w.com1.z0.glb.clouddn.com/angular_filter.gif)
html代码：
```html
<div class="form-group">
        <label for="searchInput" class="col-sm-2 control-label">搜索条件：</label>
        <div class="col-sm-3">
               <input type="text" class="form-control" id="searchInput" ng-model="searchText.name" maxlength="20" placeholder="请输入名称..." /></br>
         </div>
</div>
<div ng-if="!searchText.name">您还有
      <span class="red">{{myCount()}}</span>项任务未完成!
</div>
<div ng-if="searchText.name.length">您输入的条件是：
      <span class="red">{{searchText.name}}</span>，搜索到
      <span class="red">{{(arrs|filter:searchText).length}}</span>项!
</div>
```
{% alert info %}
说明：过滤器前面笔记已经详细说明过，这里就不赘述了。至于统计多少项，用数组的length就可以办到了。此处用ng-if来选择容器的隐藏与显示，当用户输入操作时，显示搜索条件及搜索统计。
{% endalert %}

由于篇幅原因，剩下的内容移至下一篇笔记细说，传送门在此:
[Angularjs学习笔记15~项目演示（二）](http://jartto.wang/2014/05/21/angularjs-part-16/)











