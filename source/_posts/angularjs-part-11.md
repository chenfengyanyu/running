---
title: Angularjs学习笔记10~指令 Directive（三）自定义验证实例
date: 2014-05-03 14:46:14
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_155IK010-0.jpg
thumbnailImagePosition: left
tags: 
- angularjs
comments: true
metaAlignment: center
categories: 技术博文
---
回头看看，断断续续已经连续写了10篇angularjs的总结笔记了，虽说不上经典，但是我已经竭尽全力了。本来想着，已经接手做了很多项目了，应该很容易总结。可是，事与愿违。真正动手写起来，确实困难重重。基础不牢靠、表达不清、坚持不下来都成了阻力。
<!-- more -->
好在，我一直在前进，三分之二的路程已经走完。虽然艰辛，但是充满了挑战。我能想象到未来的某个时间，再去回首这个过程，定会为自己的坚持所感动。呵呵，其他都不重要，重要的是我坚持下来了，不是吗？后续还有五篇，我会再接再厉。

指令分了三部分，为了避免过于零散，这里再次把目录列出来，以供参考：
其一，指令 Directive（一）初探
其二，指令 Directive（二）参数定义
其三，指令 Directive（三）自定义验证实例

{% alert success %}
这篇笔记可能有点长，我也不是一次写完的，只要你能耐着性子看下去，肯定会受益匪浅。其他就不多说了，下面进入正题：
{% endalert %}


#### 一、用指令来扩展
第一篇关于指令的笔记中给出了两种验证方式，一般的情况下，我们可以拿来直接用。但是，如果一些特殊需求，或者想展示一些个性化的验证方式，那么我们就需要自己来扩展验证了。

