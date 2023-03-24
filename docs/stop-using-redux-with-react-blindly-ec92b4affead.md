# 停止使用 Redux With React！

> 原文：<https://levelup.gitconnected.com/stop-using-redux-with-react-blindly-ec92b4affead>

## 这就是为什么你可能根本不需要 Redux

![](img/3d8c66e3d5cebddab97bc3c664adef8e.png)

图片由[劳塔罗·安德烈亚尼](https://unsplash.com/@lautaroandreani)在 [Unsplash](https://unsplash.com/photos/xkBaqlcqeb4) 上拍摄

假设您将要创建一个新的单页面应用程序。您初始化一个新的节点项目，并开始安装您需要的所有依赖项——React 和一些用于捆绑和传输的工具。然后，您打开您最喜欢的编辑器，编写所有初始配置和样板代码，并准备好踏上应用程序成型之旅。

但是随后，您开始在命令行中键入`npm install redux react-redux`。

![](img/36d0103bdf664eb94b957d72fcbb3f4d.png)

[资料来源:Giphy](https://giphy.com/gifs/reaction-1zSz5MVw4zKg0)

让我打断你一下！是时候后退一步了。

在你在应用程序中安装 Redux 之前，问问你自己，“我真的需要 Redux 吗？”

我不知道它是什么，但我们很多人似乎认为没有 Redux 的 React 应用程序是不完整的，就好像这是天造地设的一对。我也为此感到内疚。

我见过很多开发人员，包括我自己，只是在他们创建的每个 React 应用程序中安装 Redux，而没有先问自己这个问题。最重要的是，我们中的一些人会开始将所有状态都放入 Redux 中，而忽略 React 本身具有非常可靠的状态管理机制这一事实。对于新手开发者来说尤其如此。

想想你将会陷入什么样的境地！当您将所有东西都存储在 Redux 中时，每次添加一个新的状态时，您都会增加创建和管理额外的 reducers 和动作的开销——这些都是乏味的样板代码。

现在，在重新获取数据之前，您还需要知道一些数据是否仍然存在于存储中。如果您宁愿每次都重新获取数据，那么您就没有真正很好地利用全局存储。

如果需要在 actions 内部执行异步操作，需要再添加一个类似 [redux-thunk](https://github.com/reduxjs/redux-thunk) 或者 [redux-saga](https://redux-saga.js.org/) 的库。如果你不知道如何使用这些库，你需要学习它们。

那么，如何确定你的项目是否真的需要 Redux 呢？

只有你能决定。每个项目都不一样。然而，这里有一些你可能使用 Redux 的例子。

*   有些状态需要在项目中的多个视图之间共享。但是，每个需要数据的组件总是可以从后端再次请求数据。如果后端的计算很昂贵并且很难缓存，您总是可以在顶层组件中或者通过使用[上下文 API](https://reactjs.org/docs/context.html) 来管理这些数据。如果他们似乎不能满足你的需求，那么你需要 Redux。
*   项目中的数据流和关系非常复杂，很难跟踪数据如何变化来解决 bug。在这种情况下， [Redux devtools](https://github.com/zalmoxisus/redux-devtools-extension) 中的回放/时间旅行机制肯定会派上用场。
*   您的应用程序自己创建、管理和操作所有数据，并简单地将更新的数据推送到某个存储中。这方面的一个例子可能是只在特定时间间隔与服务器同步数据的应用程序。

不要只相信我的话，即使 Redux 的 readme 也有一个[的完整章节](https://github.com/reduxjs/redux#before-proceeding-further)来帮助你决定它是否适合你的项目。

此外，这里还有一些你可能使用 Redux 的个人原因。

*   你和你的团队只是喜欢使用 Redux API，并且已经非常习惯了。就我个人而言，我讨厌编写和管理 redux 样板文件。
*   您不喜欢将状态与组件一起存储，而是希望使用一个集中的存储来存储所有的应用程序数据。

撇开个人原因不谈，如果您的项目中确实有 Redux，那么您不必将每个状态都存储在 Redux 中。您可以在 Redux 中存储跨多个视图共享的数据。此外，如果数据仅用于显示并且从不改变，那么上下文 API 无疑是一个很好的选择。Redux 也在内部使用上下文 API。我甚至创建了一个[小演示](https://github.com/mrdivinemaniac/react-context-store)来展示如何将上下文 API 用作存储。

底线是，即使你只是因为喜欢这个 API 而在一个项目中使用 Redux 也没有关系。它很可能不会以任何显著的方式影响您的最终用户。然而，重要的是要知道你为什么在你的项目中使用一个工具或者一个依赖，不管这个原因是多么的微不足道。

这将有助于您在未来的项目中做出明智的决策，或者在您需要更换有问题的技术时做出明智的决策。这不仅适用于 Redux，也适用于您可能在任何项目中使用的任何其他工具或技术。