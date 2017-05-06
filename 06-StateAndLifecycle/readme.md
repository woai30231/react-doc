## State and Lifecycle（React 状态和生命周期）

回顾一下我们实现的一个关于[时钟](https://github.com/woai30231/react-doc/tree/master/04-RenderingElements)的例子。

我们仅仅是知道如何用一种方法更新用户界面。

我们仅仅是使用了ReactDOM.render()每一秒重新渲染了页面而已：

```jsx
	function tick(){
		const element = (
			<div>
				<h1>Hello,world!</h1>
				<h2>It is {new Date().toLocaleTimeString()}</h2>
			</div>
		);
		ReactDOM.render(
			element,
			document.getElementById('root')
		);
	};
	setInterval(tick,1000);
```

但是在本章节，我们将会以另外一种方法实现时钟Component——真正实现了可复用和封装，它将会实现自己的计时器，同时每一秒自己去更新用户界面。

我们可以先把代码改成这样：

```jsx
	function Clock(props){
		return (
			<div>
				<h1>Hello,world!</h1>
				<h2>It is {props.date.toLocaleTimeString()}.</h2>
			</div>
		);
	};


	function tick(){
		ReactDOM.render(
			<Clock date={new Date()}/>,
			document.getElementById('root')
		);
	};
	setInterval(tick,1000);
```

我们从原先的直接渲染一个时钟到抽象处一个Clock组件出来，这样就会在多个地方实现使用Clock组件了，是不是省去了很多功夫呢！

但是，我们回过来看上面的代码，其实它漏掉了一个最大的需求，就是这个Clock组件，完全可以自己在内部实现自己的定时器而不用我们周期地调用tick方法来实现更新用户界面。

正常情况下，我们只需要写下下面的代码，就能实现上面的功能：

```jsx
	ReactDOM.render(
		<Clock />,
		document.getElementById('root')
	);
```

那怎么才能实现呢？这时候就需要在Clock组件中用到我们的“state”了。

state和props类似，**但是它对于一个组件来说，完全是私有的，并且全权受组件控制！**

我们前面提到过，在用“类式”方式写一个组件的时候，可以写一些附加的属性，本地state就包含在这些可以定义的附加属性里面。

## Converting a Function to a Class（从函数式方式转变成用类式方式写一个组件）

用“类式”方式实现上面的Clock组件，你只需要按下面五步操作：

_ 1、创建一个[es6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)，类名就叫Clock，同时Clock继承React.Component；

_ 2、添加一个空方法render()；

_ 3、把函数式方式函数体里面的内容添加到render函数体里面；

_ 4、同时在函数体里面，把原先的props改成this.props；

_ 5、再删除不需要的部分。

经过上面五步，你的Clock组件将会变成这样：

```jsx
	class Clock extends React.Component{
		render(){
			return (
				<div>
					<h1>Hello,world!</h1>
					<h2>It is {this.props.date.toLocaleTimeString()}.</h2>
				</div>
			);
		};
	};
```

现在我们的Clock组件就变成了一个“类式”方式构建的了。

让我们在为组件添加一些附加的特性，比如本地state、生命周期钩子等等。

## Adding Local State to a Class

首先我们需要删除从props上获取date，只需要三步：

* 1、在render方法里面移除this.props.date，取而代之为this.state.date:

```jsx
	class Clock extends React.Component{
		render(){
			return (
				<div>
					<h1>Hello,world!</h1>
					<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
				</div>
			);
		};
	};
```

* 2、加一个[class constructor](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor)来为实例化Clock的时候初始化实例：

```jsx
	class Clock extends React.Component{
		constructor(props){
			super(props);
			this.state = {date:new Date()};
		};
		render(){
			return (
				<div>
					<h1>Hello,world!</h1>
					<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
				</div>
			);
		};
	};
```

这里注意一下，我们是怎么传递props给我们的构造函数的，有兴趣的的看下es6 class，看过之后就能懂了！

```javascript
	constructor(props){
		super(props);
		this.state = {date:new Date()};
	};
```

采用“类式”方式构建组件总是在构造函数里传递props参数。

* 3、在<Clock />中删除date属性。

```jsx
	ReactDOM.render(
		<Clock />,
		document.getElementById('root')
	);
```

我们待会儿其内部实现计时器，目前来看，整体代码如下：

```jsx
	class Clock extends React.Component{
		constructor(props){
			super(props);
			this.state = {date:new Date()};
		};
		render(){
			return (
				<div>
					<h1>Hello,world!</h1>
					<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
				</div>
			);
		};
	};
	ReactDOM.render(
		<Clock />,
		document.getElementById('root')
	);
```

