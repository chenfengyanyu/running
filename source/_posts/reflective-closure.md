---
title: 反思闭包
date: 2017-12-18 22:33:23
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/logo/closure0.png
thumbnailImagePosition: left
tags:
- js
- 闭包
comments: false
metaAlignment: center
categories: 技术博文
---
在定位一个 `Bug` 的时候，我们聊到了闭包，我认为是闭包的弊端，但在别人眼里却是优势。这是一个值得思考的问题，我决定重新认识闭包，以开放的心态去反思。
<!-- more -->
#### 一、我理解的闭包
{% alert info %}
有权访问另一个函数作用域中局部变量的函数。创建方式，就是在一个函数内部创建另一个函数，通过另一个函数访问这个函数的局部变量。
{% endalert %}

概念都是这么一说，细节问题到是不少，下面我们细说一下。

#### 二、先举几个例子
1.实现局部变量累加
```js
const sum = () => {
  let count = 666;
return () => {
return count++;
}
}
let getCount= sum();
console.log(getCount()); // 667
console.log(getCount()); // 668
console.log(getCount()); // 669
```
{% alert danger %}
由于闭包里作用返回的局部变量资源不会被回收，所以可能会驻留在内存中。
{% endalert %}

2.`JS` 没有块级作用域，用匿名函数可以用来模仿块级作用域并避免出现命名参数冲突的问题。
```js
(function(){
//块级作用域
})();
```
{% alert info %}
这种技术经常在全局作用域中被用在函数外部，从而限制向全局作用域中添加过多的变量和函数，这种做法可以减少闭包占用内存的问题。
{% endalert %}

3.闭包中的 `this` 对象
{% alert info %}
`this` 对象是在运行时基于函数的执行环境绑定的。
{% endalert %}
```js
let name = 'Window';
let obj = {
name: 'Jartto',
getName: function(){
return function(){
return this.name;
}
}
}
console.log(obj.getName()()); // Window
```

每个函数被调用时都会取得两个特殊变量：`this` 和 `arguments`。内部函数搜索这两个变量时，只会搜索到其活动对象为止，因此，不可能直接访问外部函数的这两个变量。不过将外部作用域中的 `this` 保存在一个闭包能够访问的变量里，就可以访问该对象了。

```js
let name = 'Window';
let obj = {
name: 'Jartto',
getName: function(){
let that = this;
return function(){
return that.name;
}
}
}
console.log(obj.getName()()); // Jartto
```

4.在 `IE9` 之前，如果闭包的作用域链中保存着一个 `HTML` 元素，那么意味着该元素无法被销毁。
```js
function closure(){
var element = document.getElementById(ID);
element.onclick = function(){
console.log(element.id);
}
}
```
这里 `DOM` 对象 `element` 引用闭包函数，闭包函数作用域引用 `DOM` 对象，循环引用导致内存泄露。我们修改一下如上代码：
```js
function closure(){
var element = document.getElementById(ID);
var id = element.id;
element.onclick = function(){
console.log(id);
}

element = null;
}
```
先把 `element.id` 用局部变量 `id` 保存起来，并且在闭包中引用该变量消除了循环引用。但是，这样还不能消除内存泄露，闭包会引用包含函数的活动变量，而其中会有 `element`。即使闭包闭包不直接引用 `element`，包含函数的活动对象仍然会保存一个引用，因此有必要把 `element` 设为 `null`。

5.闭包只能获取到外部函数中任何变量的最后一个值。
```js
function Outer() {
var arr = new Array();
for (var i = 0; i < 5; ++i) {
arr[i] = function() {
return i;
};
}
return arr;
}

var arr = Outer();
console.log(arr[0]()); // 输出5
console.log(arr[1]()); // 输出5
console.log(arr[2]()); // 输出5
console.log(arr[3]()); // 输出5
console.log(arr[4]()); // 输出5
```
我们可以通过定义立即执行函数来解决这个问题，如下：
```js
function Outer() {
var arr = new Array();
for (var i = 0; i < 5; ++i) {
arr[i] = function(num) {
// inner
return function() {
return num;
}
}(i);
}
return arr;
}

var arr = Outer();
console.log(arr[0]()); // 输出0
console.log(arr[1]()); // 输出1
console.log(arr[2]()); // 输出2
console.log(arr[3]()); // 输出3
console.log(arr[4]()); // 输出4
```
6.匿名自执行函数
除了每次使用变量都是用 `var` 关键字外，我们在实际情况下经常遇到这样一种情况，即有的函数只需要执行一次，其内部变量无需维护，可以尝试如下方式：
```js
var datamodel = {
table : [],
tree : {}
};
(function(dm){
for(var i = 0; i < dm.table.rows; i++){
var row = dm.table.rows[i];
for(var j = 0; j < row.cells; i++){
drawCell(i, j);
}
}
//build dm.tree
})(datamodel);
```
{% alert success %}
我们创建了一个匿名的函数，并立即执行它，由于外部无法引用它内部的变量，因此在执行完后很快就会被释放，关键是这种机制不会污染全局对象。
{% endalert %}

