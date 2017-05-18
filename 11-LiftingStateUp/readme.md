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
                <div>
                    <TemperatureInput scale="c" />
                    <TemperatureInput scale="f" />
                </div>
            );
        };
    };
```
我们现在有了两个表单，但是当你在其中一个input里面输入温度的时候，另外一个不会更新，这和我们的需求是矛盾的：我们需要它们彼此之间保持同步！

我们也不能在Calculator里面显示BoilingVerdict，组件Calculator不知道当前的温度值，因为温度值在TemperatureInput里面被隐藏了！

## Writing Conversion Functions （使用(中间层)转换函数）

首先，我们需要写两个函数，来实现Celsius和Fahrenheit之间的互换运算：

```jsx
    function toCelsius(fahrenheit){
        return (fahrenheit - 32) * 5 / 9;
    };
    function toFahrenheit(celsius){
        return (celsius * 9 / 5) + 32;
    };
```

上面的两个函数实现两个数字之间的转换，我们将继续编写一个函数，它有两个参数，一个是字符串，一个是另外一个函数，用来实现个根据一个值然后调用所传函数算出另外一个值的这样一个需求。

如果是一个非法的温度值，那么它将会返回一个空字符串，同时它保证输出精确到小数点后面三位。

```jsx
    function tryConvert(temperature,convert){
        const input = parseFloat(temperature);
        if(Number.isNaN(input)){
            return '';
        };
        const output = convert(input);
        const rounded = Math.round(output * 1000) / 1000;
        return rounded.toString();
    };
