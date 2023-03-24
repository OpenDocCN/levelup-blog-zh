# 使用 React 钩子进行全局状态管理

> 原文：<https://levelup.gitconnected.com/using-react-hooks-for-global-state-management-951834054971>

![](img/927c7b52dee6bf2426c23b17513ce277.png)

这篇文章是关于基于 react hooks 构建一个全局状态管理的库。您可以使用钩子来存储和更新 react 应用程序的全局状态，甚至可以将现有的应用程序从 Redux 重构为纯 React 和钩子。我之前的文章就是关于这种重构的，我鼓励你先读一读。此外，这个状态管理库的完整源代码和文档可以在 [Github](https://github.com/Light-Keeper/react-singleton-hook) 和 [npm](https://www.npmjs.com/package/react-singleton-hook) 上获得。

# 问题陈述

React 钩子提供了一种以有效、简洁和内聚的方式管理某些组件状态的方法。你几乎可以对组件状态做任何事情，在事件发生时更新它，并使用[定制钩子](https://reactjs.org/docs/hooks-custom.html)组织你的代码。

假设您想用一个自定义钩子获取当前用户的详细信息，下面是一段代码:

这个钩子工作得非常好，只有一个例外——每次新组件使用它时，它都会从 API 重新加载数据。钩子不共享数据——所有数据都被隔离到单个组件中。您可能希望使用上下文来确保数据被加载一次，但是上下文不知道什么时候真正需要数据，并且需要一些样板代码。

在这篇文章中，我们将开发一个库来把常规的定制钩子变成单例定制钩子。这种钩子一旦使用，将永远安装在一个附加的组件中(如上下文提供者)。此外，如果钩子没有被调用，它将永远不会被挂载，它允许你在你的项目中有许多单独的钩子，并且只有相关的钩子才起作用。从自定义钩子返回的数据将传播到所有使用钩子的组件，就像 react 上下文一样。下面是单例钩子创建的结果:

您可以在文档的[示例部分找到更多使用示例和场景。在本文中，我们将关注内部实现。](https://www.npmjs.com/package/react-singleton-hook#examples)

# 高阶钩子

我们需要接受的第一个想法是,`singletonHook`是一个接受 React 钩子并返回另一个 React 钩子的函数，因此它是一个高阶钩子。这个想法类似于[高阶组件](https://reactjs.org/docs/higher-order-components.html)，但是我们没有包装整个组件，只是包装了钩子。这种技术对于监控钩子的调用、对钩子的使用产生副作用或者完全替换钩子主体是很有用的。下面是一个简单的例子，说明在第一次使用钩子以及任何组件的钩子值发生变化时，如何使用 HOH 打印消息:

# singletonHook 很懒

我们现在已经准备好进行`singletonHook`实施。实现分为两部分。`singletonHook`依赖于`addHook`功能。`addHook`接受一个初始值，原钩子体和钩子状态改变时要调用的回调。安装和保持挂钩状态的所有魔法都发生在`addHook`内部。最关心的是懒惰——除非使用钩子，否则不应该调用它。为了跟踪这一点，我们使用了`mounted`变量，默认设置为`false`，在钩子执行期间，我们检查它是否为`false`。另外`singletonHook`保持最后已知的挂钩状态。它用`initValue`初始化，并在每次钩子更新时更新。这是新消费者所需要的:当一个新组件开始使用钩子时，它可能已经有了一个不同于初始状态的状态。有关更多实现细节，请参见下面的代码和注释:

T

这段代码有一个缺点，但这个缺点可能是可以修复的:消费者更新不是批处理的，并且没有排序保证——当较低级别的组件必须被较高级别的组件卸载时，可能会对该组件调用更新。在某些情况下，它可能会导致错误。例如，开发人员可能认为组件永远不会以某种状态呈现。使用`unstable_batchedUpdates`可以解决这个问题，具体如何使用请见[完整源代码](https://github.com/Light-Keeper/react-singleton-hook/blob/442236fc66ccd7cf8ba70144f26cfa28fb6ef3f2/src/singletonHook.js)。

另一个缺点是——用户必须显式地通过`initValue`。如果我们能从钩子中计算出 init 值，那就太好了，但是我没有找到一个好的方法在不安装钩子的情况下做到这一点。

# 添加挂钩—带挂钩的容器

分配一个组件，并在每次组件状态改变时调用一个传递的回调函数。在幕后，这个方法依赖于名为 *SingletonHooksContainer 的 React 组件。*该组件应在使用任何挂钩之前安装，如何安装则另当别论。但是考虑到 *SingletonHooksContainer* 的存在，我们可以在它的状态中添加一个钩子。然后，每个钩子被传递到一个单独的 *SingleItemContainer* 组件*。*钩子体在 *SingleItemContainer 的渲染阶段被调用。*如果钩子返回值改变，更新回调被调用。下面是一个最小的实现:

这个实现去掉了一些验证和安装代码，但是仍然可以工作，假设预先安装了*SingletonHooksContainer*。请查看[源代码](https://github.com/Light-Keeper/react-singleton-hook/blob/442236fc66ccd7cf8ba70144f26cfa28fb6ef3f2/src/components/SingletonHooksContainer.js)了解完整的实现细节，包括*SingletonHooksContainer*如何自动挂载。

# 下一步是什么？

如果能找到一种方法来避免将初始值传递给`singletonHook`就好了，比如预测钩子将在下一次渲染中被调用，并在钩子被使用之前进行渲染。但是现在——用户需要传递初始值。此外，我想知道社区可能会发现的 bug 和库可能会有的特性请求。可以自由使用图书馆。

[](https://www.npmjs.com/package/react-singleton-hook) [## 反应-单例-钩子

### 使用钩子管理 React 应用程序的全局状态。要在 React 应用程序中使用 React Singleton Hook，请将其安装为…

www.npmjs.com](https://www.npmjs.com/package/react-singleton-hook)