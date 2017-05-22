## Thinking in React （写react App的思路）

在我们的印象中，react是一种构建较大、较快App的方法。它已经在facebook和Instagram中大量使用！

在react开发过程中，最重要的一件事情就是你是怎么构思你的app架构的。在这篇文档中，我们将带领你来构建一个searchable表格，用于实现过滤显示数据的功能，从中带你一步一步领悟怎么构思一个react组件。

## Start With A Mock (开始一个需求)

我们假设已经存在一个JSON API和一个大概描述需求的设计图，设计图如下：

![](https://facebook.github.io/react/img/blog/thinking-in-react-mock.png)

JSON API返回的接口数据格式如下：

```js
    [
        {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
        {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
        {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
        {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
        {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
        {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
    ];
```

