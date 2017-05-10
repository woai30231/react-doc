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

