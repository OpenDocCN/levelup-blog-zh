# 如何使用 React 上下文

> 原文：<https://levelup.gitconnected.com/how-to-use-react-context-bb4d0b8a63ee>

![](img/83424b1b1c7d9ff14fce54d72193a2a3.png)

React Context 是 React v16.3.0 中引入的强大特性[。它允许您从组件的呈现方法中的当前上下文(例如，父级)中访问值，通常您只能访问其本地状态和属性。这为组件之间共享值创建了一个非常灵活的系统。在本文中，我们将介绍如何使用 React 上下文创建共享状态，以及如何在子组件中使用该状态。](https://reactjs.org/blog/2018/03/29/react-v-16-3.html)

本文假设您熟悉 React 的基础知识，比如 JSX、组件、状态和道具。如果你还不熟悉这些概念，我建议你先阅读[官方 React 文档](https://reactjs.org/docs/getting-started.html)。

# 什么是反应上下文？

React Context 是一个新特性，它允许您在组件之间共享数据。它在概念上类似于 React 的`useState()`钩子，但是它允许你一次跨多个组件共享数据。使用上下文 API 最常见的用例是在一个或多个子组件之间共享状态。让我们看一个例子:

```
import React from 'react'const ParentComponent = () => {
    const [counter, setCounter] = React.useState(0) return (
        <ChildComponent
            counter={counter}
            onIncrement={() => setCounter(counter + 1)}
        />
    )
}const ChildComponent = ({ counter, onIncrement }) => {
    return (
        <div>
            {counter}
            <button onClick={onIncrement}>increment</button>
        </div>
    )
}
```

这里，我们有一个`<ParentComponent>`，它用初始值`0`设置自己的本地状态。该组件正在呈现一个`<ChildComponent />`，它正在接收当前的`counter`状态作为道具。它还会在单击 increment 按钮时更新父级的计数器状态。

这对于简单的情况非常有效，但是如果我们想要在组件之间共享更多的状态，这可能会很快失控。组件开始变得越来越耦合，这使得它们更难维护和推理。我们可以做得更好！

我们可以使用 React 上下文 API 在两个组件之间共享计数器状态，而不是将`counter`属性传递给`ChildComponent`。方法如下:

```
import React from 'react'const CounterContext = React.createContext(0)const ParentComponent = () => {
    const [counter, setCounter] = React.useState(0) const increment = () => setCounter(counter + 1) return (
        <CounterContext.Provider value={{ counter, increment }}>
            <ChildComponent />
        </CounterContext.Provider>
    )
}const ChildComponent = () => {
    const { counter, increment } = React.useContext(CounterContext) return (
        <div>
            {counter}
            <button onClick={() => increment()}>increment</button>
        </div>
    )
}
```

`ChildComponent`现在从上下文中消耗计数器状态和增量动作，而不是接收它们作为道具。这允许我们避免将`ChildComponent`耦合到`ParentComponent`。

# 这和`useState()`有什么区别？

上下文 API 和`useState()`最大的区别是上下文 API 允许你一次在多个组件之间共享状态。`useState()`挂钩的范围仅限于单个组件。

另一个区别是，上下文 API 可以用来共享任何类型的数据，而不仅仅是状态。比如像函数。我们甚至可以使用上下文来共享整个 Redux 存储(但请不要这样做)。

# 上下文 API 是如何工作的？

`Context`对象是一种特殊类型的 React 对象，可用于创建和共享数据。它有两个组成部分:`Provider`和`Consumer`。提供者用于创建新的上下文对象，而使用者用于访问这些上下文中的值。

上下文 API 允许您为多个消费者提供一个提供者。例如:

```
import React from 'react'const CounterContext = React.createContext(0)const ParentComponent = () => {
    const [counter, setCounter] = React.useState(0) const increment = () => setCounter(counter + 1) return (
        <CounterContext.Provider value={{ counter, increment }}>
            <DisplayCounterComponent />
            <IncrementCounterComponent />
        </CounterContext.Provider>
    )
}const DisplayCounterComponent = () => {
    const { increment } = React.useContext(CounterContext) return <div>{counter}</div>
}const IncrementCounterComponent = () => {
    const { increment } = React.useContext(CounterContext) return (
        <div>
            <button onClick={() => increment()}>increment</button>
        </div>
    )
}
```

这里，我们使用`CounterContext`提供者在多个组件之间共享上下文。`DisplayCounterComponent`正在检索`counter`状态并显示它的值，而`IncrementCounterComponent`正在从上下文中检索`increment`动作。

# 创建自定义上下文提供程序

在前面的例子中，我们直接使用了`CounterContext.Provider`。但是我们可以创建自己的`CounterProvider`组件，这将使重用上下文逻辑变得更加容易。

```
import React from 'react'const CounterContext = React.createContext(0)const CounterProvider = ({ children, initialCount = 0 }) => {
    const [counter, setCounter] = React.useState(initialCount) const increment = () => setCounter(counter + 1) return (
        <CounterContext.Provider value={{ counter, increment }}>
            {children}
        </CounterContext.Provider>
    )
}
```

`CounterProvider`组件从父组件接收一个初始计数，用来创建上下文的初始状态。它还接收了一个`children` prop，我们将通过它来呈现任何子组件。

现在让我们看看如何在我们的应用程序中使用`CounterProvider`。

```
import React from 'react'const ParentComponent = () => {
    return (
        <CounterProvider initialCount={0}>
            <ChildComponent />
        </CounterProvider>
    )
}const ChildComponent = () => {
    const { counter, increment } = React.useContext(CounterContext) return (
        <div>
            {counter}
            <button onClick={() => increment()}>increment</button>
        </div>
    )
}
```

`useContext`钩子仍然用于在任何子组件中检索我们的计数器上下文。但是我们可以为此创建一个定制的`useCounterContext`钩子，以获得更好的可重用性。

```
const useCounterContext = () => React.useContext(CounterContext)
```

现在我们可以在组件内部使用`useCounterContext`而不是`useContext`。

```
const ChildComponent = () => {
    const { counter, increment } = useCounterContext() return (
        <div>
            {counter}
            <button onClick={() => increment()}>increment</button>
        </div>
    )
}
```

# 结束语

上下文 API 是在多个组件之间创建共享状态的强大工具。这是避免将组件耦合在一起的好方法，这使得它们更容易测试和维护。

[React 文档](https://reactjs.org/docs/context.html)也有一篇关于上下文 API 的文章。如果有兴趣了解更多，值得一看。