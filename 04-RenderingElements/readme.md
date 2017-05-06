## Rendering Elements（渲染节点）

Elements（节点）是React apps中最简单的构建块。

一个Elmeent定义了你将在屏幕上看到什么内容，如：

```jsx
	const element = <h1>Hello,world</h1>;
```

不像浏览器中的DOM节点，React elements其实就是一个普通的对象，你可以很容易的创建它，然后由React DOM来保持element和DOM的更新同步！

* **注意**

一种使人困惑的观念就是把Elements称之为Components，我们将在下一个章节介绍Components！其实Components就是由Elements组合构建起来的，如果作一个比喻：在一个国家中，国家就是一个component，而每一个人就是一个element，由所有的这个国家的人（element）构成了这个国家（component）。我们鼓励你不要跳过本章关于element的讲解，而直接查看component。

## Rendering an Element into the DOM（把element渲染到dom中）

我们说过在你的html文件某个地方总有下面的“div”存在：

```jsx
	<div id="root"></div>
```

我们之所以称之为根节点，是因为所有在其内部的内容都由React DOM来管理。如果你的app只是用react来构建的话，那么你的页面只会存在一个单一的root节点，但是如果你用react来集成一部分内容到一个已经搭建好的app里面，那么你就可以拥有很多的root节点，当然如果你喜欢的话！

为了把element渲染到dom，需要使用ReactDOM.render():

```jsx
	const element = <h1>Hello,world</h1>;
	ReactDOM.render(
		element,
		document.getElementById('root')
	);
```

上面的代码将会使你在页面中看到“Hello,World”。

## Updating the Rendered Element（更新一个已经渲染过的element）

React elements 是不可变的，只要你创建过一次，那么你就不能修改的它的子节点或者属性，一个Element就好像一部电影的某个瞬间的那一帧照片一样，它代表了用户界面在某个确定时间瞬间的具体状态。

随着我们对react掌握的逐步加深，我们会明白更新用户界面只有一个方法，那就是创建一个新的Element，然后把它传递给ReactDOM.render()，再渲染到页面中。

我们先来看一下关于时钟的例子：

```jsx
 function tick(){
 	const element = (
 		<div>
 			<h1>Hello,world!</h1>
 			<h2>It is {new Date().toLocaleTimeString()}.</h2>
 		</div>
 	);
 	ReactDOM.render(
 		element,
 		document.getElementById('root')
 	);
 	setTimeout(tick,1000);
 };
 tick();
 //setInterval(tick,1000);
```
这段代码会每隔1秒钟调用一次ReactDOM.render()，这里可以使用setInterval或setTimeout。

* **注意**

在实际开发过程中，React apps应该只能调用一次ReactDOM.render()，我们将在下一个章节学习怎样把上面的功能封装到一个[stateful components](https://facebook.github.io/react/docs/state-and-lifecycle.html)中，我们建议先看完这里，因为它们是彼此依赖的。

## React Only Updates What's Necessary（ReactDOM只会更新需要更新的节点）

React在更新dom的时候，会将当前dom跟前一次的dom作一个对比，然后只会更新有变化的节点，这一点你可以通过浏览器的开发者工具查看代码变化情况得知，如：

![](https://facebook.github.io/react/img/docs/granular-dom-updates.gif)

由上面的图片得知，尽管我们我们每一次调用tick方法的时候，我们创建了一个完整的用户界面dom树，但是只有一个文本节点的内容发生了变化，所有ReactDOM只会更新这个文本节点。

以我们以往开发react的经验来看，思考每个确定的时间点，用户界面应该显示什么内容，而不是考虑时刻去修改它可以消除一些类的bug问题。


