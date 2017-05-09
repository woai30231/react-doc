## Conditional Rendering（有条件地渲染页面）

在React中你可以封装任意内容的用户界面Component，只要你有需要的话！然后，你可以在渲染这些组件到dom中的时候，根据需要，选择其中的一部分进行渲染，这个取决你的组件state。

在react中，使用条件渲染跟你在javascript中使用条件语句是一样的，你可以使用操作符[if](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else)或者[条件语句](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)等语法有条件地根据当前的state渲染Elements，最后再更新dom以显示符合当前elements的用户界面。

思考一下下面两个组件：

```jsx
	function UserGreeting(props){
		return <h1>Welcome back!</h1>;
	};

	function GuestGreeting(props){
		return <h1>Please sign up.</h1>;
	};
```

我们将创建一个Greeting组件来显示它们其中的一个，当然了具体显示哪一个是根据当前用户是否登陆来决定的！

```jsx
 	function Greeting(props){
 		const isLoggedIn = props.isLoggedIn;
 		if(isLoggedIn){
 			return <UserGreeting />;
 		}else{
 			return <GuestGreeting />;
 		};
 	};

 	ReactDOM.render(
 		<Greeting isLoggedIn={false}/>,
 		document.getElementById('root')
 	);
```

我们将看到，Greeting怎么渲染是根据其props属性isLoggedIn来决定的。

## Element Variables（element变量）

你可以使用一个变量来存储Elements，这样就能使你的组件组件不改变返回值的情况下有条件地渲染其需要的用户界面。

我们来看下下面的登入和登出按钮组件：

```jsx
	function LoginButton(props){
		return (
			<button onClick={props.onClick}>
				Login
			</button>
		);
	};

	function LogoutButton(props){
		return (
			<button onClick={props.onClick}>
				Logout
			</button>
		);
	};
```

在下面的代码中，我们将采用“类式”方式写一个有状态的LoginControl组件。

LoginControl组件将渲染<LoginButton />或者<LogoutButton />组件，这取决它当前的状态。同时会用到前面的<Greeting />:

```jsx
	class LoginControl extends React.Component{
		constructor(props){
			super(props);
			this.handleLoginClick = this.handleLoginClick.bind(this);
			this.handleLogoutClick = this.handleLogoutClick.bind(this);
			this.state = {
				isLoggedIn:false
			};
		};

		handleLoginClick(){
			this.setState({isLoggedIn:true});
		};

		handleLogoutClick(){
			this.setState({isLoggedOut:false});
		};
		render(){
			const isLoggedIn = this.state.isLoggedIn;

			let button = null;
			if(isLoggedIn){
				button = <LogoutButton onClick={this.handleLogoutClick}/>;
			}else{
				button = <LoginButton onClick={this.handleLoginClick} />;
			};

			return(
				<div>
					<Greeting isLoggedIn={isLoggedIn} />
					{button}
				</div>
			);
		};
	};
	ReactDOM.render(
		<LoginControl />,
		document.getElementById('root')
	);
```

采用变量存储element和使用if语句来进行条件渲染是一个非常好的用法实践。有些时候，你可能更喜欢更短一点的语句来进行条件渲染，我们将在下面解释怎么做到这样。

## Inline If with Logical && Operator（true && 表达式）

你可以用[大括号植入任何有效的js表达式](https://facebook.github.io/react/docs/introducing-jsx.html#embedding-expressions-in-jsx)，包括javascript逻辑和操作符，这样可以很方便地引入一个element：

```jsx
	function Mailbox(props){
		const unreadMessages = props.unreadMessages;
		return (
			<div>
				<h1>Hello!</h1>
				{unreadMessages.lendth > 0 && 
					<h2>
						You have {unreadMessages.length} unread messages.
					</h2>
				}
			</div>
		);
	};

	const messages = ['React','Re:React','Re:Re:React'];

	ReactDOM.render(
		<Mailbox unreadMessages={messages} />,
		document.getElementById('root')
	);
```

上面的代码之所以能正常工作，是因为在javascript中：true && expression 总是会执行后面的表达式，false && expression总是会忽略后面的表达式同时返回false。

因此，如果条件为true，那么在&&符号后面的element就会出现在render方法的输出中，反之，则会忽略并跳开它执行。

## Inline If-Else with Conditional Operator（if else语句）

在react中使用的其它条件渲染语法就是：condition?true:falsel。

在下面的例子中，我们使用它渲染一个文本块：

```jsx
	render(){
		const isLoggedIn = this.state.isLoggedIn;
		return (
			<div>
				The user is <b>{isLoggedIn ? 'currently':'not'}</b> logged in.
			</div>
		);
	};
```

我们也可以写得稍微复杂一点：

```jsx
	render(){
		const isLoggedIn = this.state.isLoggedIn;
		return (
			<div>
				{isLoggedIn ? (
					<LogoutButton onClick={this.handleLogoutClick} />
				):(
					<LoginButton onClick={this.handleLoginClick} />
				)}
			</div>
		);
	};
```

当然了，至于选择上面的那种写法取决于你的团队更喜欢那种，这样更利于团队合作和提高代码可阅读性。当然了，如果的组件已经非常复杂了，那么是时候[提取、分离](https://facebook.github.io/react/docs/components-and-props.html#extracting-components)你的组件了。

## Preventing Component from Rendering（）

有时我们希望隐藏我们的组件，即使组件在包含它的组件中被渲染时，我们也希望这样做。怎么做到如此呢？我们可以使用return null来代替它以前的输出。

在下面的例子中，组件<WarningBanner />是否渲染是根据它的props属性warn决定的，当我们不希望此组件在页面中渲染处内容的时候，我们可以返回null，如下：

```jsx
	function WarningBanner(props){
		if(!props.warn){
			return null;
		};
		return (
			<div className="warning">
				Warning!
			</div>
		);
	};

	class Page extends React.Component{
		constructor(props){
			super(props);
			this.state = {showWarning:true};
			this.handleToggleClick = this.handleToggleClick.bind(this);
		};
		handleToggleClick(){
			this.setState(prevState => ({
				showWarning : !prevState.showWarning
			}));
		};
		render(){
			return (
				<div>
					<WarningBanner warn={this.state.showWarning} />
					<button onClick={this.handleToggleClick}>
						{this.state.showWarning ? 'Hide':'Show'}
					</button>
				</div>
			);
		};
	};

	ReactDOM.render(
		<Page />,
		document.getElementById('root')
	);
```
我们返回null并不会影响一个组件的生命周期，像这些生命周期钩子函数依然会调用：componentWillUpdate、componentDidUpdate！
