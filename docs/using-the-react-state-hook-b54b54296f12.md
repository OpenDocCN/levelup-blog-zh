# 使用反应状态挂钩

> 原文：<https://levelup.gitconnected.com/using-the-react-state-hook-b54b54296f12>

![](img/865c838408cfc8a46b6429476bb48194.png)

斯科特·沃尔什在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 是一个用于创建前端视图的库。它有一个庞大的图书馆生态系统与之合作。此外，我们可以用它来增强现有的应用程序。

在本文中，我们将了解如何使用状态挂钩在函数组件中保持动态状态。

# 使用状态挂钩

我们可以使用`React.useState`方法创建一个状态，并用相应的函数来改变状态。

例如，我们可以编写以下代码来为我们的应用程序添加一个挂钩:

```
import React from "react";
import ReactDOM from "react-dom";function App() {
  const [count, setCount] = React.useState(0); return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count {count}</button>
    </div>
  );
}
```

在上面的代码中，我们有一个默认为 0 的`React.useState`钩子。它返回一个包含状态的数组，我们称之为`count`，以及一个设置状态的函数，我们称之为`setCount`。

然后在按钮的`onClick`处理程序中，我们可以通过`setCount`函数来更新计数。

# 等价类示例

上面的示例与下面的类相同:

```
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  } render() {
    return (
      <div>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Count: {this.state.count}
        </button>
      </div>
    );
  }
}
```

在上面的代码中，我们在构造函数中用`count` 0 初始化了`this.state`。然后在`button`中，我们传入一个 click 事件处理程序，它调用`setState`来更新`count`状态，使其增加 1。

因此，`this.setState({ count: this.state.count + 1 })`与`setCount(count + 1)`相同，`count`与`this.state.count`相同。

# 挂钩和功能组件

功能组件看起来像:

```
const App = props => <p>foo</p>;
```

或者:

```
function App(props) {
  return <p>foo</p>;
}
```

它们以前被称为无状态组件。但是，现在 React 有了钩子，就可以保持自己的状态了。因此，它们应该被称为功能组件。

# 什么是钩子？

钩子是一个特殊的函数，它可以让我们钩住 React 特性。`useState`在上面的例子中是一个钩子，它让我们将反应状态添加到函数组件中。

![](img/037755169628909963f2630b26a53747.png)

汤米·利斯宾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 声明状态变量

`useState`声明一个状态变量。在上面的例子中，我们有一个名为`count`的变量，但是我们也可以用其他的名字来称呼它。

`useState`和`this.state`有着相同的能力，我们在一个班里就有。

当函数存在时，变量消失，但是状态变量被 React 保存。

`useState`的唯一论据是初始状态。与类不同，状态不必是对象。

我们可以用任何类型的数据作为初始状态。

如果我们想在 state 中存储多个值，那么我们将为每种数据调用`useState`。

`useState`总是给我们当前状态。这不仅仅是为了创造初始状态。

# 阅读状态

在我们的例子中:

```
import React from "react";
import ReactDOM from "react-dom";function App() {
  const [count, setCount] = React.useState(0); return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count {count}</button>
    </div>
  );
}
```

我们从`useState`返回的`count`中获取当前状态。

# 更新状态

我们不像在类组件中那样调用`this.setState`，而是直接调用`setCount`函数。

因此，我们不必担心`this`的值。

# 方括号是什么意思？

中的方括号:

```
const [count, setCount] = React.useState(0);
```

是析构赋值操作，这意味着数组中的值根据位置被赋给变量。

第一个元素赋给第一个变量，第二个元素赋给第二个变量，依此类推。

```
const [count, setCount] = React.useState(0);
```

与以下内容相同:

```
const countState = React.useState(0);
const count = countState[0];
const setCount = countState[1];
```

# 使用多个状态变量

当我们需要使用多个状态变量时，我们会多次调用`useState`。

例如，我们可以写:

```
function App() {
  const [count, setCount] = React.useState(0);
  const [foo] = React.useState({ text: "bar" });
  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Count {count}</button>
      <p>{foo.text}</p>
    </div>
  );
```

在上面的代码中，我们调用了两次`useState`来创建两个状态`count`和`foo`。

状态可以有对象值，所以我们不用创建太多的状态。

# 结论

我们可以使用`useState`钩子来获取和设置功能组件中的动态状态。

它返回当前状态和一个函数来更新数组中的状态。

然后我们可以直接使用它们来显示数据，并调用返回的函数来更新状态。