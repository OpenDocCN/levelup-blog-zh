# 颤振:Redux 集成

> 原文：<https://levelup.gitconnected.com/flutter-redux-integration-2a6747cf82f1>

![](img/931f10edecbe8dfc7f1a3c7240d10177.png)

我已经使用 Flutter 超过 8 个月了，在我学习框架和构建我的第一个应用程序期间，我*明显地*遇到了状态管理的挑战。我最初使用的是 [Provider](https://pub.dev/packages/provider) ，但是随着我的应用程序的规模和功能的增长，我意识到我对这个库的使用并不充分，状态逻辑的伸缩也不合适，主要是因为我缺乏这方面的经验。因此，在已经熟悉了 Redux 及其概念之后，我决定将我不断增长的应用从 Provider 迁移到 Redux。

在这篇文章中，我描述了我如何在我的 Flutter 应用程序中集成 Redux，这有望帮助像我这样的 Flutter 学习者开始这个主题。可能还有很大的改进空间，所以请不要犹豫，与我分享您将如何使这种集成更好。

> **注**:本文假设你对颤振发展有一些基本的了解，并重复一些概念。

# 1.导入 Redux 依赖项

首先，去你的`pubspec.yaml`添加 Redux 依赖项。对于这种集成，我们将使用以下软件包:

> 我建议在深入代码之前花些时间阅读一下这些包: [redux](https://pub.dev/packages/redux) ， [flutter_redux](https://pub.dev/packages/flutter_redux) ， [reselect](https://pub.dev/packages/reselect) ， [redux_thunk](https://pub.dev/packages/redux_thunk)

# 2.模型

为了简化 Redux 集成并向其添加一些结构，我们将创建以下模型:

## 还原

这个抽象类表示一个动作，并拥有一个`state`属性和一个`reduce`方法，因为在这个集成中，我决定将动作和它们的缩减器结合起来，以获得更好的可读性。

redux/redux_action.dart

## ReduxState

这个抽象类代表了你所在州的一部分。比如`CommentsState`或者`SearchState`。

redux/redux_state.dart

这个类拥有一个`reducer`方法，如果它匹配`state`类型，它将返回动作的`reduce`结果，否则，我们只返回状态。还有一个`clone`方法，我们将使用它来更新我们的状态而不改变它。最后，所有 redux 状态都必须实现一个`.initial`构造函数来提供初始值。

# 3.我们的第一个州区

比方说，你的应用程序中需要一个用于某些项目的部分，我们用一个类`Item`来操纵它。在一个专用的`items/`文件夹中，我们将创建一个名为`ItemsState`的部分:

redux/项目/状态. dart

在这个例子中，我们通过将 id 映射到相应的对象来存储项目。我们现在可以创建第一个操作，它将用于向 state 部分添加新项目:

redux/项目/操作. dart

在这个动作中，我们简单地覆盖了`reduce`方法来定义当这个动作被调度时我们想要如何更新我们的状态。

# 4.应用状态

现在是时候为我们的应用程序的根状态创建逻辑了:

*   `AppState`:一个包含我们所有状态部分的类(`ReduxState`)
*   `appReducer`:根部减速器

我们将把它们放在一个专用文件夹`app/`:

redux/app/state.dart

redux/app/reducer.dart

# 5.商店

我们现在拥有了创建商店所需的核心元素。我们可以通过下面的商店创建者做到这一点:

redux/store.dart

# 6.选择器

在我们的 UI 中使用这些代码之前，我们需要编写一些基本的选择器来帮助我们从商店中获取数据。首先，我们需要创建高级选择器(仅仅是函数)，它将返回我们状态的不同部分。在`app/`文件夹中，添加以下文件:

现在，我们可以为我们的项目编写两个选择器:

*   `itemsByIdSelector`为我们提供 id 到项目的映射
*   `itemSelector`这将给我们一个具体的项目

在`items/`文件夹中，我们可以添加以下内容:

多亏了 reselect，我们的选择器是可组合的，可以用作其他选择器的输入。

# 7.在用户界面中集成 Redux

要在我们的 UI 层中使用 Redux，首先要做的是在`main`函数中创建商店:

我们将商店传递给主`App`小部件，它使用一个`StoreProvider`将小部件树中的所有后代连接到商店:

就是这样！🎉现在让我们假设我们的商店神奇地装满了商品。您创建了一个小部件，一个惟一的 id 被传递给它，并且您想从商店获取相应的商品。你可以这样做:

在这个简单的例子中，我直接使用了`converter`中的选择器。当您需要更复杂和结构化的数据时，您可以创建您的自定义视图模型。

# 8.向存储中添加数据

最后，为了让我们的商店充满商品，我们必须在某个时候添加它们。最有可能的是，您的应用程序需要从后端服务获取这些项目，因此会有一些异步工作。要做到这一点，你可以在你的应用程序中的某个地方发送一个 *thunk。thunk 是一种特殊类型的动作，可以执行异步任务，并分派其他“正常”动作。让我们创建一个 thunk 来获取我们的商品并将它们添加到商店中。在我们的`items/`文件夹中，我们可以创建以下文件:*

在这个 thunk 中，您还可以分派额外的操作，比如将一个`loading`布尔值设置为 true，或者在异步工作完成之前和之后您需要做的任何事情。

Thunks 工作是因为它们被中间件拦截。我们需要在 store creator 中添加中间件:

好了，快到了。现在，如果需要，您可以在应用程序中的任何地方调度此 thunk:

```
store.dispatch(ItemsThunks.fetch())
```

这样做的一个好地方可能是，例如在像`ItemsSection`这样的更高的微件父级中，在`StoreConnector`初始化中:

这就对了。

# 文件结构

这个集成的最终文件结构如下所示(假设您将代码放在`lib/redux/`中):

```
redux/
--app/
----reducer.dart
----selectors.dart
----state.dart
--items/
----actions.dart
----selectors.dart
----state.dart
----thunks.dart
--redux_action.dart
--redux_state.dart
--store.dart
```

# 结论

所以，是的，Redux 有一些样板文件需要处理。但是一旦你设置好了，我发现它很容易使用，我的状态管理感觉健壮且结构良好。对于这种集成，我还需要考虑其他一些事情，比如正确地捕捉错误(主要来自您的 thunks)，或者使用正确的等式操作符创建视图模型，以避免不必要的重新呈现。本文的目标是介绍 Redux for Flutter，并演示一种集成它的方法。

我希望这能对一些阅读它的人有所帮助，不要犹豫指出任何可以改进的问题或事情！

干杯👋