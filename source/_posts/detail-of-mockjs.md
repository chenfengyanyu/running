---
title: 细说 Mockjs
date: 2017-01-05 10:18:40
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/mockjs0.png
thumbnailImagePosition: left
tags: 
- mockjs
comments: false
metaAlignment: center
categories: 技术博文 
---
随着技术的不断发展，前后端分离已成为 Web 的标准。前端工程师需要更加独立的去做一些事情，别再傻傻的等后端工程师的 API 文档了，确定需求，我们就可以开始了。
<!-- more -->
文章将列举日常开发中常用的 Mock 用法，更多扩展请参考：[代码示例](http://mockjs.com/examples.html)。
#### 一、引入方式
1.引入 `js` 文件：
```html
<script src="http://mockjs.com/dist/mock.js"></script>
```
2.`node` 引入及示例：
```js
// 安装
npm install mockjs

// 使用
var Mock = require('mockjs');
var data = Mock.mock({
    'list|1-10': [{
        'id|+1': 1
    }]
});
console.log(JSON.stringify(data, null, 4))
```
3.`bower` 安装以及引入：
```js
npm install -g bower
bower install --save mockjs
```
`HTML` 引入注意路径：
```
<script type="text/javascript" src="./bower_components/mockjs/dist/mock.js"></script>
```
4.RequireJS (AMD)
```js
// 配置 Mock 路径
require.config({
  paths:{
    'mock':'http://mockjs.com/dist/mock'
  }
})
// 加载 Mock
require(['mock'], function(Mock){
    // 使用 Mock
    var data = Mock.mock({
        'list|1-10': [{
            'id|+1': 1
        }]
    });
    $('<pre>').text(JSON.stringify(data, null, 4))
        .appendTo('body')
})
```
5.Sea.js (CMD)
```js
seajs.config({
  alias: {
    "mock": "http://mockjs.com/0.1/dist/mock.js"
  }
})

// 加载 Mock
seajs.use('mock', function(Mock){
    // 使用 Mock
    var data = Mock.mock({
        'list|1-10': [{
            'id|+1': 1
        }]
    });
    $('<pre>').text(JSON.stringify(data, null, 4))
        .appendTo('body')
})
```

#### 二、字符串操作
1.生成随机长度字符串 `'name|min-max': string`:
```js
Mock.mock({
  "string|1-10": "★"
})
```
2.生成指定字符串长度， `'name|count': string`:
```js
Mock.mock({
  "string|3": "★★★"
})
```
{% alert info %}
此处 count 为指定字符串循环次数。
{% endalert %}

#### 三、Number 类型
1.生成 1－100 之间的整数，`'name|min-max': number`：
```js
Mock.mock({
  "number|1-100": 100
})
```
2.生成 1-100 间个数小于 10 位的小数，`'name|min-max.dmin-dmax': number`：
```js
Mock.mock({
  "number|1-100.1-10": 1
})
```
3.生成大于 123.1 且位数小于 10位的小数：
```js
Mock.mock({
  "number|123.1-10": 1
})
```

#### 四、布尔类型
1.随机生成 boolen 类型的属性，`'name|1': boolean`：
```js
Mock.mock({
  "boolean|1": true
})
```

#### 五、生成对象
1.从指定对象中随机选择两条数据，`'name|count': object`：
```js
Mock.mock({
  "object|2": {
    "310000": "上海市",
    "320000": "江苏省",
    "330000": "浙江省",
    "340000": "安徽省"
  }
})
```
输出：
```js
{
  "object": {
    "320000": "江苏省",
    "330000": "浙江省"
  }
}
```
2.选择 2-4 位指定位数属性，`'name|min-max': object`:
```js
Mock.mock({
  "object|2-4": {
    "110000": "北京市",
    "120000": "天津市",
    "130000": "河北省",
    "140000": "山西省"
  }
})
```
#### 六、数组操作
1.随机选择给定数组中某 1 项，`'name|1': array`：
```js
Mock.mock({
  "array|1": [
    "AMD",
    "CMD",
    "UMD"
  ]
})
```
结果：
```js
{
  "array": "CMD"
}
```
2.在给定数组中选择 1 项，生成 1-10 次：
```js
Mock.mock({
  "array|1-10": [
    {
      "name|+1": [
        "Hello",
        "Mock.js",
        "!"
      ]
    }
  ]
})
```
3.随机生成 1-10 个给定数组项，｀'name|min-max': array｀：
```js
Mock.mock({
  "array|1-10": [
    "Mock.js"
  ]
})
```
4.将数组项复制 1-10 次：
```js
Mock.mock({
  "array|1-10": [
    "Hello",
    "Mock.js",
    "!"
  ]
})
```
5.拷贝数组项三次，`'name|count': array`：
```js
Mock.mock({
  "array|3": [
    "Mock.js"
  ]
})
```
同理：
```js
Mock.mock({
  "array|3": [
    "Hello",
    "Mock.js",
    "!"
  ]
})
```
结果为：
```js
{
  "array": [
    "Hello",
    "Mock.js",
    "!",
    "Hello",
    "Mock.js",
    "!",
    "Hello",
    "Mock.js",
    "!"
  ]
}
```

#### 七、调用方法
1.可以将方法作为返回值：
```js
Mock.mock({
  'foo': 'Syntax Demo',
  'name': function() {
    return this.foo
  }
})
```
结果：
```js
{
  "foo": "Syntax Demo",
  "name": "Syntax Demo"
}
```

#### 八、正则表达式
1.按照正则规则生成字符串：
```js
Mock.mock({
  'regexp': /[a-z][A-Z][0-9]/
})
```
结果为：
```js
{
  "regexp": "kN6"
}
```
2.生成 5-10 位数字：
```js
Mock.mock({
  'regexp': /\d{5,10}/
})
```

#### 九、数据占位符定义
1.生成 boolean 类型：
```js
Random.boolean() // true
Mock.mock('@boolean') // false
Mock.mock('@boolean()') // false
```
2.生成 60-100 间的整数：
```js
Random.integer(60, 100)
```
3.生成 60-100 之间浮点数，且小数点后保留 3-5 位：
```js
Random.float(60, 100, 3)
Random.float(60, 100, 3, 5)
```
4.生成步近值为 2 的数组序列：
```js
Random.range(1, 10, 2)
```
结果：
```js
[1,3,5,7,9]
```

#### 十、生成日期格式字符串
1.生成日期：
```js
Random.date('yyyy-MM-dd') // "2008-04-11"
Mock.mock('@date("yyyy-MM-dd")') // "1979-05-03"
```
2.生成时间：
```js
Random.time('A HH:mm:ss') // "PM 16:21:17"
Random.time('HH:mm:ss') // "12:20:39"
```
3.生成日期和时间：
```js
Random.datetime('yyyy-MM-dd A HH:mm:ss') // "2008-01-18 PM 21:45:12"
Mock.mock('@datetime("yyyy-MM-dd A HH:mm:ss")') // "2005-08-01 AM 00:07:46"
```
4.当前时间：
```js
Random.now() // "2017-12-03 19:02:13"
Mock.mock('@now') // "2017-12-03 19:02:13"
Mock.mock('@now()') // "2017-12-03 19:02:13"
```

#### 十一、占位图片
1.`Random.image( size?, background?, foreground?, format?, text? )`:
```js
// 200*100 的图片
Random.image('200x100') 

// 生成背景色为 #50B347 前景色为 #FFF，写着 jartt 的图片
Random.image('200x100', '#50B347', '#FFF', 'jartt') 

// 还可以指定生成的图片格式，如：png
Random.image('200x100', '#894FC4', '#FFF', 'png', 'jartto')
```
#### 十二、生成色值
1.默认生成 16 进制：
```js
Random.color() // "#79f28d"
Mock.mock('@color') // "#79f28d"
Mock.mock('@color()') // "#79f28d"
```
2.还有 rgb 和 rgba：
```js
Random.rgb() // "rgb(242, 171, 121)"
Mock.mock('@rgb')
Mock.mock('@rgb()')
```

#### 十三、生成英文/中文段落
1.随机生成 1-3 段英文段落，`Random.paragraph( min?, max? )`：
```js
Random.paragraph(1,3)
Mock.mock('@paragraph(1,3)')
```
2.随机生成 3-5 个英文句子，`Random.sentence( min?, max? )`：
```js
// Random.sentence( len )
Random.sentence(5)
Mock.mock('@sentence(5)')

// Random.sentence( min, max )
Random.sentence(3, 5)
Mock.mock('@sentence(3, 5)')
```
3.英文单词和英文标题：
```js
// Random.word( len )
Random.word(5)
Mock.mock('@word(5)')

// Random.word( min, max )
Random.word(3, 5)
Mock.mock('@word(3, 5)')
```
4.中文段落：
```js
// Random.cparagraph( len )
Random.cparagraph(2)
Mock.mock('@cparagraph(2)')

// Random.cparagraph( min, max )
Random.cparagraph(1, 3)
Mock.mock('@cparagraph(1, 3)')
```
5.中文句子：
```js
// Random.csentence( len )
Random.csentence(5)
Mock.mock('@csentence(5)')

// Random.csentence( min, max )
Random.csentence(3, 5)
Mock.mock('@csentence(3, 5)')
```
6.随机汉字：
```js
// Random.cword( min, max )
Random.cword(3, 5)
Mock.mock('@cword(3, 5)')

// Random.cword( pool, min, max )
Random.cword('零一二三四五六七八九十', 5, 7)
Mock.mock('@cword("零一二三四五六七八九十", 5, 7)')
```
结果：
```js
// Random.cword( min, max )
"活第听参"
"总算场"

// Random.cword( pool, min, max )
"一三七一二八"
"五九八三七七"
```
7.汉字标题：
```js
// Random.ctitle( len )
Random.ctitle(5)
Mock.mock('@ctitle(5)')

// Random.ctitle( min, max )
Random.ctitle(3, 5)
Mock.mock('@ctitle(3, 5)')
```

#### 十四、中英文姓名
1.生成英文名字
```js
// Random.name()
Random.name()
Mock.mock('@name')
Mock.mock('@name()')
```
结果：
```js
// Random.name()
"Patricia Clark"
"Mary Jones"
"Jose Miller"
```
2.汉语名字：
```js
// Random.cname()
Random.cname()
Mock.mock('@cname')
Mock.mock('@cname()')
```
结果：
```js
// Random.cname()
"卢明"
"黎洋"
"董芳"
```
#### 十五、地址相关
1.生成 URL：
```js
// Random.url()
Random.url() // "mailto://wiau.gp/ashkjy"
Mock.mock('@url')
Mock.mock('@url()')
```
2.生成 Email：
```js
// Random.email()
Random.email() // "h.qbhlgrab@srec.gm"
Mock.mock('@email')
Mock.mock('@email()')
```
3.生成 ip：
```js
// Random.ip()
Random.ip() // "71.1.205.35"
Mock.mock('@ip')
Mock.mock('@ip()')
```
4.生成区域：
```js
// Random.region()
Random.region() // "华南"
Mock.mock('@region') // "西北"
Mock.mock('@region()') // "西北"
```
5.生成省：
```js
// Random.province()
Random.province() // "江西省"
Mock.mock('@province')
Mock.mock('@province()')
```
6.生成城市：
```js
// Random.city()
Random.city()
Mock.mock('@city')
Mock.mock('@city()')
// Random.city( prefix )
Random.city(true)
Mock.mock('@city(true)')
```
结果：
```js
// Random.city()
"桂林市"
"海口市"
"玉溪市"
// Random.city( prefix )
"内蒙古自治区 呼伦贝尔市"
"宁夏回族自治区 银川市"
```
7.区县：
```js
// Random.county()
Random.county() // "南沙群岛"
Mock.mock('@county')
Mock.mock('@county()')
// Random.county( prefix )
Random.county(true) // "黑龙江省 齐齐哈尔市 克东县"
Mock.mock('@county(true)')
```

#### 十六、Helper
1.首字母大写：
```js
// Random.capitalize( word )
Random.capitalize('hello')
Mock.mock('@capitalize("hello")')
```
2.转大小写：
```js
// Random.upper( str )
Random.upper('hello')
Mock.mock('@upper("hello")')

// Random.lower( str )
Random.lower('HELLO')
Mock.mock('@lower("HELLO")')
```
3.数组随机取值：
```js
// Random.pick( arr )
Random.pick(['a', 'e', 'i', 'o', 'u']) // 'a'
Mock.mock('@pick(["a", "e", "i", "o", "u"])')
```
4.打乱数组项顺序：
```js
// Random.shuffle( arr )
Random.shuffle(['a', 'e', 'i', 'o', 'u']) // ["o","e","i","u","a"]
Mock.mock('@shuffle(["a", "e", "i", "o", "u"])')
```
5.生成 guid：
```js
// Random.guid()
Random.guid()
Mock.mock('@guid')
Mock.mock('@guid()')
```
6.生成 id：
```js
// Random.id()
Random.id()
Mock.mock('@id')
Mock.mock('@id()')
```

#### 十七、补充说明
文章只是展示了 `Mockjs` 的基本用法规则，更多的实用场景请参考如下链接：
[代码示例](http://mockjs.com/examples.html)
[文档地址](http://mockjs.com/0.1/)


