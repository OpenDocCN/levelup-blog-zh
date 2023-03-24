# 在 React 中使用 GraphQL 片段的更好方法

> 原文：<https://levelup.gitconnected.com/a-better-way-to-use-graphql-fragments-in-react-4f54bf862062>

## 在呈现数据的组件中定义片段有很多好处。

![](img/b2223f4452fbb011b8f3eaf1f19f6e27.png)

由 [Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/reaching?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

使用基于组件的框架(React，Vue)的一个重要原因是它允许更隔离的组件设计，这有助于解耦和单元测试。另一个好处是使用展示应用，如[故事书](https://storybook.js.org/)。这些延续了隔离的理念，允许在主应用程序之外进行设计和原型制作。

当组件数量开始增长并且我们开始获取数据时，我们需要一个新的模式，[容器组件模式](https://learn.co/lessons/react-container-components)。如果使用 GraphQL 进行数据传输，我们希望继续使用这种模式，但是有了新的变化。创建独立组件时，他们应该定义需要呈现的数据。这可以通过每个组件来更好地实现，甚至是表示性的组件，定义它们需要用自己的 GraphQL 片段来呈现的数据。

# **显示时间**

假设我们有一个组件来呈现一个显示标题的 Github 问题列表。在容器组件模式中，我们有一个“容器”组件`GithubIssueListContainer`，它处理查询的运行。之后，它将数据传递给需要它呈现的表示组件`GithubIssueInfoCard`。

这里的问题是，`GithubIssueInfoCard`依赖于它的父组件来了解 GraphQL 图中的数据来自哪里。

如果我们想从图中呈现一个新的字段，例如`labels`，我们需要将它添加到`GithubIssueListContainer`中的查询中，并通过 props 传递给`GithubIssueInfoCard`。这需要修改`GithubIssueListContainer`中的查询和`GithubIssueInfoCard`中的属性。

# **就是这条路**

按照我们的隔离原则，如果`GithubIssueInfoCard`定义了它需要从 GraphQL 图中呈现什么数据，那会怎么样？这样，当我们改变这个组件的数据时，只有这个组件需要改变。

乍一看，这似乎很奇怪，但好处是值得的。就像编程中的任何事情一样，它不会没有取舍。

# **好处**

## **少母组件耦合**

当组件定义了它需要呈现的数据时，它将组件与其父组件解耦。例如，如果您想在另一个页面上显示`GithubIssueInfoCard`,将片段导入容器组件以获取正确的数据。例如

## **类型变得更容易维护**

如果使用 TypeScript，您可能会从 GraphQL 查询中生成类型。我们的新模式的一大好处是在组件中定义道具。您可以从我们生成的类型文件中将它需要呈现的数据定义为一个类型。

当片段改变时，在你生成类型之后，不需要任何修改！

## **首先开发组件时更改的机会较少**

随着 Storybook 变得流行，许多开发人员开始首先在 Storybook 中开发组件，然后在稍后的时间将它们集成到应用程序中。可能出现的情况是，app 集成过程中，道具定义错误。

定义该组件需要呈现的 GraphQL 图形的片段，由于迫使开发人员知道需要呈现的数据的确切形状，因此在集成时更改代码的机会较少。当然，这只有在预先定义 API 的情况下才有可能，但有时情况并非总是如此。

# **取舍**

当然，像编程中的所有事情一样，这种方法也有权衡。值不值得，就看你自己了。

## **表示性组件不通用**

糟糕的是，我们的表示组件变得更加耦合到应用程序和 API 数据模型。如果我们想迁移到一个组件库供其他人使用，这些组件将需要被重构以移除它们的片段。这不是太多的工作，但它比选择更多的工作。

## **碎片有时会变得难以管理**

将许多片段导入到一个 GraphQL 查询中并不是最好的体验。如果我们在一个容器组件中有许多表示组件，将它们全部导入可能会很麻烦。有时您可能会忘记导入片段，Apollo 会返回一些无用的消息。

## **结论**

我们在 Yolk 使用这种模式已经有一段时间了，每个人都喜欢它。我们首先在 Storybook 中开发组件，这迫使开发人员了解数据来自哪里，并询问有关数据模型及其用法的问题。