# 使用 JSX 和 React 渲染简介

> 原文：<https://levelup.gitconnected.com/introduction-to-using-jsx-and-rendering-with-react-38dfa1221a98>

![](img/a943afd67e854ff7671328e455f83f5b.png)

[Peter G](https://unsplash.com/@pepegombos?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

React 是一个用于创建前端视图的库。它有一个庞大的图书馆生态系统与之合作。此外，我们可以用它来增强现有的应用程序。

编写应用程序最简单的方法是使用 JSX。在这篇文章中，我们将看看如何使用 JSX 来编写动态反应应用程序。

# 在 JSX 嵌入表达式

我们可以在花括号之间嵌入 JavaScript 表达式。例如，我们可以写:

```
import React from "react";
import ReactDOM from "react-dom";
function App() {
  const greeting = "Hello World";
  return (
    <div>
      <h1>{greeting}</h1>
    </div>
  );
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

然后我们又在屏幕上看到了 Hello World。

一些更有用的世界调用如下函数:

```
import React from "react";
import ReactDOM from "react-dom";
function App() {
  const formatName = user => {
    return `${user.firstName} ${user.lastName}`;
  };const user = {
    firstName: "Jane",
    lastName: "Smith"
  };return (
    <div>
      <h1>{formatName(user)}</h1>
    </div>
  );
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

在上面的代码中，我们在`App`组件中定义了一个`formatName`函数，它接受一个`user`对象并返回连接在一起的`user.firstName`和`user.lastName`。

然后我们用这些属性定义了一个`user`对象，并在花括号内调用函数。

无论返回什么，都将显示在大括号之间。在这种情况下，它会是简·史密斯。

# 用 JSX 指定属性

我们可以通过将 kebab 大小写或小写属性名改为 camel 大小写来为 JSX 添加 HTML 属性。

例如，我们可以写:

```
import React from "react";
import ReactDOM from "react-dom";
function App() {
  return (
    <div>
      <h1 tabIndex="0">Foo</h1>
    </div>
  );
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

在 HTML 中`tabindex`是属性，但在 JSX，它被称为`tabIndex`。

`"0"`将被 React 解释为字符串。

如果我们想传入一个数字，我们必须写:

```
<h1 tabIndex={0}>Foo</h1>
```

任何其他有效的 JavaScript 表达式也可以在花括号之间。

其他重要的属性包括 HTML `class`属性，在 JSX 是`className`。

# JSX 防止注射攻击

JSX 会对注射到任何地方的输入进行消毒。这意味着 React 在渲染之前会对嵌入在 JSX 中的任何内容进行转义。

这应该可以防止恶意代码从注入 JSX 代码的脚本中运行。

# JSX 代表物体

JSX 代码和`React.createElement`返回的组件相同。

例如:

```
const App = <h1 className="hello">Hello, world!</h1>;
```

与以下内容相同:

```
const App = React.createElement("h1", { className: "hello" }, "Hello, world!");
```

它们都被称为反应元素，描述我们在屏幕上看到的东西。React 使用返回的对象来构造 DOM 并更新它。

![](img/5a97e4f602678e4754ed61c163418a86.png)

[Yura Fresh](https://unsplash.com/@mr_fresh?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 渲染元素

React 元素是普通对象，创建它们不需要太多资源。

正如我们在前面的例子中看到的，用 React 构建的应用程序被装载到单个元素中并被渲染。

如果我们想将 React 组件集成到现有的应用程序中，我们通常会做如下事情:

```
import React from "react";
import ReactDOM from "react-dom";const App = <h1>Hello, world</h1>;const rootElement = document.getElementById("root");
ReactDOM.render(App, rootElement);
```

React 应用程序总是需要有一个根节点，在这里它可以挂载到一个现有的 DOM 元素中。

# 更新呈现的元素

React 元素是不可变的。因此，一旦它们被创建，我们就可以改变它的子元素或属性。

如果我们想要更新内容，我们必须再次创建它并将其传递给`ReactDOM.render()`。

例如，如果我们想用 React 创建一个时钟小部件，我们可以这样做:

```
import React from "react";
import ReactDOM from "react-dom";function tick() {
  const element = (
    <div>
      <p>{new Date().toLocaleTimeString()}</p>
    </div>
  );
  ReactDOM.render(element, document.getElementById("root"));
}setInterval(tick, 1000);
```

在上面的代码中，我们有一个包含`element`对象的`tick`函数，它是一个 React 组件，将当前日期格式化为时间字符串。

然后它通过传入`element`并将其安装在 ID 为`root`的元素中来调用`ReactDOM.render`。

然后一旦定义了`tick`函数，我们就把它传递给`setInterval`每秒调用一次。

# React 只更新必要的内容

React 将元素及其子节点与前一个元素进行比较，并将它们之间的差异应用于 DOM。

在上面的代码中，只有日期字符串发生了变化，所以只有它会被更新。

在使用 React 构建应用程序时，这是一个不用担心的问题。

# 结论

我们可以使用 JSX 轻松构建 React 应用程序。

为了传入 HTML 属性，我们在 camelCase 中编写属性名，并在花括号之间传入任何值或表达式。如果是字符串，我们可以把内容放在引号中。

为了使我们编写的代码安全，React 在呈现字符之前对它们进行转义，以便恶意代码无法运行。

React 组件是不可变的。一旦它们被创建，我们就不能改变任何东西，除非用新数据再次创建它们。

React 自动比较新的和以前的 DOM 结构，并且只应用需要应用的更改。