---
title: 掌握js模板引擎
date: 2016-09-15 11:16:10
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/js_2.png
thumbnailImagePosition: left
tags: 
- js
- template
- 模板引擎
comments: true
metaAlignment: center
categories: 技术博文
---
最近要做一个小项目，不管是使用angularjs还是reactjs，都觉得大材小用了。其实我可能只需要引入一个jquery，但想到jquery对dom的操作，对于早已习惯了双向绑定模式的我，何尝不是一种痛苦。
<!-- more -->
听过这样一句话：“技术没有缺席，只有姗姗来迟”，很多技术自己不知道，并非没有。今天我想介绍的就是一个简单的js模板引擎artTemplate，让我们扬帆起航吧～

#### 一、概述
artTemplate 是新一代 javascript 模板引擎，它采用预编译方式让性能有了质的飞跃，并且充分利用 javascript 引擎特性，使得其性能无论在前端还是后端都有极其出色的表现。

#### 二、我的痛点
我们先来看一段代码，当我用ajax从后台拿到一堆json数据，要填充进页面的时候，可能会这么做：
```js
$.ajax({
    type: 'GET',
    url: '/server/list.json',
    success: function(data) {
        var lists = '';
        for(var i=0;i<data.content.rows.length;i++){
          lists += '<li><div class="lititle">'+data.content.rows[i].leaveRuleName+'（共计 '+data.content.rows[i].durationStr+'）</div><div class="timer"><span>开始时间：'+data.content.rows[i].startTime+'</span><span>结束时间：'+data.content.rows[i].endTime+'</span></div><div class="btns"><p><a href="#" class="button button-round blue">'+data.content.rows[i].customApprovalStatus+'</a></p></div><div class="info">刚刚申请</div></li>';
         }
         $('#myList').append(lists);
    },
    error: function(xhr, type) {//此处省略}
})
```
html代码：
```html
<ul class="list-container" id="myList"></ul>
```
这段代码，功能上可能并没有什么问题。但是我们来看lists变量，这里面的信息已经爆炸了，html结构以及data填充，写完我就不想维护了。

正应了那句话：“此时我写的代码，只有我和天知道。半个月后，恐怕只有天知道了！”

#### 三、处理痛点
这岂止是一场噩梦，简直就是在挖坑埋雷🙈。好了，不再矫情了，我们换artTemplate来试试，代码可能是这样：
{% alert info %}
注意：记得提前引入template.js哦～
{% endalert %}
```js
$.ajax({
    type: 'GET',
    url: '/server/list.json',
    success: function(data) {
          $('#myList').append(template('myListTpl', { lists: data.content.rows}));
    },
    error: function(xhr, type) {//此处省略}
})
```
html代码：
```html
<script type="text/html" id="myListTpl">
    {{each lists}}
    <li data-id="{{$value.id}}">
        <div class="lititle">{{$value.overtimeRuleName}}（共计 {{$value.durationStr}}）</div>
        <div class="timer">
            <span>开始时间：{{$value.startTime}}</span>
            <span>结束时间：{{$value.endTime}}</span>
        </div>
        <div class="btns">
           <p><a href="#" class="button button-round {{!$value.isEnsure?'blue':($value.isEffective?'green':'red')}}">{{!$value.isEnsure?'待确认':($value.isEffective?'已确认':'已失效')}}</a></p>
        </div>
        <div class="info">{{$value.applyTime | timeFormat}}申请</div>
    </li>
    {{/each}}
</script>
<ul class="list-container" id="myList"></ul>
```
{% alert info %}
注意：script中的type="text/html"
{% endalert %}
这段代码很清晰，我们建立了myListTpl作为模板，里面的html结构清晰，变量包在了双大括号中。那么lists中的值是怎么来的，细心的你可能看到了这个：
```js
$('#myList').append(template('myListTpl', { lists: data.content.rows}));
```
对，我们用template去预编译模板，同时将数据塞进了lists中。就这样，js中不需要很复杂的逻辑，html也保存了原有的结构。

～嗡～嗡，嗯，整个世界都清静了！

#### 四、用法示例
{% alert info %}
artTemplate用起来很简单，我们来逐一举例说明：
{% endalert %}
1.template(id,data)，向id模板传入数据。
```html
<script id="test" type="text/html">
<h1>{{title}}</h1>
<ul>
    {{each list as value i}}
        <li>索引 {{i + 1}} ：{{value}}</li>
    {{/each}}
</ul>
</script>
```
js代码：
```js
var data = {
    title: '标签',
    list: ['文艺', '博客', '摄影', '电影', '民谣', '旅行', '吉他']
};
var html = template('test', data);
document.getElementById('content').innerHTML = html;
```
2.template.compile(source,options)，预编译模板数据。
```js
<script>
var source = '<ul>'
+    '{{each list as value i}}'
+        '<li>索引 {{i + 1}} ：{{value}}</li>'
+    '{{/each}}'
+ '</ul>';

var render = template.compile(source);
var html = render({
    list: ['摄影', '电影', '民谣', '旅行', '吉他']
});

document.getElementById('content').innerHTML = html;
</script>
```
{% alert info %}
上面两种方式，属于简单的数据渲染，实际应用中我们可能会碰到比较复杂的操作，下面细说。
{% endalert %}

