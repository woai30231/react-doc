# 推荐阅读

[关于react生命周期相关的介绍](https://developmentarc.gitbooks.io/react-indepth/content/react_basics/introduction.html)

## React.Component

components组件思想使得你在开发用户页面的时候，把界面看成是一个个独立、可重用的子块，并且每个块都是隔离于其它板块的。React.Component是React上提供的一个方法！

## Overview

React.Component是一个抽象基础类，我们很少直接提伦它，相反，我们更倾向于关心它的子类，我们在定义组件的时候，最少应该定义一个render方法。

通常情况下，你可能像下面这样定义一个组件：

```jsx
    class Greeting extends React.Component{
        render(){
            return <h1>Hello,{this.props.name}</h1>;
        };
    };
```

如果你没有使用过es6语法，那么你可以使用[create-react-class](https://facebook.github.io/react/docs/react-api.html#createclass)模块来代替，你可以查看[Using React without ES6](https://facebook.github.io/react/docs/react-without-es6.html)来得到更多信息！

### The Component Lifecycle (组件的生命周期)

每个组件都有一个可重写的生命周期方法，这些方法可以用来在组件生命周期每个特别的时间段执行一些操作。方法名字中带有will前缀的表示在某个时间点之前调用，方法名字中带有did前缀的表示在某个时间点之后执行。

#### Mounting

下面的这些方法将会在组件实例被创建之后并且插入到页面中的时候执行：

* [constructor()](https://facebook.github.io/react/docs/react-component.html#constructor)

* [componentWillMount()](https://facebook.github.io/react/docs/react-component.html#componentwillmount)

* [render()](https://facebook.github.io/react/docs/react-component.html#render)

* [componentDidMount()](https://facebook.github.io/react/docs/react-component.html#componentdidmount)

#### Updating

当一个组件的state或props变化的时候，会使得component发生更新操作！下面这些方法将会在组件重渲染的时候执行：

* [componentWillReceiveProps()](https://facebook.github.io/react/docs/react-component.html#componentwillreceiveprops)

* [shouldComponent()](https://facebook.github.io/react/docs/react-component.html#shouldcomponentupdate)

* [componentWillUpdate()](https://facebook.github.io/react/docs/react-component.html#componentwillupdate)

* [render()](https://facebook.github.io/react/docs/react-component.html#render)

* [componentDidUpdate()](https://facebook.github.io/react/docs/react-component.html#componentdidupdate)

#### Unmounting

下面这个方法将会在一个组件从DOM中移除的时候调用：

* [componentWillUnmount()](https://facebook.github.io/react/docs/react-component.html#componentwillunmount)

### Other APIS

每个组件实例都提供了以下这些方法：

* [setState](https://facebook.github.io/react/docs/react-component.html#setstate)

* [forceUpdate](https://facebook.github.io/react/docs/react-component.html#forceupdate)

### Class Properties （类属性）

* [defaultProps](https://facebook.github.io/react/docs/react-component.html#defaultprops)

* [displayName](https://facebook.github.io/react/docs/react-component.html#displayname)

### Instance Properties （实例属性）


* [props](https://facebook.github.io/react/docs/react-component.html#props)

* [state](https://facebook.github.io/react/docs/react-component.html#state)

### Reference (方法和属性使用详情)

##### render()

```jsx
    render()
```

首先说明一下，一个组件里面render方法是必须的，也就是你在定义一个组件子类的时候，可以其它什么数据都没有，但是必须要有这个render方法。

当这个方法调用的时候，它会去检查this.props和this.state值，最后返回一个单一的React element，（这里记住一定是单一的，如果你返回了几个elements，那么你需要用一个容器把这里elements包起来）这个element可以是浏览器原生的element，比如&lt;div /&gt;，也可以是我们自己定义的组件，比如&lt;Myapp /&gt;。

你可以通过这个方法返回一个null或false值，来表明你不想渲染什么内容到用户界面中。当返回null或false的时候，那么调用ReactDOM.findDOMNode(this)将返回null。

render方法应该是一个单纯的js函数，也就是说，多次调用它返回的内容都应该是一样的，或者说，你不能在render方法里面执行修改state或props的操作。同时你也不应该把用户交互需要执行的操作写在这个方法里面，而应该把用户交互相关的操作放在componentDidMount()或者其它生命周期函数里面。总的一句话，尽量保持render方法里面内容逻辑简单。

**注意：如果[shouldComponentUpdate()](https://facebook.github.io/react/docs/react-component.html#shouldcomponentupdate)返回false的时候，render()将不会执行**

##### constructor()

```jsx
    constructor(props)
```

constructor方法将在组件未挂载到页面中的时候执行。我们在React.Component的子类中实现constructor方法的时候，我们需要在其内部所有声明之前使用super(props)语句，否则，我们我们引用this.props的时候，其值将为undefined，从而导致bug。

constructor方法内部是初始化state的是绝佳位置，但是如果你不需要初始化state，或者为你的方法绑定上下文的话，那么你可以不书写constructor方法。
 
我们可以在初始化state的时候用到props，这是有效的！这里有一个有效的React.Component子类constructor方法实现：

```jsx
    constructor(props){
        super(props);
        this.state = {
            color : props.initialColor
        };
    };
```

当心这种模式，因为当我们的props更新的时候，我们的state并不会和props保持同步，为了达到同步，那么通常情况下，我们需要[lift the state up](https://facebook.github.io/react/docs/lifting-state-up.html)。

你也可以通过调用[componentWillReceiveProps(nextProps)](https://facebook.github.io/react/docs/react-component.html#componentwillreceiveprops)来保持state和props的同步更新，但是最好使用lift the state up，因为这样实现更容易，而且少bug。

##### componentWillMount()

```jsx
    componentWillMount()
```

componentWillMount()方法将会在组件被挂载值钱立即调用，同时他会在render()方法值钱调用，所以在这个方法里面同步地设置state，并不会导致重渲染。避免在这个方法里面使用任何有副作用或者服务。

这是唯一一个为渲染服务的方法，通常地，我们推荐用到的它的地方用constructor方法实现。

##### componentDidMount()

```jsx
    componentDidMount()
```

这个方法将会在组件被挂载的时候立即调用。这个方法将会是我们处理逻辑很好的地方，处理过后将会产生重渲染。

##### componentWillReceiveProps()

```jsx
    componentWillReceiveProps(nextProps)
```

这个方法将会在组件挂载之后并且即将接收到新的props之前调用。如果你需要用改变的props值来更新你的state值，那么你就需要在其内部执行this.setState()方法。

记住，这个方法将会在props值没有改变的情况下也会调用，所以请确定比较一下当前props和nextProps值，以确保只在值改变的情况这行相关操作，这种情况通常出现在一个父组件导致子组件的重渲染。

这个方法不会在[挂载周期时间内](https://facebook.github.io/react/docs/react-component.html#mounting)用初始props调用。只会在组件渲染之后，并且相关组件更新了props的时候调用！通常调用this.setState()方法不会触发这个方法的调用。

##### shouldComponentUpdate()

```jsx
    shouldComponentUpdate(nextProps,nextState)
```

调用这个方法主要是因为组件的输出跟不受当前props或state的改变的影响。通常情况下，我们只会在state改变你的情况下，才会重渲染组件，大多数情况都是如此。

该方法在组件接收到新的state或者props的时候调用，默认返回true。不会在初次初始state或props的时候调用，或者当forceUpdate()调用的时候。

返回false不能阻止子组件的重渲染。

正确地，如果该方法返回false，那么componentWillUpdate(),render(),和ComponentDidUpdate()都不会调用。注意，在未来，React可能把这个方法当成一个暗示而非一个严格的指令。返回false依然能够导致重渲染。

如果你想分析一下你的组件的性能，你可以通过实践React.PureComponent来实现组件，并且通过对比相关state或者props前后值，最后返回false来告诉React，更新可以跳过。