7.缓存
再来看一个例子，设想我们有一个处理过程很耗时的函数对象，每次调用都会花费很长时间，那么我们就需要将计算出来的值存储起来，当调用这个函数的时候，首先在缓存中查找，如果找不到，则进行计算，然后更新缓存并返回值，如果找到了，直接返回查找到的值即可。
{% alert info %}
闭包正是可以做到这一点，因为它不会释放外部的引用，从而函数内部的值可以得以保留。
{% endalert %}
```js
var CachedSearchBox = (function(){
var cache = {}, count = [];
return {
attachSearchBox : function(dsid){
if(dsid in cache){//如果结果在缓存中
return cache[dsid];//直接返回缓存中的对象
}
var fsb = new uikit.webctrl.SearchBox(dsid);//新建
cache[dsid] = fsb;//更新缓存
if(count.length > 100){//保正缓存的大小<=100
delete cache[count.shift()];
}
return fsb;
},
clearSearchBox : function(dsid){
if(dsid in cache){
cache[dsid].clearSelection();
}
}
};
})();
CachedSearchBox.attachSearchBox("input1");
```
这样，当我们第二次调用 `CachedSearchBox.attachSerachBox(“input1”)`的时候，我们就可以从缓存中取道该对象，而不用再去创建一个新的 `searchbox` 对象。

8.实现封装
```js
var person = function(){
//变量作用域为函数内部，外部无法访问
var name = 'default';
return {
getName : function(){
return name;
},
setName : function(newName){
name = newName;
}
}
}();
console.log(person.name); // 直接访问，结果为undefined
console.log(person.getName()); // default
person.setName('jartto');
console.log(person.getName()); // jartto
```

9.实现面向对象中的对象
我们要实现不同的对象(类的实例)拥有独立的成员及状态，互不干涉。虽然 `JS` 中没有类这样的机制，但是通过使用闭包，就可以模拟出这样的机制。
```js
function Person(){
var name = 'default';
return {
getName : function(){
return name;
},
setName : function(newName){
name = newName;
}
}
};
var p1 = Person();
console.log(p1.getName()); // default
p1.setName('wang');
console.log(p1.getName()); // wang
var p2 = Person();
console.log(p2.getName()); // default
p2.setName('jartto');
console.log(p2.getName()); // jartto
```

由此代码可知，`p1` 和 `p2` 都可以称为是 `Person` 这个类的实例，因为这两个实例对 `name` 这个成员的访问是独立的，互不影响的。

#### 三、为什么要使用闭包？
看了这么多例子，我们来总结一下为什么使用闭包：
- 闭包可以模拟面向对象的代码风格；
- 更优雅，更简洁的表达出代码；
- 在某些方面提升代码的执行效率。
- 缓存复杂逻辑代码；
- 使用闭包会把局部变量驻留在内存中，避免了使用全局变量；
- 模仿块级作用域；
- 实现封装，防止变量跑到外层作用域中，发生命名冲突；
- 匿名自执行函数，匿名自执行函数可以减小内存消耗；

{% alert success %}
在作用域链和闭包中的性能问题主要表现在数据读写的速度上。由于作用域链的原因，我们访问全局作用域的数据时，效率是最低的，而访问局部数据时的效率是最高的。
{% endalert %}

