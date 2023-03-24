# 通过示例了解 React 高阶组件

> 原文：<https://levelup.gitconnected.com/understanding-react-higher-order-components-by-example-95e8c47c8006>

## 逐步构建 React 高阶组件，以了解如何实现特设模式

在本教程中，我们将涵盖构建您自己的高阶组件(HOC)所需的概念。我们将实现一个 HOC 来将 React 状态保存到`localStorage`，称为`withStorage`，这将允许您将功能注入到组件中，而无需在整个应用程序中复制逻辑。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev) 

> 如果你有兴趣在渲染道具中看到同样的例子，请查看本文

![](img/3970d71eb62f189183ea365fb44cd6a6.png)

# HOC 简介

React 中的高阶组件是一种用于在组件之间共享公共功能而无需重复代码的模式。高阶分量实际上不是一个分量，而是一个函数。特殊函数将组件作为参数，并返回一个组件。它将一个组件转换成另一个组件，并添加额外的数据或功能。简而言之:

```
const NewComponent = (BaseComponent) => {
  // ... create new component from old one and update
  return UpdatedComponent
}
```

在 React 生态系统中，您可能熟悉的两个 HOC 实现是来自 Redux 的`connect`和来自 React 路由器的`withRouter`。Redux 的`connect`函数用于让组件访问 Redux 存储中的全局状态，并将这些值作为道具传递给组件。`withRouter`函数将路由器信息和功能注入到组件中，使开发人员能够访问或更改路由。

![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

创建和维护一份简历并不有趣。相反，让我们为你生成一份令人敬畏的简历:) [**简历生成器>**](https://gitconnected.com/resume-builder)

[](https://gitconnected.com/resume-builder) [## 软件工程师简历生成器和示例| gitconnected

### 使用您的个人资料中的详细信息构建一份有价值的简历模板。从你的投资组合网站链接到你的简历或…

gitconnected.com](https://gitconnected.com/resume-builder) ![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

# 高阶组件模式

高阶分量是以分量作为自变量并返回分量的函数。这意味着特设将始终具有类似如下的形式:

`higherOrderComponent`是一个将名为`WrappedComponent`的组件作为参数的函数。我们创建一个名为`HOC`的新组件，它从其`render`函数中返回`<WrappedComponent/>`。虽然在这个简单的例子中，这实际上没有增加任何功能，但是它描述了每个特设函数都将遵循的通用模式。我们可以如下调用特设:

```
const SimpleHOC = higherOrderComponent(MyComponent);
```

# 一个基本的例子

现在，我们将扩展我们的基本高阶组件模式，将数据注入我们的包装。

我们的团队实际上已经算出了`secretToLife`，结果是数字`42`。我们的一些组件需要共享这些信息，我们可以创建一个名为`withSecretToLife`的特设来将它作为道具传递给我们的组件。

请注意，这个特设几乎与我们的基本模式相同。我们所做的就是添加一个 prop `secretToLife={42}`，它允许被包装的组件通过调用`this.props.secretToLife`来访问值。

另一个补充是，我们将传递给组件的道具分散开来。这确保了传递给包装组件的任何其他属性都可以通过`this.props`访问，就像组件没有通过我们的高阶组件函数传递时调用它们一样。

我们的`WrappedComponent`，只是`<DisplayTheSecret/>`的增强版，将允许我们作为道具访问`secretToLife`。

# 一个实用的例子

既然我们已经对 HOC 的基本模式有了很好的理解，我们就可以构建一个实际的应用程序了。高阶组件可以访问所有默认的 React API，包括`state`和生命周期方法。

我们的`withStorage` HOC 的功能将是保存/加载组件的状态，允许我们在页面加载时快速访问和呈现它。

在`withStorage`的顶部，我们在组件的状态中有一个单独的项目，它跟踪`localStorage`在给定的浏览器中是否可用。我们使用`componentDidMount`生命周期挂钩，它将检查本地存储是否存在于`checkLocalStorageExists`函数中。在这里，它将测试保存一个项目，如果成功，将状态设置为 true。

我们还为我们的 HOC 添加了三个函数— `load`、`save`和`remove`。这些用于直接访问`localStorage` API，如果它可用的话。我们在 HOC 上的三个函数被传递给我们包装的组件，以便在那里使用。

现在我们将创建一个新的组件来包装我们的`withStorage` HOC。它将用于显示用户的用户名和最喜欢的电影。然而，获取这些信息的 API 调用需要很长时间。我们也可以假设这些值一旦设定就永远不会改变。

为了确保我们有一个很好的用户体验，我们将只在值没有被保存的时候调用这个 API。然后每次用户返回页面时，他们可以立即访问数据，而不是等待我们的 API 返回。

在我们包装的组件的`componentDidMount`内部，我们首先尝试从`localStorage`访问`username`和`favoriteMovie`。如果这些值不存在，我们就调用昂贵的名为`this.props.reallyLongApiCall`的 API。一旦这个函数返回，我们将用户名和收藏夹保存到`localStorage`，并更新组件的状态以在屏幕上显示它们。

# 高阶元件考虑因素

*   特设应该是一个没有副作用的纯功能。它不应该做任何修改，而只是通过将原始组件包装在另一个组件中来组合它。
*   不要在组件的 render 方法中使用 HOC。在组件定义之外访问特设。
*   静态方法必须被复制才能访问它们。一个简单的方法是`hoist-non-react-statics`包。
*   引用不通过。

*如果您觉得本文有帮助，请点击👏。* [*关注我*](https://medium.com/@treyhuffine) *获取更多关于区块链、React、JavaScript 和开源软件的文章！你也可以在*[*Twitter*](https://twitter.com/treyhuffine)*或者*[*git connected*](https://gitconnected.com/treyhuffine)*上找到我。*