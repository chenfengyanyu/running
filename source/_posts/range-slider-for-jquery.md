---
title: jQuery 插件 range.slider
date: 2017-11-01 23:25:53
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/jquery-slider.png
thumbnailImagePosition: left
tags: 
- jquery
- range
- slider
comments: false
metaAlignment: center
categories: 技术博文 
---
在查找一滑动条插件的时候，发现了一款 `jQuery` 支持的插件 `range.slider`，效果非常不错。于是通读了使用文档，本文算是一个插件积累吧，以备不时之需。
<!-- more -->
#### 一、先看效果
![demo](http://7xvi3w.com1.z0.glb.clouddn.com/range.png)

#### 二、简单示例
看完效果，我们来看看如何使用：
1.下载安装
```bash
# bower
bower install ionrangeslider

# npm
npm install ion-rangeslider
```
{% alert info %}
建议通过 `bower` 下载，这样可以看到所有的资源文件，包括依赖的 jQuery 文件。
{% endalert %}

2.`slider` 重写了 `input` 元素，所以你需要先添加一个 `input`：
```html
<input type="text" id="example_id" name="example_name" value="" />
```
然后根据元素 `id` 来初始化 `slider`：
```js
$("#example_id").ionRangeSlider();
```
好了，刷新页面看看，是不是很简单就使用了。

#### 三、参数详解
要简单的使用，那上面就满足需求了。但是在实际开发中，可能需要我们了解更多参数配置，丰富我们的使用场景。
- type: 控制手柄 string 类型
  - "single" || "double" 
- min: 最小值 number 类型
  - 10
- max: 最大值 number 类型
  - 100
- from: 起点 number 类型
  - min
- to: 最大值 number 类型
  - max
- step: 步进值 number 类型
  - 1
- prefix: 前缀 string 类型
- postfix: 后缀 string 类型
- max_postfix: 最大值后缀
- grid: 是否显示标尺
- grid_num: 标尺单位
...

更多请[查看文档](http://ionden.com/a/plugins/ion.rangeSlider/en.html)

#### 四、高级用法
对参数有了了解，下面列举一些复杂的使用场景：
1.传入更多的对象来初始化 `slider`
```js
$("#example").ionRangeSlider({
  min: 0,
  max: 10000,
  from: 1000,
  to: 9000,
  type: 'double',
  prefix: "$",
  grid: true,
  grid_num: 10
});
```

2.可以通过 `data-*` 在 `html` 中初始化，如下：
```html
data-min="0"
data-max="10000"
data-from="1000"
data-to="9000"
data-type="double"
data-prefix="$"
data-grid="true"
data-grid-num="10"
```

3.可用方法 `update`, `reset`, `destroy`
```js
// Launch plugin
$("#range").ionRangeSlider({
    type: "double",
    min: 0,
    max: 1000,
    from: 200,
    to: 500,
    grid: true
});

// Saving it's instance to var
var slider = $("#range").data("ionRangeSlider");

// UPDATE - updates slider to any new values
slider.update({
    from: 300,
    to: 400
});

// RESET - reset slider to it's first values
slider.reset();

// DESTROY - destroys slider and restores original input field
slider.destroy();
```
{% alert info %}
调用这三个方法，务必先实例化 `ionRangeSlider`。
{% endalert %}

4.监听 `change` 事件
```js
var $range = $("#range_47");

$range.ionRangeSlider({
    type: "single",
    min: 0,
    max: 100,
    from: 50
});

$range.on("change", function () {
    var $this = $(this),
        value = $this.prop("value");

    console.log("Value: " + value);
});
```
5.`angularjs` 中使用
```html
<input type="text" name="" value="5" ng-model="demoVals" class="ion_slider" ui-jq="ionRangeSlider" ui-options="slider.options" />
```
JS 中需要初始化：
```js
$scope.slider = {
  options: {
    min: 1,
    max: 10,
    step: 0.1,
    from: 6,
    prefix: '直径 ',
    postfix: ' 公里',
    max_postfix: '+',
    grid: true,
    onFinish: function (event) { 
      // Todo
    }
  }
}
```


#### 五、事件支持
```js
$("#range_43").ionRangeSlider({
    type: "single",
    min: 0,
    max: 100,
    from: 50,
    keyboard: true,
    onStart: function (data) {
        console.log("onStart");
    },
    onChange: function (data) {
        console.log("onChange");
    },
    onFinish: function (data) {
        console.log("onFinish");
    },
    onUpdate: function (data) {
        console.log("onUpdate");
    }
});
```

#### 六、注意事项
1.进度条是图片，所以如果你对配色不满意，只能替换图片了。
2.`rangeSlider` 支持换肤，需要下载不同的 `css` 皮肤文件以及对应的 `JS` 文件，如：FlatUI, Modern, HMTL5, Nice White, Dark 等。

#### 七、资源网站
[rangeSlider](http://ionden.com/a/plugins/ion.rangeSlider/en.html)
[demo page](http://ionden.com/a/plugins/ion.rangeSlider/demo_interactions.html)



















