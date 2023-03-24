# 重用，减少。。。useReducer？

> 原文：<https://levelup.gitconnected.com/reuse-reduce-usereducer-5f11f2dd1e98>

![](img/84815e8ceca31902e3fca2aa0b66ac30.png)

React 是一个非常棒的工具，它让编写 JavaScript 应用程序变得更加容易和简洁。任何构建过普通 JavaScript 应用程序和 React 应用程序的人可能都会同意 React 应用程序具有明显的优势。幸运的是，在 React 16.8 中引入了钩子，这进一步增加了库的易用性和优雅性。

一些最常用的挂钩是 useEffect 和 useState，这两个挂钩的学习曲线相对较浅，因为它们非常简单，并且使用简单的语法。然而，在这篇博文中，我想讨论一个更复杂的 React 钩子… useReducer。我们将看看实现这个钩子所需的每个步骤，并讨论它什么时候最有用。

# 什么是 useReducer？

在我们进入代码之前，让我们先讨论一下*useReducer 实际上是什么。useReducer 挂钩与 useState 挂钩非常相似，因为它是一种用于管理应用程序状态的工具。本质上，它允许我们存储状态，并为我们如何在应用程序中操作该状态设置规则。*

与 useState 相比，useReducer 最显著的优点之一是它提供了更简洁的状态管理解决方案，尤其是对于更复杂的状态。此外，如 [React 文档](https://reactjs.org/docs/hooks-reference.html#usereducer)所述，在基于前一状态计算下一状态的情况下，useReducer 优于 useState。这是因为 useReducer 产生了更加一致和可预测的状态转换。

# useReducer 挂钩的作用。

作为一个基本的例子，我创建了这个应用程序，它利用 useReducer 来管理/操作计数状态。在继续阅读的过程中，您可以随意打开沙箱，摆弄代码。

这个应用程序有一个初始值 1(存储为一个状态)和几个可以操作该状态的按钮。让我们看看使用 useReducer 实现这个应用程序需要做些什么。

1.  当使用 useReducer 钩子时，我们需要做的第一件事是从 React 导入它。这将允许我们“挂钩”useReducer 功能:

```
import React, { **useReducer**, useState } from “react”;
```

2.接下来，我们将使用析构赋值语法为 useReducer 进行初始设置，类似于 useState。在我们的示例中，这个 reducer 函数将接受两个参数，即函数和初始值，并将返回一个数组(状态和调度函数):

```
const [state, dispatch] = useReducer(reducer, { count: 1 });
```

3.现在我们需要编写传递给 useReducer 的 reducer 函数。这个函数将接受一个状态(当前状态)和一个动作。该动作将被传递到上面提到的 dispatch 函数中，并将被设置为我们称之为 dispatch 的任何值(参见 5):

```
function reducer(state, action) {}
```

4.一旦我们有了 reducer 函数，我们就可以开始使用 switch 语句构建我们的动作。在这里，我们可以为我们想要执行的每个状态操作创建一个案例:

5.现在我们已经在 reducer 函数中描述了所有不同的情况，我们可以使用 dispatch 函数向 reducer 发送一个动作，该动作将触发与该动作类型相关的状态变化。这类似于 useState 挂钩中的 setter 函数:

这些函数可以传递给 JSX 中按钮上的事件监听器:

# 等等……有效载荷？就像在深红的天空中？

在上面的例子中，您可能已经注意到，其中一个分派函数传递了一个 payload 对象。这是你真正开始看到 useReducer 的魔力的地方。让我们再来看看我们的 switch 语句，特别是 set-number 的情况:

```
case “set-number”:return { count: (state.count = **action.payload.value**) };
```

该操作的目的是将计数器设置为我们在表单中输入的任何值。然而，为了让这个动作知道那个值是什么，我们需要以某种方式让它知道。我们可以通过向我们的调度函数传递一个有效负载来实现这一点。有效负载是一个对象，它应该包含执行我们调用的指定操作所需的所有值。

```
const handleSet = (e, num) => {e.preventDefault();dispatch({ type: “set-number”, **payload: { value: num }** });setNum();};
```

正如您在上面看到的，我们将 num 的值(这是一个保存表单提交时的值的状态)传递给我们的“set-number”操作，这样它就可以将计数器设置为这个数字。在 switch 语句中，我们可以通过点符号深入到对象中来获得该值。

# TL；速度三角形定位法(dead reckoning)

1.  useReducer 是一个帮助我们管理和操作状态的 React 钩子。
2.  useReducer 对于复杂状态或基于前一状态计算的状态最为有用。
3.  useReducer 是使用析构赋值语法设置的。它被传递一个 reducer 函数和初始状态，并返回状态和一个分派函数。
4.  reducer 函数接受两个参数，当前状态和一个动作。
5.  reducer 函数包含所有操作状态的动作。
6.  dispatch 函数向我们的 reducer 函数发送一个动作，该动作将根据我们预定义的规则触发状态的变化。
7.  有效负载是传递给调度函数的对象，包含执行操作所需的所有值。