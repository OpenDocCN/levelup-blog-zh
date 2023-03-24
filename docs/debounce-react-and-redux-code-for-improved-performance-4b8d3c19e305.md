# 消除 React 和 Redux 代码的抖动以提高性能

> 原文：<https://levelup.gitconnected.com/debounce-react-and-redux-code-for-improved-performance-4b8d3c19e305>

![](img/2dba53fa22ab00eaabafc18ef3979870.png)

一个[去抖](https://medium.com/gitconnected/debounce-in-javascript-improve-your-applications-performance-5b01855e086)是一个每个网络开发者都应该拥有的工具。它通过限制昂贵的计算、API 调用和 DOM 更新的数量来提高性能。尽管去抖技术已经存在多年，但它仍然是现代库和框架的一个很好的选择。在[**git connected**](https://gitconnected.com)**中，我们将它与 React 和 Redux 一起使用，以保持应用程序运行速度极快。**

**[](https://gitconnected.com/learn/react) [## 学习 React -最佳 React 教程(2019) | gitconnected

### React 的前 48 门课程。教程由开发者提交并投票，让你找到最好的反应…

gitconnected.com](https://gitconnected.com/learn/react) 

我们将定义一个`<Project>`组件，为所有项目创建一个列表。它连接到一个`mockApi()`来模拟执行 API 调用的需求。我把所有东西都放在了单个文件中，而不是连接它 Redux，但是所有的原则仍然适用于您设置的任何状态管理和 API 框架。我将代码中典型的瓶颈编号为 1-3。

![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)[](https://gitconnected.com/portfolio-api) [## 组合 API —轻松发展您的编码事业| gitconnected

### 消除在每个单独位置手动更新您的详细信息的痛苦。只需在您的中更改一次数据…

gitconnected.com](https://gitconnected.com/portfolio-api) ![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

我们有一个简单的按名称列出项目的`<ul>`，和一个允许我们过滤这些项目的`<input>`。当`<input>`上有一个`onKeyUp`被触发时，它会执行`handleFilter()`，这个函数包含在我们的`debounce`函数中。这限制了我们的`mockApi()`函数只能每 500 毫秒调用一次。通过去抖，我们不再需要每次按键都执行这些操作，我们只在用户停止输入一小段时间后才这样做。这确保了我们不会执行不必要的操作来寻找用户根本不关心的项目。让我们看一下每个步骤，了解我们实际节省了多少。

1.  在最后一次按键后去抖时间到期后，第一步是进行我们的 API 调用。因为我们已经对从 API 中获取的函数进行了去抖，所以我们只在输入结束时发出一次请求。
2.  这里发生了两件事——从数据库中检索项目，然后执行繁重的计算来过滤和排序数据。在实际环境中，服务器对这些数据的查询和计算效率会更高，但是学习结果仍然完全有效。在这种情况下，让我们假设我们在去抖计时器中键入 5 个字符。如果没有反跳，我们将对 10 亿个项目执行 5 次查询，然后执行过滤、字符串匹配和排序。因此，我们在一个已经非常繁重的计算密集型操作上获得了 5 倍的性能提升。
3.  React 很快，但最大的问题是太多的渲染和协调。此外，DOM 交互非常慢。通过去反跳，我们防止了`setState()`，这大大减少了我们强制反应以协调和附加列表到 DOM 的次数。

如果没有去抖，这个组件对于如此大量的数据几乎无法使用。这个概念很容易扩展到 Redux，用于任何状态更新或 API 调用。如果这些互动分派了其他行动，我们又一次获得了倍增的收益。

去抖的常见情况是`resize`、`scroll`和`keyup/keydown`事件。此外，您应该考虑将任何触发过度计算或 API 调用的交互用去抖封装起来。

*如果您觉得这篇文章有帮助，请点击*👏*。* [*关注我*](https://medium.com/@treyhuffine) *了解更多关于 React、Node.js、JavaScript 和开源软件的文章！你也可以在*[*Twitter*](https://twitter.com/treyhuffine)*或者*[*git connected*](https://gitconnected.com/treyhuffine)*上找到我。*

![](img/f2aaaee00b80953a973cc4d292ff00ad.png)**