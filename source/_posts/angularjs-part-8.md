---
title: Angularjs学习笔记07~表单验证
date: 2014-04-25 12:15:40
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
前面的6篇笔记大概介绍了一下angularjs的一些基础，确实挺无味。这一节就来一些干货，我们来看看angularjs是如何处理表单验证的。当你亲自尝试过之后，你定会深深的喜欢上angularjs处理数据的这种方式，so cool~
<!-- more -->
那么，让咱们现在就开始吧！

#### 一、表现形式
①隐藏\显示错误提示消息；
![angularjs](http://7xvi3w.com1.z0.glb.clouddn.com/blog_angular_show.gif)
②高亮输入框
![angularjs](http://7xvi3w.com1.z0.glb.clouddn.com/blog_angualr_validate.gif)

③自定义验证
当然，如果以上两种表格验证方式还不能满足需要的话，那么也不用担心。我们可以通过编写指令来随心所欲的控制表单验证方式，这里就不细说了。后续在指令章节，我会通过具体事例来展现。

上面展现了三种方式，当然只要你善于创造，肯定不会局限于上述表现形式。就像网络流行语所说：you can you up！

#### 二、验证示例
上面展示了angularjs中两种验证方式，看起来不错吧，下面我通过简单代码一个个来具体说明。这里需要注意两个问题：

其一，需要引入以下文件（我用了angular-ui）
```html
<link href="bootstrap.css" rel="stylesheet" type="text/css" />
<script type='text/javascript' src='angular.js'></script>
<script type='text/javascript' src='ui-bootstrap-tpls-0.10.0.js'></script>
<script type='text/javascript' src='main.js'></script>
```

其二，main.js是我自建文件，代码如下：
```js
var myApp=angular.module('myModule', ['ui.bootstrap']);
myApp.controller('myCtrl',function($scope){
     $scope.reset=function(){ //表单重置
          $scope.user={account:'',email:''};
     }
});
```

这里我们向module中注入了ui.bootstrap，这样就可以用angular-ui去做一些事情了。好了，到这里，我们前期的准备就做好了。

①隐藏\显示错误提示消息；
```html
<form  id="css_form"class="form-horizontal" novalidate name="myform" role="form" >
     <div class="form-group">
    <label for="inputAccount" class="col-sm-2 control-label">账号：</label>
    <div class="col-md-2">
      <input type="number"  ng-model="user.account" min="3" max="6"  name="myAccount" required   class="form-control"  id="inputAccount" placeholder="请输入3-6的整数"/>
        </div>
     <div class="input-required">*</div>
     /*输入框*/
     <div class="alert alert-danger well-sm" ng-show="myform.myAccount.$error.required">不能为空！</div>
     <div class="alert alert-danger well-sm" ng-show="myform.myAccount.$error.min || myform.myAccount.$error.max">
     输入值必须在3-6之间!</div>
  </div>
</form>
```
{% alert info %}
注意：就像上述代码所展示的，我需要预判断用户可能输入的错误类型，然后置于div中，通过ng-show的true/false来显示/隐藏。
{% endalert %}

这里出现了一个$error属性，你需要记住的就是$error对象包含了某个特定表单所有的验证信息以及表单是否合法。如果某个验证失败，则这个属性返回true,相反如果验证通过,则这个属性返回false。

②高亮输入框具体实现代码：
首先，我们来设置输入框高亮显示的样式，代码如下：
```css
<style>
      #css_form input.ng-invalid.ng-dirty {
           background-color: #FFC0CB;
      }
      #css_form input.ng-valid.ng-dirty {
           background-color: #78FA89;
      }
</style>
```
接下来，我们需要搞出一个表单，包含两个输入框和两个按钮（为了代码简洁，我只保留一个输入框），具体代码如下：
```html
<form  id="css_form"class="form-horizontal" novalidate name="myform" role="form" >
     <div class="form-group">
    <label for="inputAccount" class="col-sm-2 control-label">账号：</label>
    <div class="col-md-2">
/*输入框*/
      <input type="text"  ng-model="user.account" ng-pattern="/^[0-9]{6}$/"  name="myAccount" required   class="form-control"  id="inputAccount"
placeholder="请输入6位的数字账号"/>
        </div>
     <div class="input-required">*</div>
  </div>
/*按钮组*/
<div class="form-group">
    <div class="col-sm-offset-2 col-sm-10">
      <button  class="btn btn-default"  ng-disabled="!myform.$valid">提交</button>
       <button class="btn btn-default" ng-click="reset()">重置</button>
    </div>
  </div>
</form>
```

注意：
从上面代码可以看出，我在form中加了id="css_form"和name="myform"这两个属性，同时在提交按钮中添加了ng-disabled="!myform.$valid"。这就够了，剩下的angular全部为我们搞定了。当然，前提是你的body中也这么写<body ng-app="myModule" ng-controller="myCtrl">，之前的笔记有说明过，这样用来确定angular的操作范围，这里就不赘述了。

你可能会疑问，valid、dirty和invalid这又是什么？不过没关心，下面我会具体阐述。

#### 三、参数说明
①valid、invalid、pristine和dirty
valid标记表单元素有效；
invalid标记表单元素无效；
pristine表示表单元素是纯净的，用户未操作过；
dirty表示表单元素是已被用户操作过；

②更改css属性
.ng-pristine {}
.ng-dirty {}
.ng-valid {}
.ng-invalid {}

正如上面示例二所示，我可以预先定义表单验证的表现方式。当然，这些类名是不能更改的，我们只能去覆盖父类属性。

#### 四、注意事项
①novalidate
标准浏览器如火狐，谷歌等对HTML5有很好的支持。众所周知，HTML5中input的type属性已经具备了验证功能。如果你要自己定义验证方式，那么请加上novalidate属性，以此避开浏览器自行验证。

②type类型
HTML5的type属性可以包含text、email、number等，但是angular又内部重写了这些属性，所以放心大胆的去用吧，angular完全可以满足你所有的验证。

③type="number"还是ng-pattern="/^[0-9]{6}$/"
你可以使用type="number"来限制输入框只能输入数字，当然你也可以用ng-pattern来验证用户输入，从而过滤掉非数字输入。这完全取决于你的爱好，没有硬性规定，只是选择多一些罢了。

参考文章：

AngularJS官方API：https://docs.angularjs.org/









