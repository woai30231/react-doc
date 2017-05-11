## Forms（表单回顾、概览）

HTML中的表单跟React中的表单还是有一点点的不同，因为HTML表单天生的就会包含内部的状态。例如：下面的HTML表单接收一个简单的name值：

```jsx
    <form>
        <label>
            Name:
            <input type="text" name="name" />
        </label>
        <input type="submit" value="Submit" />
    </form>
```
我们在浏览器中打开这个页面的时候，提交表单会使得触发表单一些默认的行为。如果我们把这段代码放到react中，也是能正常工作的。但是在多数情况下，我们需要实现一个函数来控制用户提交表单操作，或者我们需要一个管理用户输入数据的权利。我们要在react中的实现的这个技术，术语称作“控制组件”！

## Controlled Components （用react把表单联系起来并控制表单）

在html中，一些表单标签，比如：input、textarea及select等，通常它们会维护它们自己的状态并且通过用户当前的输入适时的更新这个状态！在React中，我们会在组件内部定义定义了一些异变化的状态（state），而且我们只能通过setState方法来改变这些state。

其实，我们可以在React中组合这两种行为方式，只要保证state是单一来源的。然后我们的react组件就可以渲染表单并控制当前表单上所有用户的输入。如果一个input表单的值被react控制了，那么就称它是一个“受控制的组件”。

例如，如果我们想改变一下前面的表单代码，实现当用户提交表单的时候，控制台打印一下当前的name值，那么我们就需要实现一个受控制的组件。如：

```jsx
    class NameForm extends React.Component{
        constructor(props){
            super(props);
            this.state = {value:''};

            this.handleChange = this.handleChange.bind(this);
            this.handleSubmit = this.handleSubmit.bind(this);
        };

        handleChange(event){
            this.setState({value:event.target.value});
        };
        handleSubmit(event){
            alert('A name was submitted: ' + this.state.value);
            event.preventDefault();
        };

        render(){
            return (
                <form onSubmit={this.handleSubmit} >
                    <label>
                        Name:
                        <input type="text" value={this.state.value} onChange={this.handleChange} />
                    </label>
                    <input type="submit" value="Submit"/>
                </form>
            );
        };
    };

```

当我们用组件的this.state.value来设置表单的input的value的时候，我们的state就和input表单单一地联系起来了。每当用户改变一次表单值，那么this.state.value和input的值会保持同步更新。

在这个受控制的表单中，组件的state和表单的值是通过一个函数同步联系起来的。例如，如果我们想用户界面上的表单值为大写的话，那么可以这样改变this.handleChange函数：

```jsx
    handleChange(event){
        this.setState({value:event.target.value.toUpperCase()});
    }
```

## The textarea Tag （React中的textarea表单）

在html中，textarea标签的值是根据它子元素来决定的！如：

```html
    <textarea>
        Hello there,this is some text in a text area.
    </textarea>
```
但是在react中，我们可以是通过value属性表示它的值的！这种方法使得在react中书写textarea标签变得非常简短，一行足以搞定。如：

```jsx
    class EssayForm extends React.Component{
        constructor(props){
            super(props);
            this.state = {
                value : 'Please write an essay about your favorite DOM element'
            };
            this.handleChange = this.handleChange.bind(this);
            this.handleSubmit = this.handleSubmit.bind(this);
        };

        handleChange(event){
            this.setState({value:event.target.value});
        };
        handleSubmit(event){
            alert('An essay was submitted: '+ this.state.value);
            event.preventDefault();
        };
        render(){
            return (
                <form onSubmit={this.handleSubmit} >
                    <label>
                        Name:
                        <textarea value={this.state.value} onChange={this.handleChange} />
                    </label>
                    <input type="submit" value="Submit" />
                </form>
            );
        };
    };
```
这里注意，this.state.value已经有一个初始值了，所以textarea也会有一个初始值。

## The select Tag（React中的select表单）

在html中，select标签会呈现一个下拉式的列表样式，如，我们用select来实现一个下拉式的关于味道选项的下拉列表：

```html
    <select>
        <option value="grapefruit">Grapefruit</option>
        <option value="lime">Lime</option>
        <option selected value="cocount">Cocount</option>
        <option value="mango">Mango</option>
    </select>
```
在上面的代码中，我们知道有一个初始被选择的选项：Cocount，因为option标签上包含了一个selected属性。在React中，我们会在select元素上使用selected属性来代替在option标签上使用selected属性，从而初始化默认选择的选项，这样就会极大地方便管理，因为只会在一个地方改代码，如：

```jsx
    class FlavorForm extends React.Component{
        constructor(props){
            super(props);
            this.state = {value:'cocount'};

            this.handleChange = this.handleChange.bind(this);
            this.handleSubmit = this.handleSubmit.bind(this);
        };
        handleChange(event){
            this.setState({
                value : event.targat.value
            });
        };
        handleSubmit(event){
            alert('Your favorite flavor is: ' + this.state.value);
            event.preventDefault();
        };
        render(){
            return (
                <form onSubmit={this.handleSubmit}>
                    <label>
                        Pick your favorite La Croix flavor:
                        <select value={this.state.value} onChange={this.handleChange}>
                             <option value="grapefruit">Grapefruit</option>
                             <option value="lime">Lime</option>
                             <option value="coconut">Coconut</option>
                             <option value="mango">Mango</option>
                        </select>
                    </label>
                    <input type="submit" value="Submit" />
                </form>
            );
        };
    };
```
总体上来看，在React中，input、select及textarea标签工作都非常简单，都接受一个value属性来实现一个受控制的表单组件。

## Handling Multiple Inputs （）

当你需要处理多个input标签的时候，你可以在每个input元素上添加一个name属性，这样处理函数就可以根据event.target.name来控制对应的input了！如：

```jsx
    class Reservation extends React.Component{
        constructor(props){
            super(props);
            this.state = {
                isGoing : true,
                numberOfGuests : 2
            };
            this.handleInputChange = this.handleInputChange.bind(this);
        };
        handleInputChange(event){
            const target = event.target;
            const value = target.type === 'checkbox'?target.checked : target.value;
            const name = target.name;
            this.setState({
                [name]:value
            });
        };
        render(){
            return (
                <form>
                    <label>
                        Is going:
                        <input name="isGoing" type="checkbox" checked={this.state.isGoing} onChange={this.handleChange} />
                    </label>
                    <br />
                    <label>
                        Number of guests:
                        <input name="numberOfGuests" type="number" value={this.state.numberOfGuests} onChange={this.handleInputChange} />
                    </label>
                </form>
            );
        };
    };
```

这里需要注意的是，我们是怎么用es6[computed property name](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)语法来改变对应的状态值的。如：

```jsx
    this.setState({
        [name]:value
    });
```
这段代码跟下面的代码是等价的，如：

```jsx
    var partiaState = {};
    parialState[name] = value;
    this.setState(partiialState);
```
另外，由于setState方法合并了更改的状态到当前状态对象，我们只需要利用它来实现改变的部分就好。

## Alternatives to Controlled Components （替代方案）

综上所述，实现一个受控制的组件，有时候是一件很烦闷的事情，因为你要写很多事件监听函数来保持表单值和state的同步。这尤其在那种预先存储数据或集成react到一个没有使用react的框架里面是非常讨人厌的。所以衍生了一种替补方案来解决这个问题([uncontrolled components](https://facebook.github.io/react/docs/uncontrolled-components.html))。