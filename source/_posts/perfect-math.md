---
title: 完美渲染之数学公式
date: 2018-07-08 22:59:45
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/math0.png
thumbnailImagePosition: left
tags: 
- mathjax
- latex
- katex
comments: false
metaAlignment: center
categories: 技术博文
---
我们经常会用 Office Word 在文档中插入数学公式，有没有想过，如何在你的网页上渲染数学公式呢？让我们走进数学，欣赏一下公式渲染的美妙之处。
<!-- more -->
#### 一、应用场景
{% alert info %}
可以这么说：抛开场景的应用都是耍流氓
{% endalert %}

我们先来看看都有哪些场景应用到了数学公式，有个整体的印象。此外，可以扩展技术面，让你可以在做技术选型的时候游刃有余：
- 大数据分析
当你要做一些大数据分析的时候，可能会用到很多的算法公式。如何让读者更好的理解内容，你可能需要用到公式渲染。
- 在线教育的数学试卷渲染
毋庸置疑，数学包含了很多公式，很难想象一张试卷没有包含任何数学公式。
- 富文本编辑器
这个可能是最多的场景，甚至你一直在用，却未曾感知。例如：Word 中插入公式，CKeditor 中的公式组件。
- 学术论文
如果你是一个严谨的学者，肯定不会在自己论文中粘贴图片的公式，你可能需要用到公式渲染。
- 印刷出版
刊物印刷出版，数学公式必不可少，没有人可以忍受印在书上的图片公式。因为它可能会失真。

