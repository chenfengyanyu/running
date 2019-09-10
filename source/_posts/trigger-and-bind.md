---
title: trigger和bind
date: 2015-08-29 21:37:04
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/animal_20140328160351.png
tags: 
- js
comments: true
metaAlignment: center
categories: 技术博文
---
#### bind方法
bind() 方法为被选元素添加一个或多个事件处理程序，并规定事件发生时运行的函数。
<!--more-->
```javascript
$("button").bind("click",function(){
  $("p").slideToggle();
});
```
必须是有效的事件

#### trigger方法
trigger() 方法触发被选元素的指定事件类型。
```javascript
$("button").click(function(){
  $("input").trigger("select");
});
```
可以使自定义事件（使用 bind() 函数来附加），或者任何标准事件。
```javascript
$( "#foo" ).on( "click", function() {
  alert( $( this ).text() );
});

$( "#foo" ).trigger( "click" );

$( "#foo" ).on( "custom", function( event, param1, param2 ) {
  alert( param1 + "\n" + param2 );
});

$( "#foo").trigger( "custom", [ "Custom", "Event" ] );
```

先占个坑，后续再来完善～











