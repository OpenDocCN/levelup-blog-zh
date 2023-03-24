# 如何用 React 和 Redux 连接一个 HOC

> 原文：<https://levelup.gitconnected.com/how-to-connect-hoc-with-react-and-redux-2b3bce6a7dbf>

## 创建一个加载组件，使用 HOCs 从你的服务器获取数据，玩得开心！

![](img/05e62f583b341d75fdf973e50cc69076.png)

照片由 [kyler trautner](https://unsplash.com/@kylertrautner?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果您使用过 React，您可能会遇到高阶组件(HOC)。

> 高阶组件(HOC)是 React 中重用组件逻辑的一种高级技术。本质上，hoc 不是 React API 的一部分。它们是从 React 的组合性质中出现的一种模式。

HOC 只是一个简单的函数，它将一个组件作为参数，并返回另一个组件。主要目的是**在组件**之间共享相同的逻辑。你可以在这里了解更多。

现在假设您有一个 React Redux 应用程序，您需要将所有数据从服务器 API 加载到 Redux 存储中，然后才能呈现它们。是的，您需要对所有组件都这样做。简直是自杀！😱幸运的是 HOCs 帮助了我们:)

在这种情况下，您需要:

*   **从您的服务器 API 获取**所有数据
*   **分派**并填充 redux 存储
*   从你更新的 redux 状态中渲染 HTML
*   **渲染一个进度条**直到获取数据

开始吧！

# 装载部件 HOC

首先，让我们创建一个包含`Loading`组件的文件。如果状态正在加载，它将呈现一个进度条，否则它将呈现其子代。我们已经创建了这个组件，因为您可以在任何想要应用另一个获取数据逻辑的时候重用它😉

如你所见，太简单了！:)

现在我们可以创建特设！我们将其命名为 *withLoading.js* ，并放入一个名为“ *HOC* 的单独文件夹中。

多亏了 reduce 方法，我们可以将`fetchActions`创建为一个对象，将 actions name 作为键，将 functions 作为相关值。我们将把它传递给`connect` react-redux 函数，将动作与我们需要返回的组件`LoadingDataHOC`绑定在一起。我们对`propTypes`对象做同样的事情。

我们在`LoadingDataHOC`组件中使用了两个 React 钩子:`useState`和`useEffect`。

我们使用了`useState`钩子来管理加载状态，而`useEffect`钩子在组件挂载时获取所有数据(注意空数组是第二个参数)。

当所有的数据都被获取时，这就是将加载状态设置为 false 并显示所有组件的子组件的神奇时刻！

# 使用带负载

下面我们来看看如何使用这个组件。

这里，我们没有导出`EmployeeList`，而是使用`withLoading` HOC 来包装原始组件，并在组件挂载时自动获取所有雇员。

就是这样！我们会显示进度条，直到所有员工都被加载到商店中:)

我希望你在这里学到了一些东西，并从 React 中获得了乐趣！🤘