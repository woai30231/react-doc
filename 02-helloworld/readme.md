## Hello World

最简单开始一个react案例的方法，就是使用我们的[ this Hello World example code on CodePen](http://codepen.io/gaearon/pen/ZpvBNJ?editors=0010)！你不需要安装任何东西，你只是需要在你的浏览器内另起一个选项打开我们的CodePen，然后跟随我们的代码一步一步的做并能够查看结果。当然了，如果你需要在本地搭建一个react开发环境，那么请到[installation](https://github.com/woai30231/react-doc/tree/master/installation)页面！

最简单的react例子代码是这样的：

```javascript

	ReactDOM.render(
		<h1>hello world!</h1>,
		document.getElementById('root')
	);
```

这段代码将会在html页面中的id为root的元素中渲染内容成‘hello world!’。


在接下来的几个章节中我们将逐步介绍怎么使用react，并且我们将探索构建react app时的两个最基本的构建快：elements（元素）和components（组件）。只要你熟练地掌握了它们，那么你就会用一些可复用的块来搭建更复杂的app！

## A Note on JavaScript（一个关于javascript的理解、注解）

React是一个javascript库，所以我们假定你对javascript已经很熟悉！如果你还不确定的话，我们建议你[ refreshing your JavaScript knowledge](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)，这将有助于你后期的学习！

我们同时在代码中使用了一些ES6语法，但我们会稍微克制一下使用ES6，因为es6语法对浏览器来说，相对来说还是比较新的技术。但我们鼓励你去学习一下es6的这些语法[arrow function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)(箭头函数)、[classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)(类)、[template literals](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals)(字符串模板)、[let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)及[const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)声明。你可以使用[Babel REPL](http://babeljs.io/repl/#?babili=false&evaluate=true&lineWrap=false&presets=es2015%2Creact&experimental=false&loose=false&spec=false&code=const%20element%20%3D%20%3Ch1%3EHello%2C%20world!%3C%2Fh1%3E%3B%0Aconst%20container%20%3D%20document.getElementById(%27root%27)%3B%0AReactDOM.render(element%2C%20container)%3B%0A)来把你的es6代码编译成非es6！