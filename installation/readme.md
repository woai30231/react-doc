react是非常灵活的，可以应用在各种类型的项目中。你可以用它建立一个新的app，同时你也可以逐步将它用到一个已存在的代码库中，而不需要重写原有的项目！

## 试一试React（Try React）

### 试行React

如果你只是对react感兴趣，那么你可以使用[CodePen](http://codepen.io/gaearon/pen/rrpgNB?editors=0010)，并运行一下里面的代码查看结果，同时你不需要安装任何东西。你也可以尝试修改一下源代码并能看到代码改过之后react怎么工作！

当然了，你可以使用你自己的编辑器，并下载这个文件，随后编辑它，然后用你本地系统的浏览器去浏览，它将会产生一种缓慢的响应信息，所以不要在你的实际生产环境中这样使用！

###　代码

``` jsx
	ReactDOM.render(
	  <h1>Hello, world!</h1>,
	  document.getElementById('root')
	);

```

## 创建一个新app（Create a New App）

### Creating a New Application

[Create React App](https://github.com/facebookincubator/create-react-app)是一种很好的创建spa的方式，它会帮你搭建好开发环境——你就能使用最新的javascript语言的新特性了。同时，它为你提供一个很好的开发体验和帮助你优化app等！ 

为了使用[Create React App](https://github.com/facebookincubator/create-react-app)搭建这个环境，你需要使用到[nodejs](https://nodejs.org/)和[npm](https://www.npmjs.com/)（或[yarn](https://yarnpkg.com/en/)），并在终端输入以下命令：

```bash
	npm install -g create-react-app
	create-react-app my-app

	cd my-app
	npm start

```

上面的操作不会涉及任何后台逻辑和数据库，它只是创建了一个前端项目构建流。所以你可以自己选择使用任何你想用的后台逻辑。它使用了构建工具有[Babel](https://babeljs.io/)和[Webpack](https://webpack.github.io/)，但是这些都不需要你来配置，它已经帮你默认配置好了！

最后当你需要部署你的代码到实际生产环境中时，你只需要使用*npm run build*就会创建一个优化好的app到build文件夹下，你可以在[from its README](https://github.com/facebookincubator/create-react-app#create-react-app-)和[User Guide](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#table-of-contents)学到更多相关信息！


## 把react添加到一个已存在的app中

### Adding React to an Existing Application

首先，你并不需要重写你的app！

我们推荐你把react用在你app的某个单元中，比如某个相对独立的组件：评论、侧边栏等！你将会看到react很好地工作在你的app中。

如果你不能在一个自动构建流中使用react，我们建议你配置构建工具来使你的工作更富有成效。一个现代构建流通常由以下几个部分构成：

* **一个包管理工具**，比如Yarn和npm。它们能使你更好的管理第三方库、依赖、安装及更新等等！

* **一个打包工具**，比如webpack和browserify。它们让你的代码模块化，并且能把你的代码打包到一个文件中来优化用户响应时间！


* **一个编译器**，如Babel，它能让你使用最新的javascript的新特性！最后编译成能在老浏览器中使用的代码！


###　Installing React

我们推荐使用Yarn或者npm来管理前端各种依赖库，如果你对这方面还不是很熟悉的话，那么[Yarn documentation](https://yarnpkg.com/en/docs/getting-started)是一个你很好的了解这个工具的地方！

如果使用Yarn安装React，命令如下：

```bash
	yarn init
	yarn add react react-dom
```

如果使用npm安装React，命令如下：

```bash
	npm init
	npm install --save react react-dom
```

Yarn和npm都是从[npm registry](https://www.npmjs.com/)下载这些第三方库！

### Enabling ES6 and JSX

我们推荐在使用react过程中使用Babel，它可以让你在你的javascript代码中使用es6和jsx语法，es6是一种更简单的现代javascript，而jsx是一种扩展于javascript的语言，它可以在react中很好地使用！

[Babel setup instructions](https://babeljs.io/docs/setup/)解释了怎么在各种构建环境中配置babel，在此之前，你需要确定安装了*babel-preset-react*和*babel-preset-es2015*，同时在你的[.babelrc configuration](http://babeljs.io/docs/usage/babelrc/)文件中启用它们，那么现在你可以开始了！

### Hello World with ES6 and JSX

我们建议你使用打包工具如*webpack*或*Browserify*，这样你就能写模块化的代码，并最终能将所有文件打包到一个文件中以此来优化页面加载时间！

最简单的react例子如下：

```jsx
	import React from 'react';
	import ReactDOM from 'react-dom';

	ReactDOM.render(
		<h1>Hello,world!</h1>,
		document.getElementById('root')
	);

```

这段代码的作用就是在你的html文件的某个地方找到id为root的dom，并将hello,world!渲染进去，从而呈现在页面上！

同理，你也可以把一个第三方UI库写的react Component渲染到一个已存在的app中！

### Development and Production Versions

默认情况下，React在开发过程中，包含很多有用的提示信息，这些歇息将有助于你开发，可是，它们使你的文件变得更大和更慢，所以你需要在部署代码到生产环境中去除这新信息，也就是需要一个生产版本的app！有以下这些工具帮助你实现代码从开发版本变到生产版本：

#### Branch

详情去看[Branch docs](http://brunch.io/docs/commands)！

#### Browserify

详情去看[Browserify docs](https://github.com/substack/node-browserify#usage)！

#### Rollup

详情去看[Rollup docs](https://github.com/rollup/rollup)！

#### Webpack

把*DefinePlugin*和*UglifyJsPlugin*设置项配置到你的webpack配置文件中，详情去看[this guide](https://webpack.js.org/guides/production-build/)！

### Using a CDN

当然了，如果你不想使用npm管理包，那么我们也在我们的cdn的dist目录下存放了*react*和*react-dom*，你可以在你的文件中这样引入它们：

```html
<script src="https://unpkg.com/react@15/dist/react.js"></script>
<script src="https://unpkg.com/react-dom@15/dist/react-dom.js"></script>
```

当然了，上面的版本适合用在开发版本中，如果需要在生产版本中使用，需要这样引入：

```html
	<script src="https://unpkg.com/react@15/dist/react.min.js"></script>
	<script src="https://unpkg.com/react-dom@15/dist/react-dom.min.js"></script>
```

如果你需要不同版本的react，那么只需要改动15到你需要的版本编号就可以了，如果你使用了Bower，那么可以通过bower安装react！
