# 通过示例了解 React 渲染道具

> 原文：<https://levelup.gitconnected.com/understanding-react-render-props-by-example-71f2162fd0f2>

## 使用 render props 逐步构建 React 组件，以了解如何实现该模式

在本教程中，我们将涵盖使用渲染道具构建自己的组件所需的概念。我们将使用 render props 实现一个组件来将 React 状态保存到`localStorage`，称为`<Storage/>`，这将允许您将功能注入到组件中，而无需在整个应用程序中复制逻辑。

[](https://gitconnected.com/learn/react) [## 学习 React -最佳 React 教程(2019) | gitconnected

### React 的前 48 门课程。教程由开发者提交并投票，让你找到最好的反应…

gitconnected.com](https://gitconnected.com/learn/react) 

> 如果您有兴趣查看高阶元件的相同示例，请查看本文

![](img/ce831295771fbb4c34676b7f048cbaec.png)

# 渲染道具简介

render props 模式是一种在组件之间共享功能而无需重复代码的方式。官方的 React 文件将其定义为—

> 术语“渲染道具”指的是一种简单的技术，使用一个值为函数的道具在 React 组件之间共享代码。

```
<DataProvider render={data => (
  <h1>Hello {data.target}</h1>
)}/>
```

通过使用 prop 来定义呈现的内容，组件只是注入功能，而不需要知道它是如何应用到 UI 的。让我们看一个例子来理解这实际上意味着什么。

![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)[](https://gitconnected.com/portfolio-api) [## 组合 API —轻松发展您的编码事业| gitconnected

### 消除在每个单独位置手动更新您的详细信息的痛苦。只需在您的中更改一次数据…

gitconnected.com](https://gitconnected.com/portfolio-api) ![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

# 渲染道具模式

Render props 意味着传递一个由单独组件的 props 定义的 Render 函数来指示共享组件应该返回什么。在根级别，它必须采用以下形式:

`this.props.render()`是由另一个组件传入的函数。这个函数本身应该返回一个 React 组件。我们上面创建的简单渲染道具只是将元素包装在一个`<div>`中。为了利用`<SharedComponent />`,我们要做以下事情:

还有一点值得注意的是，道具的名字不需要一定是`render`。重要的是它返回一个 React 元素。例如，我们可以将上面的道具重命名为`wrapThisThingInADiv`，它的功能仍然完全相同。

# 一个简单的渲染道具组件示例

现在我们已经建立了 render prop 模式，让我们利用它在组件之间实际共享一些功能。我们将创建一个组件，通过 render prop 函数在被渲染的组件中注入数据。

我们的团队已经算出了`secretToLife`，结果是数字`42`。我们的一些组件需要共享这些信息，我们可以创建一个名为`ShareSecretToLife`的渲染道具组件来传递给我们的组件。

这里我们将`secretToLife`作为参数传递给我们的`render`属性函数。这也允许我们使用返回的 React 组件中的值——在本例中，在`<h1>`中显示粗体 42。

# 一个实用渲染道具组件实例

既然我们已经了解了如何使用渲染道具共享数据和功能，我们就可以构建一个具有实际意义的渲染道具了。由于 render prop 组件仅仅是一个组件，因此我们可以访问整个 React API，例如生命周期方法和组件状态。

我们的`<Storage />`组件的功能是用`localStorage`保存/加载组件的状态，允许我们在页面加载时快速访问和呈现它。然后，它将通过渲染道具共享此功能。这使它具有以下形式:

在`<Storage />`的顶部，我们在组件的状态中有一个单独的项目，它跟踪`localStorage`在给定的浏览器中是否可用。我们使用`componentDidMount`生命周期钩子，它将通过`checkLocalStorageExists`函数检查`localStorage`是否存在。在这里，它将测试保存一个项目，如果成功，将状态设置为 true。

我们还向组件添加了三个函数— `load`、`save`和`remove`。这些用于直接访问可用的`localStorage` API。我们在组件上的三个函数被传递来呈现 prop 函数。

现在，我们将创建一个新的`render`组件，在我们的共享`<Storage />`组件中使用，这允许它获得存储功能。它将用于显示用户的用户名和最喜欢的电影。然而，获取这些信息的 API 调用需要很长时间。我们也可以假设这些值一旦设定就永远不会改变。

为了确保我们有很好的用户体验，我们将只在值没有保存到`localStorage`时调用这个 API。然后每次用户返回页面时，他们可以立即访问数据，而不是等待我们的 API 返回。

我们共享的`<Storage />`注入`<ComponentNeedingStorage />`使用的`save`和`load`。首先，我们通过尝试从存储器或组件状态加载来检查`username`和`favoriteMovie`是否存在。如果它们不存在，我们将使我们的`reallyLongApiCall`被称为内部`fetchData`。我们想确保我们只调用这个函数一次，所以我们也管理一个`isFetching`变量。一旦数据从`localStorage`加载或者从 API 调用返回，我们就向用户显示它们。

# 包裹

Render props 是一种强大的模式，允许开发人员在组件之间共享功能。它使我们能够保持代码干燥，并将方法提取到一个公共位置

渲染道具通常被比作高阶组件，因为它们的用途相似。如果您有兴趣了解更多关于高阶组件的知识，请查看我的另一篇文章[中我构建了相同的存储功能。](/understanding-react-higher-order-components-by-example-95e8c47c8006?source=user_profile---------1----------------)

由开发者决定他们在渲染 props 和 HOC 中更喜欢哪种方法。我个人认为渲染道具非常适合只读操作，比如跟踪屏幕上的滚动位置或鼠标位置。我相信，对于更复杂的操作，比如我们的`localStorage`功能，HOC 往往更好。

![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)[](https://gitconnected.com/portfolio-api) [## 组合 API —轻松发展您的编码事业| gitconnected

### 消除在每个单独位置手动更新您的详细信息的痛苦。只需在您的中更改一次数据…

gitconnected.com](https://gitconnected.com/portfolio-api) ![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

*如果您觉得本文有帮助，请点击👏。* [*关注我*](https://medium.com/@treyhuffine) *获取更多关于区块链、React、JavaScript 和开源软件的文章！你也可以在*[*Twitter*](https://twitter.com/treyhuffine)*或者*[*git connected*](https://gitconnected.com/treyhuffine)*上找到我。*