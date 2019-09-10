---
title: 掌握 React 组件 Component
date: 2017-02-19 22:28:40
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/A2B93BEC-661F-4F3D-8FDA-27D600A19151.png
thumbnailImagePosition: left
tags: 
- react
- component
comments: true
metaAlignment: center
categories: 技术博文
---
用 React 已经许久了，虽然可以熟练的书写业务逻辑，但对 Component 却一知半解。索性写篇笔记，系统的学习一下，嗯，是时候淬炼一下了👻
<!-- more -->
#### 一、如何创建组件
方式一：React.createClass
{% alert info %}
用 React.createClass 构建组件是 React 最传统、也是兼容最好的方法。
{% endalert %}
```js
const Button = React.createClass({
	getDefaultProps() {
		return {
			color: 'blue',
			text: 'http://jartto.wang'
		};
	},

	render() {
		const { color, text } = this.props;

		return (
			<button className={`btn btn-${color}`}>
				<em>{text}</em>
			</button>
		);
	}
})
```
React.createClass 方法就是构建一个组件对象。当另一个组件需要调用 Button 组件时，只用写成
```html
 <Button/>
 ```
 就可以被解析成 React.createElement(Button) 方法来创建 Button实例，这意味着在一个应用中调用几次 Button，就会创建几次 Button 实例。

方式二：ES6 classes
{% alert info %}
ES6 classes 的写法是通过 ES6 标准的类语法的方式来构建方法。
{% endalert %}
```js
import React, { Component } from 'react';

class Button extends Component {
	constructor(props) {
		super(props);
	}

	static defaultProps = {
		color: 'blue',
		text: 'http://jartto.wang'
	}

	render() {
		return (
			<button className = { `btn btn-${color}`}>
				<em>{text}</em>
			</button>
		)
	}
}
```
与 createClass 的结果相同的是，调用类实现的组件会创建实例对象。在 React 组件开发中，常用的方式是将组件拆分到合理的粒度，用组合的方式合成业务组件。

React 的所有组件都继承自顶层类 React.Component，它的定义非常简洁，只是初始化了 ReactComponent 方法，声明了 props、context、refs 等，并在原型上定义了 setState 和 forceUpdate 方法。

内部初始化的生命周期方法与 createClass 方式使用的是同一个方法创建的。

方式三：无状态函数
{% alert info %}
使用无状态函数构建的组件称为无状态组件，这种构建方式是 0.14 版本之后增加的，且官方颇为推崇。
{% endalert %}
```js
function Button({ color = 'blue', text = 'http://jartto.wang'}) {
	return (
		<button className = {`btn btn-${color}`}>
			<em>{text}</em>
		</button>
	)
}
```
无状态组件只传入 props 和 context 两个参数。也就是说，它不存在 state，也没有生命周期方法，组件本身即上面两种 React 组件构建方法中的 render 方法。不过，像 propType 和 defaultProps 还是可以通过向方法设置静态属性来实现。

#### 二、不同创建组件方式的使用场景
不错，我们有很多方式来创建 React 组件，那么问题来了，我们该如何抉择？这里我们把 React.createClass 和 ES6 classes 归为一类，称作有状态组件，而 function 类型的称为无状态组件。

下来我们主要来区分有状态组件和无状态组件的适用场景。

先来假设有这样的需求：在接入一个地图功能的时候，要求数据实时刷新，那么我们有两种方式实现：
其一：通过 sockets 来推数据，在初始化组件的时候，建立连接，然后动态更改数据；
其二：在组件初始化的时候，设置定时器更新数据，组件卸载时移除定时器；

当然，方式一需要后端 sockets 处理，我们用定时器来试试。那么问题其实已经转化为：如何在 React 组件中使用定时器，下面是官方推荐的写法：
```js
class Timer extends React.Component {
  constructor(props) {
    super(props);
    this.state = {secondsElapsed: 0};
  }

  tick() {
    this.setState((prevState) => ({
      secondsElapsed: prevState.secondsElapsed + 1
    }));
  }

  componentDidMount() {
    this.interval = setInterval(() => this.tick(), 1000);
  }

  componentWillUnmount() {
    clearInterval(this.interval);
  }

  render() {
    return (
      <div>Seconds Elapsed: {this.state.secondsElapsed}</div>
    );
  }
}

ReactDOM.render(<Timer />, mountNode);
```
{% alert info %}
计时器要 在componentDidMount 生命周期方法挂上，然后在 componentWillUnmount 生命周期方法清除。
{% endalert %}

无状态意味着也就没有生命周期函数，那么如果是这样的话，上述功能实现将不适合使用无状态组件方式，因为你很难找到合适的时机去创建和移除定时器。