```
例如，tryConvert('abc',toCelsius)将会返回一个空字符串，同时tryConvert('10.22',toFahrenheit)将会返回'50.396'字符串。

## Lifting State Up （）

现在，两个TemperatureInput组件都能保持独立，而且能分别把自己的值保存在本地的state里面。

```jsx
    class TemperatureInput extends React.Component{
        constructor(props){
            super(props);
            this.handleChange = this.handleChange.bind(this);
            this.state = {temperature:''};
        };
        handleChange(e){
            this.setState({temperature:e.target.value});
        };
        render(){
            const temperature = this.state.temperature;
            //后面代码省略 ...
    };
```


然而，我们的需求是这样的：我们希望两个温度值是同步的，也就是说，如果当前，我们改变了其中一个值，那么另外一个也会保持更新，反之亦然。

在React中，如果我们需要在几个组件之间共享state，那么我们可以把这些state存储在它们共同祖先组件上，技术术语把这个叫做“状态提升”！我们将在TemperatureInput组件中移除本地状态，并把数据保存在Calculator中。

当Calculator组件拥有了共享的state之后，那么在两个input表单中就符合了“单一数据源”！它就能指导两个input表单如何获取对应的值了！所以，此时两个TemperatureInput组件都包含在共同的父组件Calculator中，这两个input表单数据就可以保持同步了！

让我们来看一下它们是如何一步一步工作的！

首先，在TemperatureInput中我们将会用this.props.temperature来代替this.state.temperature。现在，我们假定this.props.temperature已经存在了，它将来会从Calculator中传递过来：

```jsx
    render(){
        //之前的代码是： const temperature = this.state.temperature;
        const temperature = this.props.temperature;
    //后续代码省略 ...
```

我们都知道组件的[props属性是只读了](https://facebook.github.io/react/docs/components-and-props.html#props-are-read-only)，当温度值保存在组件的本地state中的时候，那么组件TemperatureInput可以通过函数this.setState()来改变它，但是我们如果把温度值保存在this.props属性里面，那么我们的组件就无法控制它了。

在React中，摆脱这个问题的办法就是建立一个受控制的组件，就像DOM &lt;input&gt;接受value和onChange来作为它的props属性一样。我们也希望组件TemperatureInput从它的父组件Calculator那里接受两个值来作为其temperature和onTemperatureChange属性。

现在，如果组件TemperatureInput需要更新它的温度值，那么它需要调用this.props.onTemperatureChange:

```jsx
    handleChange(e){
        //之前我们的代码是这样的： this.setState({temperature:e.target.value})
        this.props.onTemperatureChange(e.target.value);
    //后续代码省略 ...
```

注意，我们并没有特别指定temperature和onTemperatureChange的含义，我们可以在随时调用它们，就像value和onChange属性一样，它们都是共同的转换函数。

onTemperatureChange和temperature属性将在它们的祖先组件Calaulator那里提供。onTemperatureChange将会在改变了本地状态之后调用。因此，当我们重新渲染两个表单的值之后，我们将会很快看到一个新的Calculator。

在组件Calculator改变之前，我们先来简短概述一下TemperatureInput组件。我们已经移除了它的本地状态，并且取消了this.state.temperature,取而代之是this.props.temperature，而且取消了this.setState()的调用，取而代之的是this.props.onTemperatureChange()。代码如下：

```jsx
    class TemperatureInput extends React.Component{
        constructor(props){
            super(props);
            this.handleChange = this.handleChange.bind(this)；
        };
        handleChange(e){
            this.props.onTemperatureChange(e.target.value);
        };
        render(){
            const temperature = this.props.temperature;
            const scale = this.props.scale;
            return(
                <fieldset>
                    <legend>Enter temperature in {scaleNames[scale]}:</legend>
                    <input value={temperature} onChange={this.handleChange}/>
                </fieldset>
            );
        };
    };
```

好，现在让我们把目光转到Calculator组件上。

我们将保存当前input的temperature和scale值到它本地state上，这个state就是我们将要“提升”的state，同时在接下来的使用过程中，这个state将服务于这两个input。这是最小限度的我们在组件之间的数据共享！

例如，如果我们在Celsius中输入37，那么组件Calculator的state将会变为：

```jsx
    {
        temperature:'37',
        scale:'c'
    }
```
如果我们稍后修改了Fahrenheit表单值为212，那么组件Calculator的state将会变为：

```jsx
    {
        temperature:'212',
        scale:'f'
    }
```
在这之前，我们需要同时保存两个表单的值，但现在不用如此了，只需要保存当前被改变的input值就好了，同时保存scale值。我们可以根据当前input的值，算出另外一个input的相关值！

这些input的值之所以是同步更新的，是因为它们都是来自同一个state值。

```jsx
    class Calculator extends React.Component{
        constructor(props){
            super(props);
            this.handleCelsiusChange = this.handleCelsiusChange.bind(this);
            this.handleFahrenheitChange = this.handleFahrenheitChange.bind(this);
            this.state = {temperature : '',scale:'c'};
        };
        handleCelsiusChange(temperature){
            this.setState({scale:'c',temperature});
        };
        handleFahrenheitChange(temperature){
            this.setState({
                scale:'f',temperature
            });
        };
        render(){
            const scale = this.state.scale;
            const temperature = this.state.temperature;
            const celsius = scale === 'f'?tryConvert(temperature,toCelsius) : temperature;
            const fahrenheit = scale === 'c'?tryConvert(temperature,toFahrenheit) : temperature;

            return (
                <div>
                    <TemperatureInput scale="c" temperature={celsius} onTemperatureChange={this.handleCelsiusChange} />
                    <TemperatureInput scale="f" temperature={fahrenheit} onTemperatureChange={this.handleFahrenheitChange} />
                    <BoilingVerdict celsius={parseFloat(celsius)}/>
                </div>
            );
        };
    };
```

现在，无论你编辑了哪一个input值，组件Calculator的this.state.temperature和this.state.scale状态都会更新，两个input都会建立了依赖同步！

让我们简要概述一下这其中放生了什么：

* 1、React调用了input里面的onChange属性的监听函数，在我们的设计中，这是在组件TemperatureInput上注册的handleChange函数；

* 2、handleChange函数通过根据需要调用了this.props.onTemperatureChange函数，该函数通过父组件Calculator提供；

* 3、当用户界面更新之后，组件Calculator就会指定Celsius TemperatureInput的onTemperatureChange值为Calculator的handleCelsiusChange方法，同时，Fahrenheit TemperatureInput的onTemperatureChange值为Calculator的handleFahrenheitChange方法。所以不管调用其中的那个方法都只是依赖于对应的input表单被改过了；

* 4、在这些方法里面，组件Calculator请求React通过调用this.setState()重新更新自己，就用当前的改变的input值和scale值；

* 5、React调用组件Calculator的render方法来更新用户界面；

* 6、其中TemperatureInput组件会根据Calculator的新props值，来根据自己用户界面；

* 7、最后更新最终我们希望看到的用户界面。

每次更新用户界面，都会重复上面的步骤！

## Lessons Learned

在react应用中，你应该保持单一的数据流。通常情况下，state值应该首先根据组件是否需要用它来更新用户界面来使用它！如果同时几个组件会用到相同的state值，那么这个时候你就应该想象一下，把这个state提升到它们的祖先组件中，而不是同步地分别更新多个相同的state值！你可以到[ top-down data flow](https://facebook.github.io/react/docs/state-and-lifecycle.html#the-data-flows-down)了解更多相关信息!

提升state操作貌似增加了代码量，但是权衡一下，它减少了查找bug和隔离bug的工作量。因为state被保持在了一个可以独立修改的组件里面，这样我们就很好的控制可能存在的bug了。

如果有一个数据我们可以从props或者state里面，那么这个数据很大可能不应该放在state里面。例如，为了存储celsiusValue和fahrenheitValue值，我们只需要保存最新的temperature和scale值就好了，其它的数据也可以通过这些值在render方法里面实时的算出来！

有时候，当我们看到用户界面出现错误的时候，我们可以利用[React Developer Tools](https://github.com/facebook/react-devtools)来逐步检查props属性然后排除bug所在，这会让你在源代码中去找bug，比较直观！

