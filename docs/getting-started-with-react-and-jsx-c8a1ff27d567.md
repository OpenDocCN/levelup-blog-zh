# React 和 JSX 入门

> 原文：<https://levelup.gitconnected.com/getting-started-with-react-and-jsx-c8a1ff27d567>

![](img/dcb1d204b1f036c02489f1f841e5b9f3.png)

照片由[于切尔·莫兰](https://unsplash.com/@yucelmoran?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 是一个用于创建前端视图的库。它有一个庞大的图书馆生态系统与之合作。此外，我们可以用它来增强现有的应用程序。

在本文中，我们将了解如何使用 React 创建简单的应用程序。

# 入门指南

创建 React 应用程序最简单的方法是使用 Create React 应用程序节点包。

我们可以通过运行来运行它:

```
npx create-react-app my-app
```

然后，我们可以转到`my-app`，通过运行以下命令来运行应用程序:

```
cd my-app
npm start
```

创建 React 应用程序对于创建单页应用程序非常有用。

React 应用不处理任何后端逻辑或数据库。

我们可以使用`npm run build`来构建应用程序，以创建用于生产的已构建应用程序。

# 创建我们的第一个 React 应用

一旦我们像上面那样运行 Create React App，我们就可以创建我们的第一个应用程序了。为了创建它，我们进入`App.js`，然后在那里开始修改代码。

为了使编写我们的应用程序更容易，我们将使用 JSX 来完成。它是一种类似 HTML 的语言，但实际上只是 JavaScript 之上的语法糖。

因此，我们将使用 JSX 常用的 JavaScript 结构。

我们将从创建一个 Hello World 应用程序开始。为此，我们将`index.js`中的内容替换为:

```
import React from "react";
import ReactDOM from "react-dom";function App() {
  return (
    <div>
      <h1>Hello World</h1>
    </div>
  );
}const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

在上面的代码中，我们有`App`组件，它只是一个函数。它返回:

```
<div>
  <h1>Hello World</h1>
</div>
```

这是我们显示 Hello World 的 JSX 代码。`h1`是一个标题，`div`是一个 div 元素。

上面的代码看起来像 HTML，但它实际上是 JSX。

我们上面的是一个基于函数的组件，因为组件是作为函数编写的。

在最后 2 行中，我们从`public/index.html`中获取 ID 为`root`的元素，并将`App`组件放入其中。

编写上面代码的另一种方法是:

```
import React from "react";
import ReactDOM from "react-dom";class App extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello World</h1>
      </div>
    );
  }
}const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

上面的代码是一个基于类的组件，它有一个`render`方法将相同的 JSX 代码转换成 HTML。

这两个例子的区别在于，一个是函数，另一个是扩展`React.Component`的类。

否则，他们是一样的。任何组件文件都必须包括:

```
import React from "react";
import ReactDOM from "react-dom";
```

否则，我们会得到一个错误。

React 不需要 JSX，只是用起来方便多了。创建 Hello World 应用程序的第三种方法是使用`React.createElement`方法。

我们可以使用如下方法:

```
import React from "react";
import ReactDOM from "react-dom";const e = React.createElement;
const App = e("h1", {}, "Hello World");const rootElement = document.getElementById("root");
ReactDOM.render(App, rootElement);
```

`createElement`方法的第一个参数是字符串形式的标记名，第二个参数是 props，它是我们传递给所创建的组件的对象，第三个参数是元素的内部文本。

我们不会经常使用它，因为如果我们必须嵌套组件和增加交互，它会变得非常复杂。

在所有 3 个示例中，我们得到:

![](img/784145dc0e5b5b8a61c621736e1a071e.png)

显示在屏幕上。

![](img/3a1ee94436a6dad2e5c49b8fdb25656f.png)

赫尔墨斯·里维拉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

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
  }; const user = {
    firstName: "Jane",
    lastName: "Smith"
  }; return (
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

# 结论

我们可以用 Create React app 节点包创建一个 React App。

然后我们可以添加组件作为一个函数，类，或者用`React.createElement`。

前两种方法用得最多，因为它们分别在组件类的函数或`render`方法中返回 JSX。

用 React 写 JavaScript 代码，JSX 比`createElement`方便得多，尤其是当我们的应用变得复杂时。

我们可以在花括号中嵌入 JavaScript 表达式。