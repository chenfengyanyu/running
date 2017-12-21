---
title: Markdown语法浅析
date: 2015-07-5 09:13:07
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/blog_2016127153335.png
thumbnailImagePosition: left
tags: 
- Markdown
comments: true
metaAlignment: center
categories: 技术博文
---
不管是github上的readme，还是hexo中的page，都离不开一个后缀名为.md的文件。对，这就是Markdown语言。怎么总结呢，我们搬过来百度百科的一句话：[不好意思，这里要换行了😊]
<!--more-->
> Markdown是一种可以使用普通文本编辑器编写的标记语言，通过简单的标记语法，它可以使普通文本内容具有一定的格式。

显然，三言两语并不能清楚解释清楚，那么我们用六言四语来试试呗：

> Markdown 语法的目标是：成为一种适用于网络的书写语言。

> Markdown 不是想要取代 HTML，甚至也没有要和它相近，它的语法种类很少，只对应 HTML 标记的一小部分。
> Markdown 的构想不是要使得 HTML 文档更容易书写。在我看来， HTML 已经很容易写了。
> Markdown 的理念是，能让文档更容易读、写和随意改。HTML 是一种发布的格式，Markdown 是一种书写的格式。就这样，Markdown 的格式语法只涵盖纯文本可以涵盖的范围。

> 不在 Markdown 涵盖范围之内的标签，都可以直接在文档里面用 HTML 撰写。不需要额外标注这是 HTML 或是 Markdown；只要直接加标签就可以了。


可以看到，秘密麻麻的一大堆文字说明，对我们并没有什么意义［不要告诉我为什么，只要告诉我怎么做］。那么我们直接从官网来抽象出一些常用的语法，方便日常查询，代码仅供参考哦～

#### 一、空行
只包含空格和制表符，默认显示为空行；

#### 二、最高阶标题
``========``

#### 三、第二阶标题
``---------------``

#### 四、h1-h6
```
# h1
## h2
### h3
```

#### 五、区块引用
```
>dddddd
```

#### 六、列表
```
* Red
+ Red
- Red
1.Red
```

#### 七、分隔线
在一行中用三个以上的星号、减号、底线来建立一个分隔线，行内不能有其他东西
```
***
* * *
- - -
---------------
```

#### 八、链接
要建立一个行内式的链接，只要在方块括号后面紧接着圆括号并插入网址链接即可，如果你还想要加上链接的 title 文字，只要在网址后面，用双引号把 title 文字包起来即可
```
This is [an example](http://example.com/ "Title") inline link.
[This link](http://example.net/) has no title attribute.
```

#### 九、强调
Markdown 使用星号（*）和底线（_）作为标记强调字词的符号，被 * 或 _ 包围的字词会被转成用 ``<em>`` 标签包围，用两个 * 或 _包起来的话，则会被转成 ``<strong>``
```
*em*
_em_
**strong**
__strong__
```

#### 十、代码
｀printf( )｀
<code>printf( )</code>

#### 十一、图片
```
![Alt text](/path/to/img.jpg “Optional title")
```
#### 十二、换行
在插入处先按入两个以上的空格然后回车。

#### 十三、嵌入代码
```sh
```sh
npm install react react-dom
```
```js
```js
var React = require('react');
var ReactDOM = require('react-dom');

class MyComponent extends React.Component {
  render() {
    return <div>Hello World</div>;
  }
}

ReactDOM.render(<MyComponent />, node);
```
#### 十四、也支持html语法
```html
<p align="center">
  <a href="http://www.askgreenstone.com/">
    <img height="300" width="300" src="http://transfer.green-stone.cn/greenStoneicon300.png">
  </a>
</p>
```
参考：
http://www.appinn.com/markdown/
http://wowubuntu.com/markdown/




