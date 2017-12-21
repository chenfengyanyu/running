---
title: JS 反混淆
date: 2017-10-31 06:29:59
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/aliasing0.png
thumbnailImagePosition: left
tags: 
- js
- 反混淆
comments: false
metaAlignment: center
categories: 技术博文 
---
在使用某插件的过程中，大量个性化需求不能满足，于是我有了更改源码的冲动。翻遍所有角落，只找了一份压缩混淆的 js 文件，能否反混淆，这是本节讨论的重点。
<!-- more -->
#### 一、场景复现
先来说说几种我们迫切需要知道源码的情况：
1.阅读源码，当然，大部分开源的代码都是可以直接查看的；
2.对某插件做个性化的需求更改，这时候你渴望看到未混淆压缩的代码；
3.为了增加代码分析的难度，混淆（obfuscate）工具被应用到了许多恶意软件（如 0day 挂马、跨站攻击等）当中。分析人员为了掀开恶意软件的面纱，首先就得对脚本进行反混淆（deobfuscate）处理。
4.当你准备抄袭别人代码时，这个稍微有点不可描述🙈；

本文，我们假设是在前两种场景的条件下，来探索一下 js 反混淆问题。

#### 二、寻求方案
为了快速的解决问题，我们首先尝试一下现有方案：
1.[jsnice](http://jsnice.org/)
![aliasing1](http://7xvi3w.com1.z0.glb.clouddn.com/aliasing1.png-blog)
一个简单的示例：
```js
function chunkData(e, t) {
  var n = [];
  var r = e.length;
  var i = 0;
  for (; i < r; i += t) {
    if (i + t < r) {
      n.push(e.substring(i, i + t));
    } else {
      n.push(e.substring(i, r));
    }
  }
  return n;
}
```
解析后：
```js
/**
 * @param {string} str
 * @param {number} step
 * @return {?}
 */
function chunkData(str, step) {
  /** @type {Array} */
  var colNames = [];
  var len = str.length;
  /** @type {number} */
  var i = 0;
  for (;i < len;i += step) {
    if (i + step < len) {
      colNames.push(str.substring(i, i + step));
    } else {
      colNames.push(str.substring(i, len));
    }
  }
  return colNames;
};
```
是不是反混淆之后，可读性强了很多。

2.[js 代码混淆站长工具](http://tool.chinaz.com/tools/jscodeconfusion.aspx)
我们先从 Lodash 找一段演示代码，如下：
```js
function arrayReduce(array, iteratee, accumulator, initAccum) {
  var index = -1,
      length = array == null ? 0 : array.length;

  if (initAccum && length) {
    accumulator = array[++index];
  }
  while (++index < length) {
    accumulator = iteratee(accumulator, array[index], index, array);
  }
  return accumulator;
}
```
普通混淆后，就变成了这样：
```js
function arrayReduce(RkeVlNGo1, ZPgGaWYOP2, uFMz3, dHyv4) {  var tTsFyC5 = -1,      sh6 = RkeVlNGo1 == null ? 0 : RkeVlNGo1["\x6c\x65\x6e\x67\x74\x68"];  if (dHyv4 && sh6) {    uFMz3 = RkeVlNGo1[++tTsFyC5];  }  while (++tTsFyC5 < sh6) {    uFMz3 = ZPgGaWYOP2(uFMz3, RkeVlNGo1[tTsFyC5], tTsFyC5, RkeVlNGo1);  }  return uFMz3;}
```
为了测试一下如何反混淆，我们将混淆后代码拷贝到 [jsnice](http://jsnice.org/) ：
```js
/**
 * @param {Object} collection
 * @param {?} callback
 * @param {Text} accumulator
 * @param {number} a
 * @return {?}
 */
function arrayReduce(collection, callback, accumulator, a) {
  /** @type {number} */
  var index = -1;
  var b = collection == null ? 0 : collection["length"];
  if (a && b) {
    accumulator = collection[++index];
  }
  for (;++index < b;) {
    accumulator = callback(accumulator, collection[index], index, collection);
  }
  return accumulator;
};
```
可以看到，普通混淆后，代码还是可以做一些复原。好的，我们加大力度，采用加密压缩方式：
```js
eval(function(p,a,c,k,e,d){e=function(c){return(c<a?"":e(parseInt(c/a)))+((c=c%a)>35?String.fromCharCode(c+29):c.toString(36))};if(!''.replace(/^/,String)){while(c--)d[e(c)]=k[c]||e(c);k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1;};while(c--)if(k[c])p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c]);return p;}('b a(2,7,4,6){9 3=-1,5=2==8?0:2.5;e(6&&5){4=2[++3]}d(++3<5){4=7(4,2[3],3,2)}c 4}',15,15,'||array|index|accumulator|length|initAccum|iteratee|null|var|arrayReduce|function|return|while|if'.split('|'),0,{}))
```
这次代码明显多了，而 jsnice 也反混淆失败了：
```js
eval(function(str, n, name, pair, func, opt_attributes) {
  /**
   * @param {number} i
   * @return {?}
   */
  func = function(i) {
    return(i < n ? "" : func(parseInt(i / n))) + ((i = i % n) > 35 ? String.fromCharCode(i + 29) : i.toString(36));
  };
  if (!"".replace(/^/, String)) {
    for (;name--;) {
      opt_attributes[func(name)] = pair[name] || func(name);
    }
    /** @type {Array} */
    pair = [function(timeoutKey) {
      return opt_attributes[timeoutKey];
    }];
    /**
     * @return {?}
     */
    func = function() {
      return "\\w+";
    };
    /** @type {number} */
    name = 1;
  }
  for (;name--;) {
    if (pair[name]) {
      /** @type {string} */
      str = str.replace(new RegExp("\\b" + func(name) + "\\b", "g"), pair[name]);
    }
  }
  return str;
}("b a(2,7,4,6){9 3=-1,5=2==8?0:2.5;e(6&&5){4=2[++3]}d(++3<5){4=7(4,2[3],3,2)}c 4}", 15, 15, "||array|index|accumulator|length|initAccum|iteratee|null|var|arrayReduce|function|return|while|if".split("|"), 0, {}));
```
这段代码，地球人已经没法读懂了，乱七八糟的。那么，问题来了，加密混淆的代码真的没有办法复原吗？

#### 三、思维突破
从上面的演示可以看出来，加密之后的混淆，已经完全无法反混淆了。除非我们知道混淆算法，可是混淆方式不计其数，你需要知晓它的混淆方式，并制定出反混淆算法，那估计会累死。

{% alert danger %}
如果我们这么想的话，那么就陷入了泥潭，甚至无法自救。那么，突破点到底在哪里呢？
{% endalert %}

众所周知，JavaScript 是解释性语言，它严重依赖游览器。不管 JavaScript 如何混淆，**最终浏览器都会知道最真实的代码**。所以，我们还得以浏览器为突破口。

首先，同步一下原始代码：
```js
function arrayReduce(array, iteratee, accumulator, initAccum) {
  var index = -1,
      length = array == null ? 0 : array.length;

if(typeof(iteratee) ==='function') throw 'jartto-test';

  if (initAccum && length) {
    accumulator = array[++index];
  }
  while (++index < length) {
    accumulator = iteratee(accumulator, array[index], index, array);
  }
  return accumulator;
}
arrayReduce();
```
其次，确定源码中是否包含在 `eval` 中，参考代码如下：
```js
eval(function(p,a,c,k,e,d){e=function(c){return(c<a?"":e(parseInt(c/a)))+((c=c%a)>35?String.fromCharCode(c+29):c.toString(36))};if(!''.replace(/^/,String)){while(c--)d[e(c)]=k[c]||e(c);k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1;};while(c--)if(k[c])p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c]);return p;}('9 a(2,6,4,7){d 3=-1,5=2==e?0:2.5;8(b(6)===\'9\')h\'i-g\';8(7&&5){4=2[++3]}c(++3<5){4=6(4,2[3],3,2)}f 4}a();',19,19,'||array|index|accumulator|length|iteratee|initAccum|if|function|arrayReduce|typeof|while|var|null|return|test|throw|jartto'.split('|'),0,{}))
```
然后，查找关键字 `throw`，如果有，那就成功了一大步；
最后，改动源码，让源码抛出异常，让 Eval Code 还原出真实代码；

{% alert info %}
可以看到，为了演示，我们让源代码具有某些特性，然而实际情况会远远复杂于此；
{% endalert %}

思维启蒙：[一招破解混淆后的 JavaScript 代码](http://www.xyhtml5.com/break-confusion-javascript-code.html)

#### 四、相关工具介绍
我们先来看一下，目前常用的混淆工具：
- [YUI Compressor](http://yui.github.io/yuicompressor/)
- [Google Closure Compiler](https://developers.google.com/closure/compiler/)
- [UglifyJS](https://github.com/mishoo/UglifyJS)
- [JScrambler](https://jscrambler.com/)

反混淆工具：
- [jsbeautifier](http://jsbeautifier.org/)
- [JSDetox](http://relentless-coding.org/projects/jsdetox/)

了解更多，请参考：[几种常见的JavaScript混淆和反混淆工具分析实战](http://www.freebuf.com/articles/web/97945.html)

#### 五、了解混淆手段
1.base62 编码，其最明显的特征是生成的代码以 eval(function(p,a,c,k,e,r)) 开头；
{% alert info %}
看到这里，上述站长工具的加密方式，就不难理解了。
{% endalert %}
这类混淆的关键思想在于将需要执行的代码进行一次编码，在执行的时候还原出浏览器可执行的合法的脚本，然后执行之。看上去和可执行文件的加壳有那么点类似。Javascript 提供了将字符串当做代码执行（evaluate）的能力，可以通过 Function 构造器、eval、setTimeout、setInterval 将字符串传递给 js 引擎进行解析执行。

无论代码如何进行变形，其最终都要调用一次 eval 等函数。解密的方法不需要对其算法做任何分析，只需要简单地找到这个最终的调用，改为 console.log 或者其他方式，将程序解码后的结果按照字符串输出即可。

2.隐写术
严格说这不能称之为混淆，只是将 js 代码隐藏到了特定的介质当中。如通过最低有效位（LSB）算法嵌入到图片的 RGB 通道、隐藏在图片 EXIF 元数据、隐藏在 HTML 空白字符等。

比如这个耸人听闻的议题：《一张图片黑掉你》在图片中嵌入恶意程序，正是使用了最低有效位平面算法。结合 HTML5 的 canvas 或者处理二进制数据的 TypeArray，脚本可以抽取出载体中隐藏的数据（如代码）。

隐写的方式同样需要解码程序和动态执行，所以破解的方式和前者相同，在浏览器上下文中劫持替换关键函数调用的行为，改为文本输出即可得到载体中隐藏的代码。


3.复杂表达式
代码混淆不一定会调用 eval，也可以通过在代码中填充无效的指令来增加代码复杂度，极大地降低可读性。Javascript 中存在许多称得上丧心病狂的特性，这些特性组合起来，可以把原本简单的字面量（Literal）、成员访问（MemberExpression）、函数调用（CallExpression）等代码片段变得难以阅读。

举个简单的例子，可能会更好理解：
1. 访问一个对象的成员有两种方法——点运算符和下标运算符。调用 window 的 eval 方法，既可以写成 window.eval()，也可以 window['eval']；
2. 为了让代码更变态一些，混淆器选用第二种写法，然后再在字符串字面量上做文章。先把字符串拆成几个部分：'e' + 'v' + 'al'；
3. 这样看上去还是很明显，再利用一个数字进制转换的技巧：14..toString(15) + 31..toString(32) + 0xf1.toString(22)；
4. 一不做二不休，把数字也展开：(0b1110).toString(4<<2) + (' '.charCodeAt() - 1).toString(Math.log(0x100000000) / Math.log(2)) + 0xf1.toString(11 << 1)；
5. 最后的效果：window[(2*7).toString(4<<2) + (' '.charCodeAt() - 1).toString(Math.log(0x100000000) / Math.log(2)) + 0xf1.toString(11 << 1)]('alert(1)')

在 js 中可以找到许多这样互逆的运算，通过使用随机生成的方式将其组合使用，可以把简单的表达式无限复杂化。

到这里，我已经晕头转向了。深入了解，请移步[使用 estools 辅助反混淆 Javascript](http://blog.knownsec.com/2015/08/use-estools-aid-deobfuscate-javascript/)

#### 六、写在最后
JavaScript 作为一个以函数式为核心的多范式动态弱类型脚本语言，因为它的灵活性，导致了源代码在经过一些压缩工具处理后，变得极难还原。

也有可能，当我们费劲心思还原出来的代码，也许只是与源代码运行流程一致的另一套代码。当然，我们可以继续探索，发掘未知的领域。

#### 七、了解更多
- [l1l=document.all 特征 JS 混淆器反混淆流程详解](https://www.blackglory.me/l1l-document-all-features-detailed-js-confused-with-anti-aliasing-process/)
- [反混淆的技巧和套路](https://www.blackglory.me/javascript-deobfuscate-general-routines-and-tips-chapter-1/)
- [前端如何给 JS 加密](https://www.zhihu.com/question/47047191)
