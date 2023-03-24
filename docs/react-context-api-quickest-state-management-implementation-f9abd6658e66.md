# React 上下文 API:最快的状态管理实现

> 原文：<https://levelup.gitconnected.com/react-context-api-quickest-state-management-implementation-f9abd6658e66>

![](img/8ceb6ea7720cb8f772bc89d7902c79bf.png)

Redux 是在中小型应用程序中实现全局状态管理的有效方法。在本指南中，我们将会看到在你的应用中实现状态管理的最简洁、最快、最简单的方法！这些简单的步骤将花费您不到五分钟的时间，并且您将启动并运行您自己的全局状态管理系统。

我们将使用 React 上下文 API，它已经包含在 React 中，因此我们不必为这个项目安装任何额外的包。

```
npx create-react-app contextpractice
```

我们首先创建一个包含初始逻辑的小文件`state.js`。代码将在幕后为您处理一切，所以现在不要担心理解它。

然后将这个文件导入到`App.js`中。

```
import { StateProvider } from "./state"
```

我们的示例应用程序将是一个简单的计数器，可以使用我们的全局状态来增加和减少。

在 App.js 中，我们将执行三个简单的步骤:

1)创建初始状态:

```
const initialState = {
   counter: 0
}
```

2)创建一个具有增减功能的减速器:

```
const reducer = (state, action) => {
   switch (action.type) {
      case "increaseCounter":
         return {
            ...state,
            counter: state.counter + 1
         };
      case "decreaseCounter":
         return {
            ...state,
            counter: state.counter - 1
         };
      default:
         return state;
      }
};
```

3)用我们刚刚导入的`StateProvider`包装我们的应用程序。

```
return (
   <StateProvider initialState={initialState} reducer={reducer}>
      <Counter />
   <StateProvider>
   )
}
```

您的`App.js`文件应该如下所示:

这就是我们的全球状态管理！我们现在可以在应用程序中包含的任何组件中使用我们的状态。让我们看看如何访问和更新类组件中的状态。

首先创建一个`Counter.js`文件。

从任何组件访问我们的状态只需三个简单的步骤:

1.  导入状态上下文:`import { StateContext } from ".state"`
2.  渲染前功能:`static contextType = StateContext`
3.  渲染函数之后，返回之前:`const [{ counter }, dispatch] = this.context`

我们现在可以访问`counter`状态变量，并且我们可以使用`dispatch`来更新状态:

不到 5 分钟，我们就在应用程序中实现了全局状态管理！🎉

让我们练习添加另一块到我们的状态。如果我们想给我们的`increaseCounter`函数传递一个参数呢？比如要递增的量？让我们在 App.js 中创建另一个计数器`counter2`，以及一个新的`increase` / `decrease`计数器函数:

注意，在`increaseCounter2`中，我们说:

```
counter: state.counter + action.increment
```

我们将把一个名为`increment`的参数传递给`Counter.js`中的`dispatch`函数，该函数将保存我们希望计数器增加的值。让我们看看它是如何工作的:

我们的调度函数现在看起来像这样:

```
dispatch({type: "increaseCounter2", increment: 10})
```

增量参数然后作为`action.increment`传递给我们的缩减器。

感谢阅读！如果你喜欢这篇文章，留下一点掌声，如果这个过程的任何部分让你感到困惑，留下评论🎉

Github 库:[https://github.com/deeayeen/mediumcontextpractice](https://github.com/deeayeen/medium-context)