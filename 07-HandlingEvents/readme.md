## Handling Events（事件处理）

React中的事件处理跟dom中的事件处理是差不多的，只不过在语法上面有几处不同：

_ 1、react事件处理属性命名采用驼峰式命名规则，而不是全部大小写，例如，react采用onClick，而dom中采用的是onclick；

_ 2、在jsx你需要传递一个函数作为事件监听器，而不是一个字符串，如：

```jsx 
	function fn(){
		alert('我点击了div');
	};
	const element = <div onClick={fn}>点击</div>;
	ReactDOM.render(
		element,
		document.getElementById('root')
	);
```

我们先来看一个例子，看下它们之间有什么不同！

首先在html中：

```html
	<button onclick="activalteLasers()">
		Activate Lasers
	</button>
```

同样的目的，在react中稍稍有点不同：

```jsx
	<button onClick={activateLasers}>
		Activate Lasers
	</button>
```
还有一个不同就是，你不能像普通html那样用return false来阻止某些浏览器默认行为，你必须明确的调用preventDefault方法。例如，在普通的html中，我们可以为a标签绑定点击事件，用return false来阻止默认的页面跳转行为，于是你的代码是这样的：


```html
	<a href="#" onclick="console.log('The link was clicked.');return false">
		Click me
	</a>
```

但是在react中，你得这样写：

```jsx
	function ActionLink(){
		function handleClick(e){
			e.preventDefault();
			console.log('The link was clicked.');
		};
		return (
			<a href="#" onClick={handleClick}>
				Click me
			</a>
		);
	};
```

这里注意一下，参数e是一个事件合成物，react定义这个e是按照w3c标准实现，所以你不用担心跨浏览器兼容问题。你可以从[这里](https://facebook.github.io/react/docs/events.html)得到更多react对e的信息。

在使用react的过程中，你可以不用在添加一个元素之后，然后再使用addEventListener方法为这个元素添加事件监听器。取而代之，你可以在元素调用render方法的时候提供一个事件监听器就好了。

在使用“类式”方式构建一个组件的时候，一个通常的模式就是把事件处理定义成组件的一个方法。例如，下面的Toggle组件，有个一个按钮用来在“on”和“off”状态之间切换：

```jsx
	class Toggle extends React.Component{
		constructor(props){
			super(props);
			this.state = {isToggleOn : true};

			//这个绑定上下文的代码是有必要的，因为这样可以确保你调用当前方法总是在保证上下文是当前对象
			this.handleClick = this.handleClick.bind(this);
		};

		handleClick(){
			this.setState(prevState=>({
				isToggleOn : !prevState.isToggleOn
			}));
		};

		render(){
			return (
				<button onClick={this.handleClick}>
					{this.state.isToggleOn?'ON':'OFF'}
				</button>
			);
		};

	};

	ReactDOM.render(
		<Toggle />,
		document.getElementById('root')
	);

```

你必须小心注意组件里面方法的“this”绑定问题，因为在类里面，这个是默认没有绑定上下文的，所以如果你忘记绑定了哈，那么它的值将是undefined。

这个问题不是react独有的，而是普遍存在的，你可以在[how functions work in JavaScript](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/)查找到相关线索。所以如果你没有在定义组件的时候直接在方法后面加上圆括号直接调用的话，那么你就必须进行上下文绑定处理。

当然了，如果你觉得进行绑定这一步会使你感觉很恼火的话，那么有两种方法可以避免：

* 方法一：如果你在使用[property initializer syntax](https://babeljs.io/docs/plugins/transform-class-properties/)，你就可以利用属性初始化工具来正确绑定上下文：

```jsx
	class LoggingButton extends React.Component{
		handleClick = () => {
			console.log('this is:',this);
		}
		render(){
			return (
				<button onClick={this.handleClick}>
					Click me
				</button>
			);
		};
	};
```

这种方式已经在你使用[Create React App](https://github.com/facebookincubator/create-react-app)搭建react环境的时候默认支持。

* 方法二：使用箭头函数[arrow function](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)。如：

```jsx
	class LoggingButton extends React.Component{
		handleClick(){
			console.log('this is:',this);
		};
		render(){
			//这种方法可以使得帮当前对象“this”绑定到handleClick里面
			return (
				<button onClick={(e)=>this.handleClick(e)} >
					Click me
				</button>
			);
		};
	};
```

这种方式有个不同的地方就是，每次渲染LoggingButton的时候都会调用一次回调，在大多数情况下，这样做没有什么问题，但是当这个方法传递给下一级的组件做props属性时，那么这些下一级的组件将不会no-rendering。我们通常推荐你用第一种方法来避免这种类型的问题！