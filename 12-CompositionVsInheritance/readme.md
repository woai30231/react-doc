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