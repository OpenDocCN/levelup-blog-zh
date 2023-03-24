# React 上下文和 useContext 挂钩

> 原文：<https://levelup.gitconnected.com/react-context-and-the-usecontext-hook-dfa3f8a00a1b>

React 是一个用于构建用户界面的 JavaScript 库。如果你不熟悉 JavaScript，那么看看这篇文章——[JavaScript，简介](https://medium.com/@abongsjoel/javascript-a-brief-introduction-e995ae5a2494)。

![](img/be03f6831138333a7725992b914e8252.png)

本文假设您熟悉 JavaScript 框架 **React** 。

在本文中，我们将关注 React 上下文——它是什么以及如何使用它。

# 反应上下文

React Context 提供了一种通过组件树传递数据的方式，而不必在每一层手动向下传递属性。

Context 旨在共享可以被视为 React 组件树的“全局”数据，例如当前经过身份验证的用户、主题或首选语言。

**我们如何使用 React 上下文？**

使用 React 上下文有三个主要步骤，即:

1.  使用`createContext`方法创建一个上下文。

```
const MyContext = React.createContext();
```

2.将创建的上下文中的`Provider`方法包装在组件树周围，并将组件要使用的数据作为`value`传递给它。

```
export default function App() {
  return (
    <MyContext.Provider value="Some data to pass down">
      <MyCompenent />
    </MyContext.Provider>
  )
}
```

3.使用上下文使用者读取任何子组件中传递的值。

```
function ChildComponent() {
  return (
    <MyContext.Consumer>
      {value => <h1>{value}</h1>} 
    </MyContext.Consumer>
  )
}
```

注意:当我们调用`React.createContext`时，我们可以将一个初始值传递给我们的 value prop。创建的上下文是一个带有属性的对象:`Provider`和`Consumer`，它们是上面看到的组件。

为了使用我们传递下来的值，我们使用所谓的 render props 模式，这只是消费者组件作为道具提供给我们的一个函数。在那个函数的返回中，我们可以返回并使用`value`。

# useContext 挂钩

随着 React 钩子的出现，React 16.8 提供了另一种使用上下文的方式。

简单地说，钩子是 React 中的一个特性，它让我们不用写类就可以使用状态和其他 React 特性。

因此，我们现在可以使用 useContext 挂钩来使用上下文，如下所示:

```
function ChildComponent() {
  const value = React.useContext(MyContext) return <h1>{value}</h1>} 
}
```

我们可以清楚地看到 useContext 钩子如何通过使我们的组件更加简洁易读来简化我们的生活。

# 结论

如果我们不更新状态，而仅仅是将它传递到组件树中，我们就不需要像 Redux 这样的全局状态管理库。在这种情况下，我们可以简单地使用 React 上下文。

虽然可以将 React context 与一个类似 useReducer 的挂钩结合起来，创建一个临时的状态管理库，而不需要任何第三方库，但出于性能原因，通常不建议这样做。

—-

如果你喜欢这篇文章，请留下几个(或很多)掌声，如果你还没有，请确保在 Medium 上关注我。您也可以订阅，以便在我发布时收到电子邮件。谢了。