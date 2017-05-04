## Rendering Elements（渲染节点）

Elements（节点）是React apps中最简单的构建块。

一个Elmeent定义了你将在屏幕上看到什么内容，如：

```jsx
	const element = <h1>Hello,world</h1>;
```

不像浏览器中的DOM节点，React elements其实就是一个普通的对象，你可以很容易的创建它，然后由React DOM来保持element和DOM的更新同步！

* **注意**

一种使人困惑的观念就是把Elements称之为Components，我们将在下一个章节介绍Components！其实Components就是由Elements组合构建起来的，如果作一个比喻：在一个国家中，国家就是一个component，而每一个人就是一个element，由所有的这个国家的人（element）构成了这个国家（component）。我们鼓励你先看下element的讲解，再去看component。