3.template.helper(name,callback)，在模板中使用自定义方法。
某种情况下，我们需要对数据做一下预处理，类似angularjs中的filter操作，那怎么办呢？
```html
<script id="test" type="text/html">
{{time | timeFormat}}
</script>
```
```js
template.helper('timeFormat',function(date){
    var result = Common.formateTime(date);
    return result;
});
```
如上所示，我们调用了template.helper方法，将自定义方法放在回调函数中，那么数据是不是可以进行处理了呢。这里我的场景是对时间数据做了格式化处理，我希望他显示成“1小时前，刚刚”之类的文案。

4.template.config(name,value)，更改引擎的默认配置。
![配置](http://7xvi3w.com1.z0.glb.clouddn.com/js_4.png)

5.include可嵌套子模板。
```html
<div id="content"></div>
<script id="test" type="text/html">
<h1>{{title}}</h1>
{{include 'list'}}
</script>
<script id="list" type="text/html">
<ul>
    {{each list as value i}}
        <li>索引 {{i + 1}} ：{{value}}</li>
    {{/each}}
</ul>
</script>
```
js代码：
```js
var data = {
	title: '嵌入子模板',
	list: ['文艺', '博客', '摄影', '电影', '民谣', '旅行', '吉他']
};
var html = template('test', data);
document.getElementById('content').innerHTML = html;
```
重点在于：
```js
{{include 'list'}}
```
将id为list的模板包含进来，很简单，这里就不细说了。

6.转义
```html
<div id="content"></div>
<script id="test" type="text/html">
	<p>不转义：{{#value}}</p>
	<p>默认转义： {{value}}</p>
</script>
```
js代码：
```js
var data = {
    value: '<span style="color:#F00">hello world!</span>'
};
var html = template('test', data);
document.getElementById('content').innerHTML = html;
```
{% alert info %}
我们只需要加上“＃”号，模板默认就不转义啦。
{% endalert %}

7.在页面上打印你的数据。
当然，你还可以这么玩：
```html
<script id="test" type="text/html">
{{print a b c}}
</script>
```
js代码：
```js
var html = '';
var data = {
	a: 'hello',
	b: '--world',
	c: '--!!!'
};
html = template('test', data);
document.write(html);
```
基本用法就这些了，是不是觉得很简单呢，那就赶快试试吧！

#### 五、原理剖析
当然，我们并不会只停留在会用的层次上面，模板引擎又是如何工作的呢？

artTemplate 模板编译器会根据一些简单的规则提取好所有模板变量，声明在渲染函数头部，这个函数类似：
```js
var render = function($data){
	var content = $data.content,
		$out = '';
	$out += '<h3>';
	if(typeof content === 'string'){
		$out += content;
	}
	$out += '</h3>';
	return $out;
};
```
这个自动生成的函数就如同一个手工编写的 javascript 函数一样，同等的执行次数下无论 CPU 还是内存占用都有显著减少，性能近乎极限。

值得一提的是：artTemplate 很多特性都基于预编译实现，如沙箱规范与自定义语法等。

虽然每个引擎从模板语法、语法解析、变量赋值、字符串拼接的实现方式各有所不同，但关键的渲染原理仍然是动态执行 javascript 字符串。

嗯，点到为止吧。如果想深入了解，去下载个template.js，看看源码吧。

#### 六、负荷测试与调试
![负荷测试](http://7xvi3w.com1.z0.glb.clouddn.com/js_1.png)
在 chrome 下渲染效率测试中分别是知名引擎 Mustache 与 micro tmpl 的 25 、 32 倍。
除了性能优势外，调试功能也值得一提。模板调试器可以精确定位到引发渲染错误的模板语句，解决了编写模板过程中无法调试的痛苦，让开发变得高效，也避免了因为单个模板出错导致整个应用崩溃的情况发生。示例如下：
```html
<script id="test" type="text/html">
<ul>
{{each list}}
{{/each}}

{{window.alert=null}}
</ul>
</script>
```
js代码：
```js
var html = '';
html = template('test', {});
document.write(html);
```
list变量并没有赋值，模板引擎会将错误抛出，方便定位与查错。

#### 七、意外收获
原本我以为artTemplate会是最快的，结果又发现了doT，很好，你已经成功引起了我的注意。
![doT](http://7xvi3w.com1.z0.glb.clouddn.com/js_3.png)
正如大家所见，js模板引擎五花八门，如果每个引擎写一篇文章，我想几年内我应该不用干其他事了（我才不会这么无聊呢👻）。so，不要纠结到底使用哪个模板引擎。请记住，适合自己的才是最好的。

#### 八、总结
按照学习思路，从上到下有了这篇文章。之所以想要详细的去写一篇关于模板引擎的文章，主要是想弥补心里的内疚。其实早在2014年我就有听说过artTemplate，但却只止步于听过的程度。

当地球人都在用的时候，我还没有开始，当大家已经去追逐天边的云彩，我才反应过来。

血淋淋的教训，谨以此献给和我有同样遭遇以及感受的人们，任何时间请不要停下学习的脚步，哪怕晚了，但至少你努力过。

#### 九、参考文章
1.官方文档：
https://github.com/aui/artTemplate
2.快速上手：
https://github.com/aui/artTemplate
3.原理解析：
http://cdc.tencent.com/2012/06/15/高性能javascript模板引擎原理解析/
4.官方示例：
https://github.com/aui/artTemplate/tree/master/demo

































































