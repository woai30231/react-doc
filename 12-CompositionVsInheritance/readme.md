## Composition vs Inheritance （合成对比继承）

React拥有一个非常强大的合成模型，我们推荐在两个组件之间使用合成代替继承。

在这一个板块中，我们将带领您探索几个问题，这些问题偶尔也会涉及到继承，我们将告诉你怎样通过合成来解决这些问题。

### Containment （包含）

在开发过程中，有些组件是无法提前知道它的内容（childrend）的！特别是在编写一些比较通用的组件的时候，比如Slidbar、Dialog等！

我们推荐你在调用组件的render方法的时候，通过props的一个特殊属性children来代表将来可能存在的内容（children）。如：

```jsx
    function FancyBorder(props){
        return (
            <div className={'FancyBorder FancyBorder-' + props.color}>
                {props.children}
            </div>
        );
    };
```

这将会使得其它组件传递任意数据来作为内容（children）给FancyBorder。如：

```jsx
    function WelcomeDialog(){
        return (
            <FancyBorder color="blue">
                <h1 className="Dialog-title">
                    Welcome
                </h1>
                <p className="Dialog-message">
                    Thank you for visiting our spacecraft!
                </p>
            </FancyBorder>
        );
    };
```

任何在组件FancyBorder里面的内容都会被当作它的props.children属性。因为组件FancyBorder的render方法里面用一个div包含了{props.children}，所以我们可以看到最终输出了我们需要的用户界面。

当然了有些时候这种情况还不能适合于业务场景，有些时候你可能需要更灵活的控制Component，那么这个时候你就可能需要通过组件的props属性传递另外一个组件，来代替使用props.children属性。如：

```jsx
    function SplitPane(props){
        return (
            <div className="SplitPane">
                <div className="SplitPand-left">
                    {props.left}
                </div>
                <div className="SplitPane-right">
                    {props.right}
                </div>
            </div>
        );
    };

    function App(){
        return (
            <SplitPane left={<Contacts />} right={<Chat />} />
        );
    };
```

其实呀，在React中，一个组件就是一个对象，比如&lt;Contacts /&gt;、&lt;Chat /&gt;等！所以你可以把它们当作普通数据意义昂传递给组件的props属性。

### Specialization ()

有时候，我们可以这样理解一个组件：这个组件是另外一个组件的特定形式。比如，我们可以说组件WelcomeDialog是组件Dialog的特定形式。

在React中，这通常需要通过合成得到组件特定形式。更形象点说就，我们可以通过配置通用组件的props属性来传递相关数据实现特定形式的组件。如：

```jsx
    function Dialog(props){
        return (
            <FancyBorder color="blue">
                <h1 className="Dialog-title">
                    {props.title}
                </h1>
                <p className="Dialog-message">
                    {props.message}
                </p>
            </FancyBorder>
        );
    };

    function WelcomeDialog(){
        return (
            <Dialog title="Welcome" message="Thank you for visiting our spacecraft!" />
        );
    };
```