# React 应用程序中的特殊模式

> 原文：<https://levelup.gitconnected.com/hoc-pattern-inside-your-react-app-50265362f38f>

## 如何重用您的逻辑并保持代码干燥

![](img/a6d0f5fa3ec76ec6b8ef80f8e193f2c7.png)

安东·大流士在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 什么是特设委员会？

> 高阶组件(HOC)是 React 中重用组件逻辑的一种高级技术。本质上，hoc 不是 React API 的一部分。它们是从 React 的组合性质中出现的一种模式。— [反应文件](https://reactjs.org/docs/higher-order-components.html)

好吧，也许这很难理解，但是 React 文档有一个更简单的定义:

> 具体而言，**高阶分量是取一个分量并返回一个新分量的函数。—** [**反应文件**](https://reactjs.org/docs/higher-order-components.html)

现在我认为这是理解 HOC 的一个更简单的方法。但是它如何帮助你重用你的代码呢？你如何写你的第一篇论文？

# 不带 HOC 的代码

让我们假设您的 React 应用程序将呈现一个用户表。为此，您调用一个 API 并等待它返回数据，同时显示一个加载器并呈现返回的数据。代码可能看起来像这样:

进行 API 调用以获取用户并在表中呈现数据

现在，您有了另一个呈现球队表格的组件。整个逻辑与前面的组件相同:

进行 API 调用以获取团队并在表格中呈现数据

你看到代码几乎一样了吗？这就是 HOC 可以帮助你使你的代码更加简洁，并且在渲染你的组件时去除这种重复的逻辑的地方。

# 用 HOC 编码

首先，像这样创建您的 HOC:

封装加载和空数据状态

这个特设的目的是接收一个`Component`，在这个例子中是`Table`组件，加上一些其他的道具来决定如何呈现它的逻辑。

现在让我们在您的`Users`和`Teams`组件中使用它:

使用 HOC 保持代码干燥的用户组件

团队组件使用 HOC 保持代码干燥

就是这样！您已经将加载和空数据状态封装在一个文件中，该文件可以在应用程序的任何部分重用。当您用您的 HOC 包装一个组件时，您是在用 HOC 内部的逻辑增强那个组件。

# 多用点 HOC！

这种模式可以多次应用于同一个组件。

您想添加分页吗？只需创建一个新的特设组件，例如`withPagination`:

特设处理分页逻辑

然后像这样在你的组件中使用它:

使用 HOCs 处理加载和分页的用户组件

# 问题

**为什么要这样设置 HOC 的显示名称？**

```
WithLoader.displayName = `WithLoader(${Component.displayName || Component.name || 'Component'  })`
```

这是 [React 文档](https://reactjs.org/docs/higher-order-components.html#convention-wrap-the-display-name-for-easy-debugging)推荐使用的一个约定，以便于调试。

**为什么在主组件外用 HOC 声明增强组件？**

声明 inside 将在每次渲染时重新创建增强的组件。

**为什么只将** `**{...props}**` **传递给由 HOC 返回的组件？**

这样，您就不需要担心组件需要什么属性，只需传递除了呈现加载器、空状态或分页之外的所有属性。

[](https://blog.jagonzalr.com/membership) [## 加入我的介绍链接媒体-何塞安东尼奥冈萨雷斯罗德里格斯

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.jagonzalr.com](https://blog.jagonzalr.com/membership)