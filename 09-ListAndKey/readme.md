## Lists and Keys （列表和键）

首先，我们回顾一下在javascript中怎么去变换列表。

下面了的代码，我们用到了数组函数的map方法来实现数组的每一个值变成它的2倍，同时返回一个新数组，最后打印出了这个数组：

```javascript
	const numbers = [1,2,3,4,5];
	const doubled = numbers.map(number=>number * 2);
	console.log(doubled);
```
最终在控制台的打印结果是：[2,4,6,8,10]。

在React中，转换一个数组到列表，几乎是相同的。

下面，我们依次通过调用数组的map方法，并返回一个用li标签包含数组值当元素，最后分配它们到listItems数组里面：

```jsx
	const numbers = [1,2,3,4,5];
	const listItems = numbers.map(number => <li>{number}</li>);
```
现在我们把完整的listItems用一个ul标签包起来，同时[render it to the DOM](https://facebook.github.io/react/docs/rendering-elements.html#rendering-an-element-into-the-dom)。

```jsx
	ReactDOM.render(
		<ul>{listItems}</ul>,
		document.getElementById('root')
	);
```

这样就会在页面中显示一个带列表符号的ul列表，项目编号是从1到5。

## Basic List Component（一个基础的列表组件）

我们经常会在一个组件里面输出一个列表elements。

好，我们来写一个组件实现前面同样的功能，这个组件接受一个数字数组，最后返回一个无序列表。

```jsx
	function NumberList(props){
		const numbers = props.numbers;
		const listItems = numbers.map(number => <li>{number}</li>);
		return (
			<ul>
				{listItems}
			</ul>
		);
	};

	const numbers = [1,2,3,4,5];
	ReactDOM.render(
		<NumberList numbers={numbers}/>,
		document.getElementById('root')
	);
```

如果你运行上面的代码，你将会得到一个警告：你需要为每一个li元素提供一个key属性，这个“Key”字符串属性当你创建一个列表元素的时候必须添加。我们将在接下来讨论一下它为什么这么重要。

让我们在numbers.map()分配一个key属性到列表里面的li标签里来解决上面给出的警告提示问题，代码如下：

```jsx
	function NumberList(props){
		const numbers = props.numbers;
		const listItems = numbers.map(number => <li key={number.toString()}>{number}</li>);
		return (
			<ul>{listItems}</ul>
		);
	};

	const numbers = [1,2,3,4,5];
	ReactDOM.render(
		<NumberList numbers={numbers} />,
		document.getElementById('root')
	);
```

## Keys（如何设置的key属性值）

那我们为什么非要需要这个key属性呢？其实这个key属性可以帮助React确定一下那个列表选项改变了、是新增加的、或者被删除了，反正这个key属性就是用来让react跟踪列表在过去的时间发生了什么变化。key属性值应该在数组里面指定，这样就能保证列表元素就能有一个稳定的身份验证值。

```jsx
	const numbers = [1,2,3,4,5];
	const listItems = numbers.map(number => <li key={number.toString()}>{number}</li>);
```

最好的方法设置key值就是指定一个独一无二的字符串值来把当前列表元素同它的兄弟列表元素分离开来。但是通常情况下，你的后台给你的接口数据中都应该有一个当前数据为一个的"id"值，那么你就可以用这个id值来设置key属性值。代码大概像这样子：

```jsx
	const todoItems = todos.map(todo => <li key={todo.id}>{todo.text}</li>);
```

如果你的接口没有这样的一个id值来确定列表元素的key属性，那么最后的办法就是把当前列表的元素的索引值设置为key属性值了。如：

```jsx
	const todoItems = todos.map((todo,index) => (
		//只有在todo数据里面没有独一无二的id值的情况才这么做
		<li key={index}>
			{todo.text}
		</li>
	));
```

我们不推荐你使用索引值来作为key属性值，因为你的列表重新排序的时候，这样会严重使程序变得很慢。如果你愿意的话，可以在[这里（in-depth explanation about why keys are necessary）](https://facebook.github.io/react/docs/reconciliation.html#recursing-on-children)得到更多有关的信息！

## Extracting Components with Keys（当我们提取一个组件到另一个组件的时候，需要注意怎么管理key）

key属性只有在数组数据环境中才有意义，其它地方是没有意义的。

例如，当我们实现一个ListItem组件的时候，这个组件封装了一个li元素，那么我们不应该在li元素上直接设置key属性，因为没有意义，key是用来跟踪数组才有意义，于是我们在NumberList组件使用到ListItem组件的时候，在数组方法里面设置key属性才有意义。好，我们先来看一个错误设置key属性的版本：

```jsx
	function ListItem(props){
		const value = props.value;
		return (
			//这里是错误的，因为这里不需要指定key属性
			<li key={value.toString()}>
				{value}
			</li>
		);
	};

	function NumberList(props){
		const numbers = props.numbers;
		const listItems = numbers.map(number => (
			//这里也是错误的，因为这里才是真的需要指定key属性值的地方
			//记住一个要点就是:key属性只会在用到有关js处理数组有关的环境中用到
			<ListItem value={number} />
		));
		return (
			<ul>
				{listItems}
			</ul>
		);
	};
	const numbers = [1,2,3,4,5];
	ReactDOM.render(
		<NumberList numbers={numbers} />,
		document.getElementById('root')
	);
```

**正确地设置key属性版本的例子在下面：**

```jsx
	function ListItem(props){
		//正确的，这儿不需要设置key属性
		return (
			<li>{props.value}</li>
		);
	};

	function NumberList(props){
		const numbers = props.numbers;
		const listItems = numbers.map((number) => (
			//正确，这儿才是真的需要设置key属性的地方
			<ListItem key={number.toString()} value={number}/>
		));
		return (
			<ul>
				{listItems}
			</ul>
		);
	};

	const numbers = [1,2,3,4,5];
	ReactDOM.render(
		<NumberList numbers={numbers} />,
		document.getElementById('root')
	);
```
**为了帮你这里理解这一层，我自己的理解就是：并不是渲染到页面中的li标签需要key属性，（同时li标签也是没有关系的，我们在这里之所有用到li标签，只是更形象的说明问题，其实你也可以用div等等其它标签）之所要设置key属性，是React内部用来方便管理一个数组数据，跟踪数组里面的每一个数据！所以一个最好的法则就是，凡是需要调用map方法的时候你使用key属性就对了！**


## Keys Must Only Be Unique Among Siblings（key属性值相对于兄弟数据来说是独一无二的，记住只是相对于兄弟数据，其它数据没有关系）

key属性值只会跟当前（同一个）数组数据之间是独一无二的，而不用是全局独一无二的，例如，有两个数组，那么它们的key就可以是一样的。如：

```jsx
	function Blog(props){
		const sidebar = (
			<ul>
				{props.posts.map((post) => (
					<li key={post.id}>
						{post.title}
					</li>
				))}
			</ul>
		);
		const content = props.posts.map((post) => (
			<div key={post.id}>
				<h3>{post.title}</h3>
				<p>{post.content}</p>
			</div>
		));
		return (
			<div>
				{sidebar}
				<hr />
				{constent}
			</div>
		);
	};

	const posts = [
	  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
	  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
	];

	ReactDOM.render(
	  <Blog posts={posts} />,
	  document.getElementById('root')
	);
```
