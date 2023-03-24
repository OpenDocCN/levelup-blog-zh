# 用 Redux 和 TypeScript 简化连接的道具

> 原文：<https://levelup.gitconnected.com/simplifying-connected-props-with-redux-and-typescript-62163cc5a25c>

![](img/5cb84e85b6f9231b5988f739fb67b3fa.png)

当使用 Redux 连接的组件时，可以有多达三个道具源:

*   从父组件传递的道具，
*   从`mapStateToProps`返回的道具，
*   从`mapDispatchToProps`返回的道具。

当与 TypeScript 一起使用时，所有这些属性都需要有类型。如果它是有状态的基于类的组件，状态也需要被类型化。这是大量的手动类型声明，将来也需要维护。幸运的是，从`@types/react-redux`包的 7.1.2 版本开始，在大多数情况下可以自动推断连接道具的类型。在 [React Redux 文档](https://react-redux.js.org/using-react-redux/usage-with-tsx#inferring-the-connected-props-automatically)中记录了这样做的方法，在这篇文章中，我们将看到一个具体例子的应用。

我们将重构一个示例`App`组件，为了简洁起见，简化了它的实现(但不是类型)细节。组件本身在挂载时(通过 Redux 动作)获取一个项目列表，然后呈现从 props 接收的列表。此外，该组件使用 React router，从那里接收 URL 参数作为道具。

注意，我们使用`typeof`来推断动作的类型，而`mapStateToProps`中的类型基本上是`AppState`和`OwnProps`类型的组合。看起来我们正在为其他地方已经可用的类型做大量的手动类型声明，那么为什么不使用类型信息并自动推断组件属性呢？

这里的另一个问题是被调度的动作返回一个`ThunkAction`类型的函数，该函数又返回`void`(即 nothing)。当将组件连接到 Redux 并以严格模式运行 TypeScript 时，我们会得到以下错误:

最后一部分，`Type 'void' is not assignable to type 'ThunkAction<void, AppState, undefined, { payload: any; type: string; }>'.`这里最重要。即使`loadData`的类型是`() => ThunkAction => void`，由于 React-Redux 解析 thunks 的方式，实际推断的类型将是`() => void.`

这就是`ConnectedProps`助手类型变得有用的地方。它允许从`mapStateToProps`和`mapDispatchToProps`推断连接的类型，并且它将正确地解析 thunks 的类型。首先，让我们把`mapStateToProps`和`mapDispatchToProps`移到文件的顶部，并把它们从所有的泛型类型声明中去掉，因为它们不再是必需的。

接下来我们需要通过组合 Redux 中的道具来创建一个`connector`函数。我们在声明组件之前这样做，因为我们将在创建`Props`类型时使用这个函数。

现在是时候使用`ConnectedProps` helper 提取连接道具的类型了。在此之前，我们还需要移除我们的`ConnectedProps`和`DispatchProps`接口。

最后，我们将这些道具与自己的道具结合起来，为组件创建`Props`类型。

最终的结果会是这样的。

我们已经通过去除从 Redux 接收的道具的手动声明简化了我们的组件。它们现在是从我们在状态和动作中为它们设置的类型中自动推断出来的。这大大提高了应用程序的可维护性，也修复了错误推断 Redux thunk 操作返回类型的问题。

*原载于*[*https://claritydev.net*](https://claritydev.net/blog/simplifying-connected-props-with-redux-and-typescr/)*。*