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

**注意：如果(shouldComponentUpdate()](https://facebook.github.io/react/docs/react-component.html#shouldcomponentupdate)返回false的时候，render()将不会执行**

##### constructor()

```jsx
    constructor(props)
```