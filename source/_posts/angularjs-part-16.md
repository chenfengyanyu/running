---
title: Angularjs学习笔记15~项目演示（二）
date: 2014-05-21 18:32:58
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
先把目录搬过来，上一篇笔记对前两条内容作了详细的阐述，这节我们接着来看后面两个功能如何实现。
<!-- more -->
列表如下：
1.用户输入动态统计，以及按钮的置灰；√
2.filter展示，也就是搜索功能；√
3.列表的添加和删除效果；
4.任务状态的修改；

#### 一、列表的添加和删除效果
![project](http://7xvi3w.com1.z0.glb.clouddn.com/angular_ffdif.gif)
html代码：
```html
<div class="form-group">
   <div class="col-sm-offset-2 col-sm-10">
   <button type="button" ng-click="addNew()" ng-disabled="!name" type="button" class="btn btn-default">添 加</button>
   </div>
</div>
<table class="table table-striped">
   <tr>
      <th>序列号</th>
      <th>名称</th>
      <th>操作</th>
  </tr>
  <tr ng-repeat="x in arrs|filter:searchText">
      <td>{{$index+1}}</td>
      <td class="throwLine_{{x.status}}">{{x.name}}</td>
      <td>
          <span><input ng-model="x.status" type="checkbox" />完成</span>|
          <a href="#" ng-click="deleteOne($index)">删除</a>
      </td>
   </tr>
</table>
```

JS代码：
```js
$scope.arrs=[];
$scope.addNew=function(){
     $scope.arrs.push({name:$scope.name,status:false});
     $scope.name="";
}
$scope.deleteOne=function(index){
     $scope.arrs.splice(index,1);
}
```
{% alert info %}
说明：添加和删除需要注意addNew和deleteOne这两个方法，添加主要用到了js中的push方法，而删除需要传入当前行的索引index，应用splice方法就可以了。当然，这里的删除和添加仅仅演示的是dom的动态刷新，实际项目中，你还需要后台代码去辅助实现相应功能。
{% endalert %}

#### 二、任务状态的修改
![project](http://7xvi3w.com1.z0.glb.clouddn.com/angular_4.gif)
css代码：
```css
<style>
    .throwLine_true {
        text-decoration: line-through;
        color: #cdcdcd;
    }
</style>
```

html代码：
```html
<table class="table table-striped">
   <tr>
      <th>序列号</th>
      <th>名称</th>
      <th>操作</th>
  </tr>
  <tr ng-repeat="x in arrs|filter:searchText">
      <td>{{$index+1}}</td>
      <td class="throwLine_{{x.status}}">{{x.name}}</td>
      <td>
          <span><input ng-model="x.status" type="checkbox" />完成</span>|
          <a href="#" ng-click="deleteOne($index)">删除</a>
      </td>
   </tr>
</table>

<div ng-if="!searchText.name">您还有
     <span class="red">{{myCount()}}</span>项任务未完成!
</div>
```

JS代码：
```js
$scope.myCount=function(){
      var temp=0;
      angular.forEach($scope.arrs, function(a){
           temp+=a.status?0:1;
      });
      return temp;
}
```
{% alert info %}
说明：该效果用户操作checkbox，进行任务统计并将完成项划掉。核心为myCount函数，通过循环遍历完成项，从而统计未完成任务数目。还需要注意的就是checkbox状态的更改去调用throwLine_true 类，从而完成相应功能。
{% endalert %}