#### 二、多种选择
这里主要介绍两个 `JS` 数学公式渲染的类库：
1.[Mathjax](https://www.mathjax.org): A JavaScript display engine for mathematics that works in all browsers. No more setup for readers. It just works.
![mathjax](http://7xvi3w.com1.z0.glb.clouddn.com/math1.png)

2.[KaTex](https://khan.github.io/KaTeX/): The fastest math typesetting library for the web.

#### 三、Mathjax 示例
先来看看 `Mathjax` 的例子，使用方式非常简单，我们引入类库文件：
```
<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML' async></script>
```
一个简单的例子可能如下：
```html
<!DOCTYPE html>
<html>
<head>
<title>Jartto's MathJax Demo </title>
<script type="text/javascript" async
  src="https://example.com/mathjax/MathJax.js?config=AM_CHTML"></script>
</head>
<body>

<p>When `a != 0`, there are two solutions to `ax^2 + bx + c = 0` and
they are</p>
<p style="text-align:center">
  `x = (-b +- sqrt(b^2-4ac))/(2a) .`
</p>

</body>
</html>
```
再比如：
```
When $a \ne 0$, there are two solutions to \(ax^2 + bx + c = 0\) and they are
$$x = {-b \pm \sqrt{b^2-4ac} \over 2a}.$$
```
渲染后的结果如下：
![mathjax](http://7xvi3w.com1.z0.glb.clouddn.com/math2.png)

我们可以把表达式放到引号里，如下：
```
`ax^2 + bx + c = 0`
```
或者 `$` 中
```
$ax^2 + bx + c = 0$
```

使用很简单，详情可以看看[文档](https://docs.mathjax.org/en/latest/mathjax.html)。

#### 四、KaTex 示例
看完 `Mathjax` 之后，我们再来了解一下 `KaTex`：
1.安装
```bash
# npm
npm install katex
npm install -g katex
# yarn
yarn add katex
yarn global add katex
# Browser
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.10.0-rc.1/dist/katex.css" integrity="sha384-JwmmMju6Z7M9jiY4RXeJLoNb3aown2QCC/cI7JPgmOLsn3n33pdwAj0Ml/CMMd1W" crossorigin="anonymous">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.10.0-rc.1/dist/katex.js" integrity="sha384-atIluo+2Hixq8HCazSQWa1JjeC5L0CQeWAx74Q+EbqgAW4UixbrBQF4+1jvBX01b" crossorigin="anonymous"></script>
```
{% alert info %}
KaTex 支持大部分的浏览器：Chrome, Safari, Firefox, Opera, Edge, and IE 9–11
{% endalert %}

2.使用 Dom 渲染 TeX 表达式：
```js
katex.render("c = \\pm\\sqrt{a^2 + b^2}", element, {
    throwOnError: false
});
```

3.反斜线需要特殊处理：
```js
katex.render(String.raw`c = \pm\sqrt{a^2 + b^2}`, element, {
    throwOnError: false
});
```

4.服务端渲染：
```js
var html = katex.renderToString("c = \\pm\\sqrt{a^2 + b^2}", {
    throwOnError: false
});
// '<span class="katex">...</span>'
```

5.处理自动渲染：
- 首先加载 js 和 css
```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.10.0-rc.1/dist/katex.min.css" integrity="sha384-D+9gmBxUQogRLqvARvNLmA9hS2x//eK1FhVb9PiU86gmcrBrJAQT8okdJ4LMp2uv" crossorigin="anonymous">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.10.0-rc.1/dist/katex.min.js" integrity="sha384-483A6DwYfKeDa0Q52fJmxFXkcPCFfnXMoXblOkJ4JcA8zATN6Tm78UNL72AKk+0O" crossorigin="anonymous"></script>
```
- 额外引入
```html
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.10.0-rc.1/dist/contrib/auto-render.min.js" integrity="sha384-yACMu8JWxKzSp/C1YV86pzGiQ/l1YUfE8oPuahJQxzehAjEt2GiQuy/BIvl9KyeF" crossorigin="anonymous"></script>
```

- 使用方式
```html
<script>
    document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(document.body, {
            // jartto's demo
        });
    });
</script>
```

{% alert success %}
当然，我们也可以这么使用：window.renderMathInElement(elem, options);
{% endalert %}

6.可用参数
{% alert info %}
KaTex 能支持很多的场景，缘于它可以灵活的配置。
{% endalert %}

- displayMode
- throwOnError
- errorColor
- macros
- colorIsTextColor
- maxSize
- maxExpand
- allowedProtocols
- strict

参数定义可以看[这里](https://katex.org/docs/options.html)，我们可以根据场景来配置参数。那么如何使用呢，一个简单的例如可能如下：
```js
katex.render("c = \\pm\\sqrt{a^2 + b^2}\\in\\RR", element, {
  displayMode: true,
  macros: {
    "\\RR": "\\mathbb{R}"
  }
});
```

7.异常处理
{% alert warning %}
公式排版很细致，但是疏漏不可避免，这时候你可能需要了解一下公式的异常处理。
{% endalert %}
```js
try {
    var html = katex.renderToString(texString);
    // '<span class="katex">...</span>'
} catch (e) {
    if (e instanceof katex.ParseError) {
        // KaTeX can't parse the expression
        html = ("Error in LaTeX '" + texString + "': " + e.message)
            .replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    } else {
        throw e;  // other error
    }
}
```

8.在线尝试
公式排版不是一件容易的事，为了达到更好的渲染效果，调试必不可少。还好，KaTex 为我们提供了[在线调试](https://katex.org/#demo)工具。


#### 五、优缺点
- `Mathjax` 有着更全面的解析，但却丧失了体积轻量这一优势；
- `KaTex` 则更加灵活，以高速渲染为优势；

{% alert info %}
所以，按照使用场景选择合适的类库吧。
{% endalert %}

#### 六、万变不离其宗
不管是 `Mathjax` 还是 `KaTex` 其实都依赖了底层协议，也就是我们常说的 `TEX`。

[TEX](https://zh.wikipedia.org/wiki/TeX)（希腊语：/tɛx/[1]，音译“泰赫”，文本模式下写作 TeX ），是一个由美国计算机教授高德纳（Donald Ervin Knuth）编写的功能强大的排版软件。它在学术界十分流行，特别是数学、物理学和计算机科学界。

{% alert success %}
`TEX` 被普遍认为是一个优秀的排版工具，特别是在处理复杂的数学公式时。
{% endalert %}

利用诸如是 `LATEX` 等终端软件，`TEX` 就能够排版出精美的文本以帮助人们辨认和查找。`LATEX` 是一套以 `TEX` 描述的宏软件。`LATEX` 有很多预设的模版、样式。它比 `TEX` 更为结构化，如包含了供创建索引、表格、列表等的宏和公用软件。

#### 七、掌握语法
既然大家都遵守 `TEX` 协议，那么就好办了。我们只要掌握底层语法，不管多少个类库都只是包了一层语法糖。那么我们就从语法开始吧，这里我稍作整理，大概如下：
1.换行 `\\`
2.求和
```
\displaystyle\sum_0^n
```
3.比较
```
a \gt b
```
4.分数
```
\frac{2}{9}
```
5.圆弧
```
\overgroup{AC}
```

6.表格
```
\begin{array}{|c|c|c|c|c|c|}\hline年龄\left(岁\right)&12&13&14&15&16\\\hline人数&1&4&3&7&5\\\hline\end{array}
```

7.矩阵
```
\begin{matrix}
   a & b \\
   c & d
\end{matrix}
```
8.条件判断
```
\begin{cases}
   a &\text{if } b \\
   c &\text{if } d
\end{cases}
```

如果感兴趣，可以看这里的[文档](https://katex.org/docs/support_table.html)。


#### 八、实用技巧
1.公式太长，如何换行?
```
\frac{a^{2}-1}{a}÷\frac{(a-1)^{2}}{a}=\frac{(a+1)(a-1)}{a}×\frac{a}{(a-1)^{2}}=\frac{a+1}{a-1}
```
{% alert info %}
像这样的公式，渲染出来会特别长，甚至超出了屏幕边界，大大影响用户体验。
{% endalert %}
那我们改一下，使用折行：
```
\begin{alignedat}{2}
   \frac{a^{2}-1}{a}÷\frac{(a-1)^{2}}{a}= \\
   \frac{(a+1)(a-1)}{a}×\frac{a}{(a-1)^{2}}=\frac{a+1}{a-1}
\end{alignedat}
```
这样就完美了～

2.一定要使用异常捕获
```js
try {
  return katex.renderToString(jarttoLatex, { displayMode: true });
} catch (e) {
// TODO log error latex
console.log('katex error',e);
return jarttoLatex;
}
```
{% alert warning %}
公式语法还是比较复杂，稍有不慎很容易出错，所以一定要 try catch。
{% endalert %}







