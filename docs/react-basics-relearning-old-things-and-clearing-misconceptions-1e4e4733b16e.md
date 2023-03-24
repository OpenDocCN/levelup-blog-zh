# 反应基础——重新学习旧的东西，消除误解

> 原文：<https://levelup.gitconnected.com/react-basics-relearning-old-things-and-clearing-misconceptions-1e4e4733b16e>

![](img/7078e12c5c6549d7c3e216b379838ac8.png)

图为[安德鲁·斯莫尔](https://unsplash.com/@andsmall?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

React 是一个用于创建 web 应用程序和移动应用程序的流行库。

在本文中，我们将了解如何从头开始学习 React 并使用它构建应用程序。

我们还会看到一些需要澄清的误解。

# ES2015

一切都将写在 ES2015+里。

我们将在 React 应用中大量使用 spread and rest 操作符和析构函数。

它使得创建 React 组件比使用旧版本的 JavaScript 容易得多。

# 声明式编程

React 是一个声明性库。这意味着我们不会一行一行地写出所有的步骤。

相反，我们只是告诉它我们想要实现什么，而不是一行一行地写出来。

例如，不写:

```
const lower = str => str.toLowerCase();
```

我们写道:

```
import React from "react";export default function App() {
  return <p>hello world</p>;
}
```

声明式编程意味着我们只需编写代码来告诉 React 我们想要呈现什么。

这不同于命令式编程，在命令式编程中，我们写出代码来告诉计算机做什么来实现我们想要的。

# 忘却一切

我们必须抛弃过去对 JavaScript 编程的了解。

它为 JavaScript 引入了一种新的语法，看起来像 HTML。

我们还了解到关注点的分离非常重要。

但是有了 React，我们必须把这些想法扔出窗外。

相反，我们把事物分成组件。

组件可能都包含一些用于保存临时状态的逻辑，也可以从其他组件获取数据。

此外，我们认为我们将 CSS 从 JavaScript 中分离出来，但 React 并不总是这样。

我们对 React 不再有清晰的关注分离。

例如，我们写道:

```
import React from "react";export default function App() {
  return <p style={{ color: "red" }}>hello world</p>;
}
```

我们可以像在 HTML 中一样添加一个`style`属性。

然后我们会看到红色文本而不是黑色文本。

这更适用于动态风格。

要添加共享 CSS，我们可以有一个包含样式的对象:

```
import React from "react";const styles = { color: "red" };export default function App() {
  return <p style={styles}>hello world</p>;
}
```

# 误解

React 本身就是一个小型库。

然而，它确实有大量的图书馆。

此外，我们必须处理包管理器、传输器、模块包等。将 React 项目构建到我们可以使用的 JavaScript 应用程序中。

还有许多复杂的样板项目。

它们有很多依赖项和组件。

这些样板文件让 React 看起来比它应该的更难。

React 本身是一个微型库，可以在任何页面中使用。

只有一个 React 脚本和一个 React DOM 脚本。

我们可以添加带有脚本标签的脚本，然后我们可以开始使用 React。

对于非常简单的应用程序，我们不必使用 JSX。我们可以使用`createElement`来制作我们的应用程序。

JSX 只是 T2 的简称。

如果我们使用一个有很多依赖项和包的样板文件，那么我们就会迷失方向。

Transpilers 允许我们使用最新的 JavaScript 特性，而不需要内置在浏览器中的支持。

创建简单 React 项目的最简单方法是使用`create-react-app`项目。

我们可以通过运行以下命令来安装它:

```
npm install -g create-react-app
```

然后我们可以运行:

```
create-react-app hello-app
```

或者我们可以不安装`npx`就运行它:

```
npx create-react-app hello-app
```

然后，如果我们进入`hello-app`项目文件夹，我们可以用`npm start`运行它。

我们在项目中有一个`App.js`文件，我们可以开始修改它来开始。

最初的项目非常简单。它有一个组件、一个 CSS 文件和最少的样板文件。

这是启动 React 项目最简单的方式。

![](img/09f50e1691e637515996d33f0e44bcf7.png)

Anthony DELANOIX 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

React 是一个声明式视图库，我们可以用它来构建客户端 web 应用程序。

我们不得不抛弃以前学过的关于编程设计的东西来构建 React 应用程序。

此外，为了防止我们迷路，我们应该使用`create-react-app`来创建我们的 React 应用程序项目。