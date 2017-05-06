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

现在，我们来实现Clock自己的计时器，并且自己每秒去更新自己。

## Adding Lifecycle Methods to a Class（向类中添加生命周期方法）

在一个包含很多组件的app里面，一件非常重要的事情需要去做就是——当一个组件被删除的时候，需要释放它所占的系统资源空间。

我们将在任何时候Clock组件第一次被渲染到页面的时候实现[设置一个计时器](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval)。在react中，一个组件出现在页面中，技术术语称作“mounting（挂载）”。

我们同时需要在任何时候[清除这个计时器](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/clearInterval)，只要Clock组件在页面上消失。这个技术术语在react中称作“unmounting（卸载）”。

我们可以在用“类式”方式构建组件的时候，添加一些方法，表明组件“卸载”和“挂载”时需要执行的一些操作，如下：

```jsx
	class Clock extends React.Component{
		constructor(props){
			super(props);
			this.state = {date:new Date()};
		};

		//组件挂载时执行
		componentDidMount(){
			//代码省略
		};

		//组件卸载时执行
		componentWillUnmount(){
			//代码省略
		};

		render(){
			return (
				<div>
					<h1>Hello,world!</h1>
					<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
				</div>
			);
		};
	}
		
```

像上面的componentWillUnmount和componentDidMount方法，在react中，我们称之为“react生命周期钩子”。

componentDidMount方法在组件渲染到页面上之后调用，这个时候非常适合开启我们的计时器。

```jsx
	componentDidMount(){
		this.timerID = setInterval(()=>{
			this.tick();
		},1000);
	};
```

注意，我们这里是怎么保存计时器返回的id值的。

我们知道this.props和this.state是由react自己创建的，但是this.state却又特殊的含义，你可以自由向this.state对象下添加一些你可能需要或者不需要的数据。

如果你要使用的数据不在render方法里面，那么不需要在state里面设置。

当componentWillUnmount调用的时候，我们将关闭计时器。如：

```jsx
	componentWillUnmount(){
		clearInterval(this.timerID);
	};
```

接下来，我们将实现tick方法，它会每秒调用一次，并且会修改本地state。如：

``` jsx
	class Clock extends React.Component{
		constructor(props){
			super(props);
			this.state = {date:new Date()};
			this.tick = this.tick.bind(this);
		};
		render(){
			return (
				<div>
					<h1>Hello,world!</h1>
					<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
				</div>
			);
		};
		componentDidMount(){
			this.timerID = setInterval(()=>this.tick(),1000);
		};
		componentWillUnmount(){
			clearInterval(this.timerID);
		};
		tick(){
			this.setState({
				date:new Date()
			});
		};
	};

	ReactDOM.render(
		<Clock />,
		document.getElementById('root')
	);
		
```

现在，我们的代码完了，它会向时钟一样，每秒更新一次。

我们简单回顾一下，react做了什么，并且看一下依次调用了那些方法：

* 1、当Clock组件被ReactDOM.render方法向页面渲染的时候，React会首先调用Clock类的构造方法，然后它知道Clock组件需要显示当前时间，那么它就会初始化this.state，并且将它设置成包含一个当前时间的对象。而后会更新this.state；

* 2、当然调用Clock组件的render方法时候，react就会知道需要在页面上显示什么内容，那么react就会更新页面内容，这个内容就是Clock组件的render方法的输出；

* 3、当Clock插入到页面的时候，React就会调用componentDidMount，从而React会请求浏览器启动一个计时器，以便用来每秒调用一次tick方法；

* 4、当调用tick方法的时候，会调用this.setState方法，这个方法会把state的date属性更新会当前时间，得益于this.setState方法，React才知道state发生了改变，那么它重新调用一次Clock组件的render方法，那么此时的date变成了当前时间，React会有效的更新页面；

* 5、
