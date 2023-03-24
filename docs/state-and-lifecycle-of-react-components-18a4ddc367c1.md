# React 组件的状态和生命周期

> 原文：<https://levelup.gitconnected.com/state-and-lifecycle-of-react-components-18a4ddc367c1>

![](img/3890b99b82a30790f06e49fa15903196.png)

照片由[本怀特](https://unsplash.com/@benwhitephotography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 是一个用于创建前端视图的库。它有一个庞大的图书馆生态系统与之合作。此外，我们可以用它来增强现有的应用程序。

在本文中，我们将研究 React 组件的生命周期以及如何改变它们的内部状态。

# 改变内部状态

道具是不可变的，因为我们希望保持 React 组件相对于道具的纯洁性。这意味着当我们传入相同的道具时，我们总是得到相同的输出，假设其他什么都没有改变。

这在大多数情况下不是很有用，因为我们想改变组件内部的东西。

为此，我们可以操纵组件的内部状态。

所有基于类的 React 组件都有可通过`this.state`访问的内部状态，我们可以通过调用`this.setState`来改变它。

例如，如果我们想创建一个显示当前时间并每秒更新一次的`Clock`组件，我们可以这样做:

```
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date(), timer: undefined };
  } componentWillMount() {
    this.updateDate();
  } componentWillUnmount() {
    clearInterval(this.state.timer);
  } updateDate() {
    const timer = setInterval(() => {
      this.setState({ date: new Date() });
    }, 1000);
    this.setState({ timer });
  } render() {
    return (
      <div>
        <p>It is {this.state.date.toLocaleTimeString()}.</p>
      </div>
    );
  }
}
```

在上面的代码中，我们声明了一个扩展了`React.Component`的类`Clock`。这意味着代码是一个 React 组件，它可以有自己的生命周期方法和`render`方法。

在`Clock`组件内部，我们声明了`constructor`中的`state`字段，它具有初始状态`date`和一个`timer`状态来保存由`setInterval`返回的定时器对象。

另外，我们调用`super(props)`是因为`Clock`扩展了`React.Component`，而`React.Constructor`将`props`作为参数。

然后，当`Clock`组件被挂载到 DOM 中时，我们有自己的`componentDidMount`生命周期钩子来加载初始化代码。

在`componentDidMount`中调用`updateDate`方法，每秒不断更新`this.state.date`。

为了更新`setInterval`回调中的日期，我们用一个对象调用了`this.setState`。

对象将状态名作为键，正如我们在第一行中定义的那样，值是我们想要设置状态的新值。

我们还调用了`setState`来设置`timer`字段，这样我们就可以在`componentWillUnmount`中用它来调用`clearInterval`。当组件从 DOM 中移除时，调用`componentWillUnmount`钩子。

这是一个运行任何用于清理的代码的好地方。

最后，在`render`方法中，我们呈现日期。每当`this.state`通过调用`this.setState`而改变时，这个方法就会被调用，所以如果我们把`this.state`属性放在那里，它的最新值将总是被显示。

这意味着`this.state.date`将是以下日期之后的最晚日期:

```
this.setState({ date: new Date() });
```

叫做。

`componentDidMount`和`componentWillUnmount`是生命周期方法。它们只在 React 组件生命周期的特定部分被调用。

`this.setState`是异步的，所以我们不应该假设在`this.setstate`调用之后列出的代码会在`this.setState`之后立即运行。

为了在 DOM 中挂载它，我们调用带有传入的`Clock`的`ReactDOM.render`方法，如下所示:

```
import React from "react";
import ReactDOM from "react-dom";class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date(), timer: undefined };
  } componentWillMount() {
    this.updateDate();
  } componentWillUnmount() {
    clearInterval(this.state.timer);
  } updateDate() {
    const timer = setInterval(() => {
      this.setState({ date: new Date() });
    }, 1000);
    this.setState({ timer });
  } render() {
    return (
      <div>
        <p>It is {this.state.date.toLocaleTimeString()}.</p>
      </div>
    );
  }
}const rootElement = document.getElementById("root");
ReactDOM.render(<Clock />, rootElement);
```

![](img/cbb411f69b8ca85b676c0696d835f2a8.png)

Max Ostrozhinskiy 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 正确使用状态

我们不应该直接修改状态，因为它不会触发组件的重新呈现。

所以我们不应该写:

```
this.state.foo = 'bar';
```

相反，我们应该始终使用如下`this.setState`:

```
this.setState({foo: 'bar'});
```

# 状态更新可以是异步的

状态更新是异步的，所以我们不应该依赖它一个接一个地运行。

同样，我们不能仅仅在`setState`中组合`props`和`state`的值。

例如，以下操作可能会失败:

```
this.setState({
  count: this.state.count + this.props.increment,
});
```

相反，我们写道:

```
this.setState((state, props) => ({
  count: state.count + props.increment
}));
```

从`props`获取最新值来更新代码。

常规函数和箭头函数都起作用，所以我们也可以写:

```
this.setState(function(state, props) {
  return {
    count: state.count + props.increment
  };
});
```

# 合并状态更新

当我们调用`setState`时，React 将我们传入`setState`的对象合并到当前状态。

例如，如果我们有两个状态，`firstName`和`lastName`，那么当我们调用`this.setstate({ lastName });`时，`this.state.firstName`保持不变，而`this.state.lastName`用最新的值`lastName`更新。

# 数据向下流动

状态总是在组件内部，它可以作为道具传递给子组件和元素。

React apps 有一个从父母到孩子的单向数据流。

# 结论

我们可以通过在组件内部存储状态来创建一个动态的 React 组件。

状态对象存储在 React 组件类的`state`字段中。

我们可以通过运行`this.setState`来设置状态。

React 组件具有触发 React 组件类中的方法的生命周期。这些被称为生命周期挂钩。

我们可以使用它们在 React 组件生命周期的特定阶段运行代码。