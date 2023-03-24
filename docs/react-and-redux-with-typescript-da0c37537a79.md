# 使用 TypeScript 进行反应和还原

> 原文：<https://levelup.gitconnected.com/react-and-redux-with-typescript-da0c37537a79>

![](img/4d024077c0fa5a063a59ed692bf7aed5.png)

本周，我终于完成了过去几个月一直在做的一个兼职项目，学习 TypeScript 中的 React 和 Redux 这是一个密码管理器类型的应用程序，如果你想检查你可以在 [GitHub](https://github.com/JonJam/yorpw_ui_web) 上找到。

在构建这个应用的时候，我确实遇到过一些令人挠头的时刻，试图让 React 或 Redux 与 TypeScript 的类型系统很好地配合，而不是简单地求助于使用 [any](https://www.typescriptlang.org/docs/handbook/basic-types.html) 类型。有时我确实在 StackOverflow 或博客帖子上找到了一些有帮助的例子，但大多数时候我都是独自一人。

鉴于我在 TypeScript 中使用 React/Redux 的经验，我想我会在一个地方分享这些库的主要部分的示例，以帮助下一个遇到我遇到的任何问题的人。

我在 [GitHub](https://github.com/JonJam/react-redux-ts) 上创建了一个样本应用程序，将所有这些代码集中在一起，然后我就闭嘴展示代码了…

# 反应

## 类别组件

这些是使用 ES6 类定义的 React 组件，通常在您希望组件具有状态或使用生命周期功能(如 componentDidMount)时使用。我通常用它们来定义页面。

有关 React 中类组件的更多信息，请参见[文档](https://reactjs.org/docs/components-and-props.html#functional-and-class-components)。

React 类组件

## 功能成分

这些是使用函数定义的更简单的 React 组件，它们只能访问传入的属性。我用它们来创建表示组件，即那些定义 UI 元素的组件，如导航栏。

有关 React 中功能组件的更多信息，请参见[文档](https://reactjs.org/docs/components-and-props.html#functional-and-class-components)。

反应—功能组件

# Redux

## 行动

动作是围绕应用程序发送的改变状态的消息。

有关操作的更多信息，请参见 Redux 文档的[本节](http://redux.js.org/docs/basics/Actions.html)。

**动作类型**

所有动作都必须有一个定义为字符串常量的类型，在 TypeScript 中可以使用 actionTypeKeys.ts 文件中所示的[字符串枚举](https://www.typescriptlang.org/docs/handbook/enums.html)。

另一方面，index.ts 文件使用接口定义了登录动作的形状。这些接口可以与不同模块中定义的其他接口相结合，以定义描述应用程序中所有可能操作的 ActionTypes 类型。

Redux —动作类型

**动作创作者**

动作创建者是返回一个动作的函数，但是他们也可以分派其他动作。在下面的例子中，其他被分派的动作表明了调用的状态，即某件事情是正在进行、成功还是失败。

Redux —动作创建者

## 还原剂

Reducers 处理应用程序中创建的动作，并根据类型和有效负载依次改变存储中的状态。

关于减速器的更多信息，参见 Redux 文档的[本节](http://redux.js.org/docs/basics/Reducers.html)。

redux-Reducers

## 商店

存储是 Redux 的中心点，将动作和 Redux 结合在一起；它是保存应用程序状态的地方。

有关商店的更多信息，请参见 Redux 文档的[本节](http://redux.js.org/docs/basics/Store.html)。

Redux —商店

# 现在一起…

当您将所有这些部分放在一起，并希望在 React 组件中使用状态或调度操作时，它就被称为容器组件。

有关一起使用 React 和 Redux 的更多信息，请参见 Redux 文档的本节。

反应和还原—容器组件

[![](img/ff5028ba5a0041d2d76d2a155f00f05e.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/react) [## 学习 React -最佳 React 教程(2019) | gitconnected

### 前 45 名 React 教程。课程由开发人员提交并投票，使您能够找到最佳反应…

gitconnected.com](https://gitconnected.com/learn/react)