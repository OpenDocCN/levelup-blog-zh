# 使用挂钩执行异步操作

> 原文：<https://levelup.gitconnected.com/performing-async-actions-using-hooks-e4da47293d8e>

![](img/36e5c6132699bee8c3fc27440a353630.png)

由[布鲁克·安德森](https://unsplash.com/photos/gTQbZXL417Q?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/rock-climbing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

最近，我写了关于使用[钩子和渲染道具](https://medium.com/front-end-weekly/data-fetcher-component-using-hooks-and-render-props-aacf3162dfc2)获取数据的文章。这篇文章的目的是获取和显示数据。在这篇文章中，我想重温一下那篇文章，并用例子来扩展它，展示如何使用相同的概念来执行其他类型的操作(例如发布数据)。

## 我们如何抽象出数据动作？

无论您是获取、更新、添加、删除还是执行自定义操作，所有这些操作都有一个共同点。它们都要经历三个阶段——加载、成功/数据和错误。这三个阶段是 UI 开发的组成部分。我们做的任何操作(甚至不需要是 API 动作)，都会有这三种状态。

现在，让我们考虑一下界面。我们的接口接受一个异步动作(在我们的例子中，它是一个函数),并返回三个状态以及执行该动作的方法。

我想提一下，仅仅用一个钩子或者一个组件是不够的。操作必须遵循提供的接口；这样，我们的逻辑才能正常工作。当抽象任何一种逻辑时，这是需要记住的重要事情之一。我们应该建立抽象概念的所有部分都同意的接口/契约。

## 使用钩子构建异步操作功能

钩子提供了一种将定制的、可重用的**功能**集成到我们的组件中的方法。我们曾经为视图逻辑重用组件，现在我们也可以使用钩子重用业务逻辑。在编写实现之前，让我们展示一下我们将如何使用它:

```
import React from 'react';
import { useAction } from './hooks';
import { saveProfile } from './api';function App() {
  // The second element "perform" is just a variable using array destructor
  **const [state, perform] = useAction(saveProfile);** // We send the data as an argument into the perform function,
  // which in our case === "updateProfile"
  **async function handleSuccess(e) {
    e.preventDefault();
    await perform({ age: "12456" });
  }** **async function handleError(e) {
    e.preventDefault();
    await perform({ age: "test" });
  }** // Helper function to action result coming from the hook
  // The tested values in this function are the
  // values returned from hook function
  **function renderStatus() {
    if (state.loading) return "Loading...";** **if (state.error) return `Error: ${state.error.message}`;** **return `Data: ${JSON.stringify(state.data)}`;
  }** return (
    <div className="App">
      <button onClick={**handleSuccess**}>Success!</button>
      <button onClick={**handleError**}>Error!</button>
      <div className="status">{**renderStatus()**}</div>
    </div>
  );
}
```

现在开始实施:

```
// hooks.jsimport { useState } from 'react';export const useAction = (action) => {
  const [loading, setLoading] = useState(false);
  const [data, setData] = useState(null);
  const [error, setError] = useState(null); // The incoming "action" argument to the hook is NOT performed.
  // It is only stored in the function scope; so that, we can use it when
  // performing the action using the following function
  // This function is returned as the second element in the returned array
  **const performAction = async (body = null) => {
    try {
      setLoading(true);
      setData(null);
      setError(null);
      const data = await action(body);
      setData(data);
    } catch (e) {
      setError(e);
    } finally {
      setLoading(false);
    }
  }** return [{ loading, data, error }, performAction];
}
```

这个简单的钩子给了我们要执行的当前状态动作和执行动作的函数。body 参数在`performAction`函数中，因为非 GET 请求通常有一个请求体。在上面的例子中，如果我们调用`perform`函数，加载、数据和错误状态将被“钩子”设置。然后，我们可以轻松地使用这些状态来执行我们的视图逻辑(显示加载栏、显示错误或显示操作成功)。

如您所见，我们使用`try/catch/finally`块来决定我们是收到错误还是成功。这些块中使用的 try catch 不是我们从编程中了解的“真正的”try catch。它们是伪装在异步块中的承诺解决/拒绝方法。我前面提到过，动作必须在钩子提供的接口上达成一致，以统一和简化开发人员的体验。在我们的例子中，接口是必须返回成功数据，必须抛出错误。下面是一个 API 调用代码示例:

```
// api.jsexport const updateProfile = async (body) => {
  const response = await fetch(SOME_URL_HERE, {
    method: 'PUT',
    body,
    headers: {
      'Content-Type': 'application/json'
    }
  } const data = await response.json();
  if (!response.ok) {
    throw new Error(data);
  } return data;
}
```

通常，fetch 会抛出与 API 无关的错误(网络中断、JSON 验证错误等)。然而，我们也为我们的 API 逻辑抛出了错误(错误的请求，REST 404 等)。这允许我们以类似的方式显示来自 API 的所有错误的处理错误(将其存储在状态中),并使我们更容易考虑我们的操作逻辑。如果你想区分获取错误和 API 错误，你可以创建一个单独的错误类(例如`ApiError`)并抛出它，而不是一般的 JS `Error`。

就是这样。现在，多亏了钩子，你可以使用一个简单而强大的接口来执行异步数据相关的操作！

# 演示

我添加了一个简单的演示来展示它是如何工作的。演示和代码样本之间唯一的区别是，我没有做真正的`fetch`请求，而是模仿了一个 API 请求。

CodeSandbox 演示

# 结论

这篇文章的重点是概括数据获取，以允许执行任何类型的异步操作。定制挂钩使得构建可附加到任何组件的定制和可重用功能变得非常容易。无冲突行为为我们提供了一种安全的方法来将业务逻辑从视图逻辑中分离出来，而不需要创建组件。

*编辑:改写了帖子中的代码示例；所以，很清楚是怎么回事。添加了 CodeSandbox 演示。修正了一些语法问题。澄清了一些文字。*

编辑 2:修正了钩子逻辑，错误不会在每个执行函数中被清除。

[![](img/b62413b7c7ec47e9b1212d1f624904d7.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/react) [## 学习 React -最佳 React 教程(2019) | gitconnected

### 排名前 49 的 React 教程-免费学习 React。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/react)