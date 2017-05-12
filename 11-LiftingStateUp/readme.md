## Lifting State Up（提升state）

通常情况下，在react中我们可能同时有几个组件修改同样的数据或者说跟同一个数据有关系。我们推荐在这几个组件最亲近的组件组件哪里提升一个共同的state来管理这些数据，让我们来看一下具体怎么实现！

在这个章节中，我们将创建一个温度计算器，用来实现计算水温为多少的时候，水将变为沸腾。

我们首先来实现一个BoilingVerdict，它接受一个celsius温度值作为props属性，用来表示是否足以让水沸腾。

```jsx
    function BoilingVerdict(props){
        if(props.celsius >= 100){
            return <p>The water would boil.</p>;
        };
        return <p>The water would not boil.</p>;
    };
```

接下来，我们将创建一个Calculator组件，它会向页面中渲染一个input表单以便让用户输入一个温度值，并且把这个温度值保存到自己的state里面。

```jsx
    class Calculator extends React.Component{
        constructor(props){
            super(props);
            this.state = {
                temperature : ''
            };
            this.handleChange = this.handleChange.bind(this);
        };
        handleChange(event){
            this.setState({
                temperature:event.target.value
            })
        };
        render(){
            const temperature = this.state.temperature;
            return (
               <fieldset>
                   <legend>Enter temperature in Celsius:</legend>
                   <input value={temperature} onChange={this.handleChange}/>
                   <BoilingVerdict celsius={parseFloat(temperature)} />
               </fieldset>
            );
        };
    };
```

## Adding a Second Input （添加第二个input表单）

我们现在需求是实现另外一个input表单，用来表示华氏温度，并且很摄氏温度保持同步更新。

我们可以从Calculator组件中提取一个TemperatureInput组件，我们可以给他添加一个scale属性，它的只可以为‘c’或者‘f’。

```jsx
    const scaleNames = {
        c:'Celsius',
        f:'Fahrenheit'
    };

    class TemperatureInput extends React.Component{
        constructor(props){
            super(props);
            this.handleChange = this.handleChange.bind(this);
            this.state = {temperature:''};
        };
        handleChange(e){
            this.setState({
                temperature:e.target.value
            });
        };
        render(){
            const temperature = this.state.temperature;
            const scale = this.props.scale;
            return (
                <fieleset>
                    <legend>Enter temperature in {scaleNames[scale]}:</legend>
                    <input value={temperature} onChange={this.handleChange} />
                </fieleset>
            );
        };
    };
```

现在我们改变一下我们刚才写的Calculator组件，让它向页面中渲染两个分离表示温度的input表单：

```jsx
    class Calculator extends React.Component{
        render(){
            return (
                <Temper
            );
        };
    };
```