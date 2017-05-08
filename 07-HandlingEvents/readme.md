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