无状态组件的创建形式使代码的可读性更好，并且减少了大量冗余的代码，精简至只有一个render方法，大大的增强了编写一个组件的便利，除此之外无状态组件还有以下几个显著的特点（[详情](http://www.cnblogs.com/wonyun/p/5930333.html)）：

- 组件不会被实例化，整体渲染性能得到提升
因为组件被精简成一个 render 方法的函数来实现的，由于是无状态组件，所以无状态组件就不会在有组件实例化的过程，无实例化过程也就不需要分配多余的内存，从而性能得到一定的提升。
- 组件不能访问 this 对象
无状态组件由于没有实例化过程，所以无法访问组件this中的对象，例如：this.ref、this.state 等均不能访问。若想访问就不能使用这种形式来创建组件。
- 组件无法访问生命周期的方法
因为无状态组件是不需要组件生命周期管理和状态管理，所以底层实现这种形式的组件时是不会实现组件的生命周期方法。所以无状态组件是不能参与组件的各个生命周期管理的。
- 无状态组件只能访问输入的 props，同样的 props 会得到同样的渲染结果，不会有副作用。

从《深入 React 技术栈》中摘录到这样一句话：
{% alert success %}
在适合的情况下，我们都应该且必须使用无状态组件。无状态组件不像其他两种方法在调用时会创建新实例，它创建时始终保持了一个实例，避免了不必要的检查和内存分配，做到了内部优化。
{% endalert %}

对于有状态函数中的 React.createClass 和 ES6 classes 有如下区别：
- React.createClass会自绑定函数方法（不像ES6 classes只绑定需要关心的函数）导致不必要的性能开销，增加代码过时的可能性。
- React.createClass的mixins不够自然、直观；
- React.Component形式非常适合高阶组件（Higher Order Components--HOC）,它以更直观的形式展示了比mixins更强大的功能，并且HOC是纯净的JavaScript，不用担心他们会被废弃。

更多且别请移步[React.createClass与React.Component区别](http://www.cnblogs.com/wonyun/p/5930333.html)

#### 三、组件生命周期
关于组件生命周期，我们先来看几个概念：
1.挂载与卸载过程；
组件的挂载是最基本的过程，这个过程主要做组件状态的初始化。下面是一个模板：
```js
import React, { Component, PropTypes } from 'react';

class App extends Component {
     // 	static 定义的 propTypes 和 defaultProps 可以在类外面访问，如：App.propTypes
	static propTypes = { //props 类型检查 };
	static defaultProps = { //默认类型 };
	constructor(props) {
		super(props);
		this.state = { //... };
	}
	componentWillMount() { // render 方法之前执行}
	componentDidMount() { // render 方法之后执行}
	render() {
		return <div>jartto's blog</div>;
	}
}
```
组件卸载非常简单，只有 componentWillUnmount 这一个卸载前状态：
```js
import React, { Component, PropTypes } from 'react';

class App extends Component {
	componentWillUnmount() { //... }
	render() {
		return <div>jartto's blog</div>;
	}
}
```
{% alert info %}
在 componentWillUnmount 方法中，我们常常会执行一些清理方法，如事件回收或者是清除定时器。
{% endalert %}

2.生命周期；
- componentWillMount 在渲染前调用,在客户端也在服务端。
- componentDidMount 在第一次渲染后调用，只在客户端。之后组件已经生成了对应的DOM结构，可以通过this.getDOMNode()来进行访问。 如果你想和其他JavaScript框架一起使用，可以在这个方法中调用setTimeout, setInterval或者发送AJAX请求等操作(防止异部操作阻塞UI)。
- componentWillUnmount在组件从 DOM 中移除的时候立刻被调用。
- componentWillReceiveProps 在组件接收到一个新的prop时被调用。这个方法在初始化render时不会被调用。
- shouldComponentUpdate 返回一个布尔值。在组件接收到新的props或者state时被调用。在初始化时或者使用forceUpdate时不被调用。 
可以在你确认不需要更新组件时使用。
- componentWillUpdate在组件接收到新的props或者state但还没有render时被调用。在初始化时不会被调用。
- componentDidUpdate 在组件完成更新后立即调用。在初始化时不会被调用。

3.图示流程；
![react-component](http://7xvi3w.com1.z0.glb.clouddn.com/react-component.jpeg)

#### 四、总结
React 中组件的使用不外乎文中写的这些，甚至在我们日常的项目中很多生命周期方法都用不到。但是作为一个开发者，我们要更深层次的理解这些，这样有助于我们使用最适当的方式去高效编程。

参考：
1.《深入 React 技术栈》
2.[官方文档](https://facebook.github.io/react/docs/state-and-lifecycle.html)
3.[runoob](http://www.runoob.com/react/react-component-life-cycle.html)
4.[React创建组件](http://www.cnblogs.com/wonyun/p/5930333.html)