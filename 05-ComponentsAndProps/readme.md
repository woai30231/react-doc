## Components and Props（组件和属性对象）

Components（组件）把你的用户界面分解每个独立的部分，这样就可以复用，保持作用域隔离等等！

概念性地理解，Components就好像javascript里面的function一样，它可以接收任意的数据作为输入（props），同时最后返回一个用以决定你的界面上显示什么内容的React elements。

## Functional and Class Components（函数式组件和类式组件）

最简单的方法创建一个React component就是使用javascript的function，代码如下：

```jsx
	function Welcome(props){
		return <h1>Hello,{props.name}</h1>;
	};

```

我们说上面的Welcome是一个有效的React Component，是因为它接收一个props作为参数，并且返回一个React element。我们说这是一个函数式组件，是因为它就是以javascript的function创建的。

你也可以用[es6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)创建一个类式组件，如：

```jsx

	class Welcome extends React.Component{
		constructor(props){
			super(props);
		};
		render(){
			return <h1>Hello,{this.props.name}</h1>;
		};
	};
```


从React视图上来说，上面两个组件是等价的。

但是类式创建react component会有一些附加的属性用以更灵活的控制components，我们将在下一个章节讨论，但是在此之前，为了更简洁的说明问题，我们将用函数式Components。

## Rendering a Component（渲染一个组件到页面）

在此之前，我们总是渲染的标签是一个标准的dom标签：

```jsx
	const element = <div />;
```

然而，elements也可以是一个用户定义的组件：

```jsx
	const element = <Welcome name="Sara" />;
```

当react看到一个用户定义的组件时，它将会传递一个“props”对象给它！

看一个例子，下面的代码将会页面上显示“Hello,Sara”。

```jsx
	function Welcome(props){
		return <h1>Hello,{props.name}</h1>;
	};

	const element = <Welcome name="Sara" />;
	ReactDOM.render(
		element,
		document.getElementById('root')
	);
```

我们简要说明一下，上面的代码发生了什么：

_ 1、我们通过调用ReactDOM.render，渲染了<Welcome name="Sara"/> element；

_ 2、React将对象{name:'Sara'}作为props参数传递给Welcome组件；

_ 3、组件Welcome返回&lt;h1&gt;Hello,Sara&lt;/h1&gt;结果作为一个element；

_ 4、React有效地更新DOM，以此来跟&lt;h1&gt;Hello,Sara&lt;/h1&gt;匹配。



* **警告**

组件名字的第一个字母通常是大写字母。

例如，一个&lt;div /&gt;代表一个DOM tag，但是<Welcome />代表一个组件，同时需要把Welcome添加到作用域中。

## Composing Components （构造组件）

在react中，一个组件的输出通常可以依赖于其它组件，这使得你可以使用组件来描述任何页面内容，比如：一个按钮、一个表单，一个对话框，一屏内容等；所有这些通常都可以表达为一个组件。

例如，我们可以创建一个App component ，它内部多次引用了Welement组件，如：

```jsx 
	function Welcome(props){
		return <h1>Hello,{props,name}</h1>;
	};

	function App(){
		return (
			<div>
				<Welcome name="Sara"/>
				<Welcome name="Cahal"/>
				<Welcome name="Edite"/>
			</div>
		);
	};

	ReactDOM.render(
		<App />,
		document.getElementById('root')
	);
```

通常情况下，一个新建的React App在顶层只有一个App组件，然而，如果你是集成react到一个已存在的app里面，你将会自下而上的创建一些小组件，比如一个按钮组件等等，然后逐步到顶层中。

* **警告**

react components只能有一个根元素，所以我们创建App组件的时候会使用一个div把所有的Welcome组件包起来。

## Extracting Components（分离、提取组件）

不要害怕去把一个复杂、庞大的组件分离层一些小的组件。

例如，我们看一下下面的评论component:


```jsx
	function Comment(props){
		return (
			<div className="Comment">
				<div className="UserInfo">
					<img className="Avatar"
						src={props.author.avatarUrl}
						alt={props.author.name}
					/>
					<div className="UserInfo-name">
						{props.author.name}
					</div>
				</div>
				<div className="Comment-text">
					{props.text}
				</div>
				<div className="Comment-date">
					{formatDate(props.date)}
				</div>
			</div>
		);
	};

```

首先我们看一下这个组件描述的UI需要那些数据（props）:包含一个author对象、一个字符串text、一个日期字符串。它描述了一个在社交网站上评论板块内容。

这个组件首先很难去改里面数据，因为嵌套了很多层，同时它很难复用代码，因为组件每部分的耦合性太强了，是连在一起的，我们对它进行适当的提取来改变这种情况。

首先，我们将提取一个Avatar组件：

```jsx
	function Avatar(props){
		return (
			<img className="Avatar"
				src={props.user.avatarUrl}
				alt={props.user.name}
			/>
		);
	};
```

这个Avatar是一个独立的组件，所以它并不需要知道它将在用在Comment组件里面，这也是为什么我们给了一个更通用的user属性名，而不是auther。

我们建议在给一个组件取属性名的时候，名字应该根据这个它的功能来取，而不需要根据它将使用的上下文环境来命名。

现在我们就可以简化上面的Comment组件一点点了，代码如：

```jsx
	function Comment(props){
		return (
			<div className="Comment">
				<div className="UserInfo">
					<Avatar user={props.author}/>
					<div className="UserInfo-name">
						{props.author.name}
					</div>
				</div>
				<div className="Comment-text">
					{props.text}
				</div>
				<div className="Comment-date">
					{formatDate(props.date)}
				</div>
			</div>
		);
	};
```

下一步，我们将提取UserInfo组件，代码如下：

```jsx
	function UserInfo(props){
		return (
			<div className="UserInfo">
				<Avatar user={props.user}/>
				<div className="UserInfo-name">
					{props.user.name}
				</div>
			</div>
		);
	};
```

现在，我可以进一步简化我们的comment组件：


```jsx
	function Comment(props){
		retrun (
			<div className="Comment">
				<UserInfo user={props.author}/>
				<div className="Comment-text">
					{props.text}
				</div>
				<div className="Comment-date">
					{formatDate(props.date)}
				</div>
			</div>
		);
	};
```

提取组件这项工作一开始总是很繁重的工作，但是你这样做了的话，那么将有利于你后期复用代码，更利于构建大型的app。一个良好的经验法则：如果你写的一个组件包含了很多概念上能却别开来独立的部分，比如，一个对话框里面分别包含了标题，按钮，文字等等；或者，你写的组件非常庞大，那么可以确信，你的组件非常需要进一步提取小组件。

## Props are Read-Only（props是只读的，不要尝试修改它的值）

无论你是采用函数式还是类式创建组件，都不能修改的它的props值，来看一下下面的sum函数:

```javascript
	function sum(a,b){
		return a + b;
	};
```

我们称这样的函数为[pure](https://en.wikipedia.org/wiki/Pure_function)函数，因为他不会企图修改的输入值，而且同样的参数，总是返回同样的值。

作为对比，下面的函数就是不纯的，因为它修改了自己的输入参数：

```javascript
	function withdraw(account,amount){
		account.total -= amount;
	};
```

react是非常灵活的，但是它有一条非常严格的规则：

**一个组件必须扮演成一个pure的函数，它会尊重它的props值，不要尝试修改props值。**

当然了， 应用程序用户界面是动态的和时刻在变的，在[下一个章节](https://facebook.github.io/react/docs/state-and-lifecycle.html)，我们将介绍另一个概念"state"，state就不遵守这条规则，state可以允许你在任何时间修改它的输出值。