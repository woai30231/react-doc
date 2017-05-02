## Introducing JSX（介绍JSX）

首先，看一下下面的声明语句：

```jsx
	const element = <h1>Hello,world!</h1>;
```
这种有趣的标签语法既不是字符串也不是html，它被叫作JSX！JSX是js的扩展，我们推荐在react开发过程中使用jsx语法来描述一个UI，JSX语法会让你感觉这是一种模板语言，但是它就是诞生于javascript。

在react开发中，JSX语法用来生产React 'elements'，我们将在下一个板块中介绍'elements'！下面，我们先来看一下如何用必要的语法构建jsx语句！

## Embedding Expressions in JSX（把表达式植入到JSX中）

在jsx中，你可以用一个大括号来包上任何有效的js表达式，如：2+2，user.firstName，formatName(user)等等！

```jsx
	function formatName(user){
	  return user.X + ' ' + user.M;
	};

	var user = {
	  X : '名字的姓',
	  M : '名字的名'
	};
	var element = (
		<h1>
			你的姓名全称是： {formatName(user)}!
		</h1>
	);

	ReactDOM.render(element,document.getElementById('root'));
```


有时候，我们增加我们的代码的可阅读性，我们需要把代码分成多行，但记住这不是必要的，你完全可以写到一行里面！同时，如果我们把代码分成几行了，那么请记住，我们需要用圆括号括起来，这样可以有效的避免[automatic semicolon insertion](http://stackoverflow.com/questions/2846283/what-are-the-rules-for-javascripts-automatic-semicolon-insertion-asi)陷阱！

## JSX is an Expression Too（JSX也是一种表达式）

经过编译之后，JSX表达式就会变成合格的javascript对象！

这意味着，你可以在if语句或者for循环语句中使用jsx，为其分配变量，把它作为函数参数，以及作为函数的返回值，如下面代码：

```jsx
	function getGreeting(user){
		if(user){
			return <h1>Hello,{formatName(user)}!</h1>;
		};
		return <h1>Hello,Stranger.</h1>;
	};
```

## Specifying Attributes with JSX （为JSX指定属性）

你可以使用引号包含字符串字面值来作为属性，如：

```jsx
	const element = <div tabIndex="0"></div>;
```
你也可以使用大括号包含javascript表达式来作为属性，如：

```jsx
	const element = <img src={user.avatarUrl}></img>;
```

请不要在使用大括号的时候在外面加引号，否则，jsx将会以字符串字面值来对待，而并非以js表达式对待，你可以使用两种方式的任何一种（字符串字面值就用引号，js表达式就用大括号），但不要同时使用两种！

## Specifying Children with JSX（指定JSX子元素）

如果一个标签是空的，那么可以使用“/>”来提前关闭它，像XML语法一样，如：

```jsx
 const element = <img src={user.avatarUrl} />;
```

JSX标签可以包含子元素：

```jsx
	const element = (
		<div>
			<h1>Hello!</h1>
			<h2>Good to see you here.</h2>
		</div>
	);
```

* **警告**

因为jsx语法更接近javascript而非html，所以react dom使用驼峰命名法来命名html的属性名字！

例如，在JSX中，class属性变成了className，和tabindex变成了[tabIndex](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/tabIndex)！


## JSX Prevents Injectiioni Attacks（JSX阻止了注入攻击）

在jsx中，注入用户的输入值是安全的，如：

```jsx
	const title = response.potentiallyMaliciousInput;

	//this is safe：

	const element = <h1>{title}</h1>;
```

默认情况下，React DOM在向页面渲染之前，会避免[escapes](stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html)使用任何不在JSX中写入的值，因此，它可以确保你不会注入任何不在你app里面的数据，所有的数据在它被渲染到页面之前，都将被转换为字符串，这会帮助你阻止[XSS(cross-site-scripting)](https://en.wikipedia.org/wiki/Cross-site_scripting)攻击！

## JSX Represents Objects

Babel 把JSX编译成React.createElement()调用。

下面两个例子的作用是一样的：

```jsx
	//代码1
	const element = (
		<h1 className="greeting">
			Hello,world!
		</h1>
	);
```

```jsx
	
	//代码2
	const element = React.createElement(
		'h1',
		{className:'greeting'},
		'Hello,world!'
	);

```

React.createElement()执行几步简单的检查来帮助你确定写出没有问题的代码，但是本质上，它生成了一个对象，如下：

```jsx
	//Note:this structure is simplified
	const element = {
		type : 'h1',
		props : {
			className : 'greeting',
			children : 'Hello,world!'
		}
	};
```

上面这个对象，我们通常称之为“React elements”。你可以把它理解成这样一种东西————它描述了你在页面将会看到什么内容，React会读取这个对象，并使用它来构建我们的dom，而且时刻保持最新！

我们将在下一个板块探索如何渲染“React elements”到DOM！


* 小贴士：

我们推荐把“Babel”语法作为你的编译器的语法器，这样的你在使用es6或jsx的时候，你的代码将会高亮，更利于你编程！





