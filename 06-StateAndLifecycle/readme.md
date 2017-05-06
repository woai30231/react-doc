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

* 5、只要Clock从DOM中移除，就会触发componentWillUnmount方法钩子，那么这个就会清除定时器。

## Using State Correctly（正确使用state）

在使用state的时候，有三件事情你需要知道。

### Do Not Modify State Directly （不要直接修改state）

例如，下面的代码就不会重新调用组件render方法：

```jsx
	//Wrong
	this.state.comment = 'Hello';
```

你不应该直接修改this.state，而应该通过this.setState方法。如：

```jsx
	//Correct
	this.setState({comment:'Hello'});
```
只有一个地方你可以直接修改，那就是组件构造函数里面，在其它地方凡是涉及到需要修改state的，都应该使用this.setStata。

### State Updates May Be Asynchronous（state的更新是异步的）

React可能为了性能，一次更新一批state状态。

因为props和state的更新是异步的，所以你不能依靠它的前一个值算出下一个值。

例如，下面的代码就会失败：

```jsx
	//Wrong
	this.setState({
		counter:this.state.counter + this.props.increment
	});
```

为了解决这个问题，我们可以使用另外一种格式的this.setState方法调用，我们通过传递一个回调函数而不是对象来解决。这个回调函数接受两个参数，分别是前一个state和当前组件的props，代码如下：

```jsx
	//Correct
	this.setState((previousState,props)=>{
		return {counter:previousSate.counter + props.increment};
	});
```

我们上面使用了[arrow function](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，但它可以使用正常的javascript函数。如：

```jsx
	//Correct
	this.setState(function(previousState,props){
		return {counter:previousSate.counter + props.increment};
	});
```

## State Updates are Merged（采用合并的方法更新state）

当我们调用setState方法的时候，react会合并一个对象到当前的state里面。

例如，你的state可能包含几个独立的值：

```jsx
	constructor(props){
		super(props);
		this.state = {
			posts : [],
			comments : []
		};
	};
```
然后你可以通过分开调用setState方法来分别更新它们，如：

```jsx
	componentDidMount(){
		fetchPosts().then(response=>{
			this.setState({
				posts:response.posts
			});
		});

		fetchComments().then(response=>{
			this.setState({
				comments:response.comments
			});
		});
	};
```

这个合并是简单执行的，就是说当更新comments的时候，它不会动posts，但却完整地改变comments。

## The Data Flows Down（数据是向下流的）

在一个组合组件里，父组件和子组件都不知道这个组件的是富状态的还是少状态的，它们也不关心组件是用“函数式”还是“类式”构建的。

这也是为什么说state是本地或密封的。组件是不能访问另外一个组件的state，更不要说去设置或更改它。

一个父组件可以选择通过向下传递它的state作为子组件的props，如：

```jsx
	<h2>It is {this.state.date.toLocaleTimeString()}.</h2>
```

也可以在用户定义的组件用：

```jsx
	<FormattedDate date={this.state.date} />
```

这里，FormattedDate组件从它的props中接受date值，而不用管它是通过Clock的state或者Clock的props传递过来的，又或者是手写的：

```jsx
	function FormattedDate(props){
		return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
	};
```

这通常称为“自上向下”或者“单向”的数据流。一个state只能被某个独立的组件所拥有，但这个state只能影响或者传递给组件树层中下一层的组件。

如果你把一个组件树的props看作一个瀑布流的话。每个组件的comment的state就像传统的水源一样，不管来自什么地方。但总是向下流的。

为了展示我们所说的所有的组件都是隔离的，我们可以创建一个App组件，它选了一个Clock三次：

```jsx
	function App(props){
		return (
			<div>
				<Clock />
				<Clock />
				<Clock />
			</div>
		);
	};

	ReactDOM.render(
		<App />,
		document.getElementById('root')
	);
```

每个Clock都用自己的定时器，并且独立的更新自己。

在react程序里面，无论一个组件是富状态的还是少状态的，都被认为是一个合格的组件实现。你可以在一个富状态的组件里面引用一个少状态的组件，反之亦然。