此外，需要补充一点：[全局变量的优缺点](http://jartto.wang/2014/08/20/js-variable/)
- 优点：减少变量个数、减少实参和形参之间传递的时间等等。
- 缺点：
- 首先，全局变量保存在静态存储区，程序开始运行时为其分配内存，程序结束时释放该内存。与局部变量的动态分配。动态释放相比，生存期比较长，因此过多的全局变量会占用较多的内存单元；
- 其次，全局变量破坏了函数的封装性能。函数象一个黑匣子，一般是通过函数参数和返回值进行输入输出，函数内部实现相对独立。但函数中如果使用了全局变量，那么函 数体内的语句就可以绕过函数参数和返回值进行存取，这种情况破坏了函数的独立性，使函数对全局变量产生依赖。同时，也降低了该函数的可移植性。
- 最后，全局变量使函数的代码可读性降低。由于多个函数都可能使用全局变量，函数执行时全局变量的值可能随时发生变化，对于程序的查错和调试都非常不利。

通过全局变量的缺点来看，适当的使用闭包也是一种优雅的编程方式。

#### 四、闭包的弊端
看完上面的栗子，大夸特夸之后，是时候泼点冷水了，闭包都有哪些弊端呢？
- 由于闭包作用域返回的局部变量资源不会被立刻销毁回收，所以可能会占用更多的内存；
- `this` 对象在运行时基于函数的执行环境绑定的，而闭包在运行时是指向 `window` 的，因为闭包并不属于这个对象的属性或方法（用 `call` 可以强制的要求指向那个对象）；

#### 五、闭包为什么会导致内存占用过多？
一般来讲，当函数执行完毕后，局部活动对象就会销毁，内存仅保存全局作用域。但是，闭包的情况不同，`closure` 函数执行完毕后，其活动对象不会销毁，因为匿名函数的作用域链仍然引用这个活动对象。直到匿名函数被销毁后，`closure` 函数的活动对象才会被销毁。

由于闭包会携带包含它的函数的作用域，因此会占用更多的内存，过度的使用闭包会导致内存占用过多，因此，在绝对必要时，再考虑使用闭包。

#### 六、内存占用和内存泄漏
很多人都会把内存使用和内存泄露搞混。所谓内存泄露是老浏览器（主要是IE6）由于垃圾回收有问题导致的 `bug`，跟 `JS` 本身没有关系。

正如[贺师俊](https://www.zhihu.com/question/31078912?sort=created)大神所说闭包不会造成内存泄漏，程序写错了才会造成内存泄漏。

跟闭包和[内存泄露](https://www.zhihu.com/question/31078912/answer/56771027)有关系的地方是，使用闭包的同时比较容易形成循环引用，如果闭包的作用域链中保存着一些 `DOM` 节点，这时候就有可能造成内存泄露。但这本身并非闭包的问题，也并非 `JavaScript` 的问题。

在 `IE` 浏览器中，由于 `BOM` 和 `DOM` 中的对象是使用 `C++` 以 `COM` 对象的方式实现的，而 `COM` 对象的垃圾收集机制采用的是引用计数策略。在基于引用计数策略的垃圾回收机制中，如果两个对象之间形成了循环引用，那么这两个对象都无法被回收，但循环引用造成的内存泄露在本质上也不是闭包造成的。

#### 七、反思
到这里，我们对闭包的理解已经很清楚了，那么如下的问题，对与错也就很容易回答了。
- 闭包会造成内存泄漏吗？(✗)
- 闭包中的局部变量会变成全局变量；(✗)
- 闭包的弊端就是会造成内存泄漏；(✗)
- 闭包会影响性能；(✗)
- 闭包是作用域的应用；(√)
- 闭包本身不会造成内存泄漏，但闭包过多很容易导致内存泄漏。(√)

#### 八、总结
知识总是在不断论述中得到升华，感谢与我论述并给我启发的 Wmu 同学。

#### 九、相关资源
[JavaScript 闭包详解](http://blog.csdn.net/vuturn/article/details/44560717)
[JavaScript 闭包的原理与缺陷](http://blog.csdn.net/hongchh/article/details/52222561)
[JS 闭包的用途](http://blog.csdn.net/sunlylorn/article/details/6534610)
[JS 闭包测试](http://www.cnblogs.com/rubylouvre/p/3345294.html)
[关于 JS 闭包是否真的会造成内存泄漏？](https://www.zhihu.com/question/31078912?sort=created)