为了更好的说明问题，这里我用指令来做出一个类似如下图的验证效果。当然，纯粹是为了介绍一下如何使用指令，所以只做出了简单效果。如果你真打算这么用，希望你能够进行完善。
![directive](http://7xvi3w.com1.z0.glb.clouddn.com/angular_ya.gif)

#### 二、自定义验证示例
上面用动态图演示了一个用指令写出的验证效果，那我们来看看代码是如何实现的。

①引用文件，看效果，我们很容易能看出Bootstrap的影子。对的，angularjs的一个ui框架是使用Bootstrap的，所以下列文件必不可少：
```html
<link href="bootstrap.css" rel="stylesheet" type="text/css" />
<script type='text/javascript' src='angular.js'></script>
<script type='text/javascript' src='ui-bootstrap-tpls-0.10.0.js'></script>
```
除此之外，我又添加了另外两个js文件：
```html
<script type='text/javascript' src='main.js'></script>
<script type='text/javascript' src='nbvalidate.js'></script>
```
其中main.js为主文件，主要去构建angularjs的作用域。nbvalidate.js这个是用指令扩展的验证插件。这里，可能你有些疑问，为什么要分两个文件来做这件事，一个岂不是更好？

其实，前面已经提及过，指令很大的一个优势是去做通用插件，这里我想把验证作为一个插件。当你需要进行验证，只需要把nbvalidate.js引入，nbvalidate作为依赖项，验证就可以了用了，是不是很方便？

②html结构
```html
<div ng-app="myModule" ng-controller="myCtrl">
     <form class="form-horizontal" name="myform" role="form" >
     <div class="form-group">
    <label for="inputAccount" class="col-sm-2 control-label">账号：</label>
    <div class="col-md-2">
      <input type="text"  ng-model="user.account"  name="myAccount"   class="form-control"        id="inputAccount" placeholder="请输入数字账号"/>
       <my-validate ztype="account" zmin="3" zmax="6" zcontent="user.account"  ng-show="!myform.myAccount.$pristine"></my-validate>
    </div>
     <div class="input-required">*</div>
  </div>
  </form>
</div>
```
代码很简单，主要就是一个输入框的UI。需要注意的是上述代码中粗体部分，我们单独拿出来做分析:
```html
<my-validate ztype="account" zmin="3" zmax="6" zcontent="user.account"  ng-show="!myform.myAccount.$pristine"></my-validate>
```
这里就是自定义指令的html核心部分，可以看到我创建了一个my-validate元素标签，当你需要对输入框进行验证的话，插入此段代码，基本就搞定了。

除此之外，里面还有一些自定义属性，如：
- ztype=“account”：告知js逻辑，此输入框为account验证；
- zmin=“3”：限制输入个数最少为3；
- zmax=“6”：限制输入个数最多为6；
- zcontent=“user.account”：这里取出与之绑定的输入框的值；
- ng-show=“！myform.myAccount.$pristine”：控制气泡的显示，如果用户对输入框未操作，则不显示气泡。

可以看出，基本全是自定义的元素和属性，那么它们是如何工作的呢？下面来看看两个主要的js文件，main.js和nbvalidate.js。

③js文件们
main.js:
```js
var myApp=angular.module('myModule', ['ui.bootstrap','nbValidate']);
myApp.controller('myCtrl',function($scope){
     $scope.user={
          //篇幅原因，无关字段已经去掉
          account:''
     }
});
```
上面代码大家应该不会陌生，创建module和controller，并且初始化了一个user对象，该对象包含account属性（这是我用来演示验证的字段）。需要注意的是代码加粗部分两个依赖项，
其一，ui.bootstrap是页面ui，前面已经提过。
其二，自定义的js文件nbValidate，很显然重要的验证逻辑都会包含在此依赖项中。

nbValidate.js
```js
angular.module('nbValidate',[])
     .directive('myVaildate',function(){//验证指令
     return{
          restrict:'AE', //指令在模板中的使用方式为属性或者元素
          transclude:true,//把指令元素中原来的子节点移动到一个新模板内部
          scope:{inputVal:"=zcontent"},//绑定当前zcontent属性，从而获取用户输入
          templateUrl:'temlate.html',//模板文件temlate.html，这里特指弹出的气泡html结构
          link:function(scope,element,attrs){
               //长度限制预先定义
               var min=attrs.zmin?attrs.zmin:4;
               var max=attrs.zmax?attrs.zmax:20;
            //账号验证规则
               var patternAccount=new RegExp("^([0-9]){"+min+","+max+"}$");
               //模板的显示/隐藏控制，以及气泡中的错误提示
               scope.displayFlag="none";
               scope.validateContent="";

                    scope.$watch('inputVal',function(){//用watch来监控zcontent属性值的变化
                    if(scope.inputVal.length){
                         switch(attrs.ztype){
                              case "account"://账号
                                        if(!patternAccount.test(scope.inputVal)){
                                             scope.displayFlag="block";//不能通过验证则显示错误提示气泡
                                             if(!patternNumber.test(scope.inputVal)){
                                                 scope.vaildateContent="账号只能包含数字！";
                                             }
                                             else if(scope.inputVal.length<min&&
                                                       scope.inputVal.length>0){
                                                  scope.vaildateContent="账号必须大于"+min+"位！";
                                             }
                                             else if(scope.inputVal.length>max){
                                                  scope.vaildateContent="账号不能超过"+max+"位！";
                                             }
                                        }
                                        else{
                                             scope.displayFlag="none";
                                        }
                                   break;
                              //邮箱
                        //与账号类似，依次罗列其他验证逻辑
                              }
                    });
               }
          }
});
```

这段代码有些长，相对比较复杂，代码中我已经做了注释，仔细看应该不难理解。这里，我做了两件事：
其一，用正则去匹配用户输入；
其二，根据验证结果去选择隐藏或显示模板文件；

{% alert danger %}
如果你还是看不懂，希望你能够先去看看[《指令 Directive（二）参数定义》](http://jartto.wang/2014/04/28/angularjs-part-10/)这个笔记，回过头再来看本篇笔记，难度就会小很多。学知识是循序渐进的，掌握好基础知识，后续就可以畅通无阻了。
{% endalert %}

接下来，进入到最后一个环节，我们来看看模板文件吧！

④模板文件（验证气泡）
temlate.html
```html
<div class="tooltip top fade in"  is-open="tt_isOpen" animation="tt_animation" placement="top"  content="{{validateContent}}" title="" tooltip-popup=""
style="top: -33px;display: {{displayFlag}};">
     <div class="tooltip-arrow"></div>
     <div class="tooltip-inner ng-binding" ng-bind="content"></div>
</div>
```

可以看出这段代码无非就是一些气泡的样式。我们还是来聚焦一下代码中的粗体部分，validateContent用来将错误信息填充到气泡中，displayFlag用来控制气泡的隐藏和显示。

#### 三、总结
到这里，关于指令一些基本用法，以及如何创建自定义指令，基本就完事了。我用三篇文章来梳理指令相关知识，确实有些乏力，不过达到入门级别应该是没问题了。如果你想深入学习，angularjs官方api应该是个不错的选择。

当然，我也是初学，有的地方可能理解的不是很清楚。如果文中有错误，希望作为读者的你能够指出来。

{% alert info %}
由于实际代码比较多，我只取出了一小部分来做说明。如果你需要完整的demo，请留下邮箱，我会抽空发给你的。
{% endalert %}









