# 反应提示—片段、列表和事件

> 原文：<https://levelup.gitconnected.com/react-tips-fragments-lists-and-events-c2e48b8a9ac3>

![](img/06f35922e3d3d4e24212411f7f5c816a.png)

[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 是创建前端应用程序最流行的库之一。它还可以用来创建带有 React Native 的移动应用程序。

在本文中，我们将了解如何使用片段呈现一组没有包装元素的元素和组件，在 React 应用程序中呈现列表，以及处理事件。

# 碎片

在 React 中，片段是 React 组件，不呈现 DOM 中的任何内容。我们可以使用片段将一起呈现的组件组合在一起。

例如，我们可以编写以下代码，以便在我们的代码中使用它们:

```
import React from "react";export default function App() {
  return (
    <>
      <h1>foo</h1>
      <h2>bar</h2>
    </>
  );
}
```

在上面的代码中，空标签是片段的开始和结束标签。我们用它来包裹 h1 和 h2 元素。

当我们在浏览器开发人员控制台中检查 DOM 时，我们在 DOM 中看不到任何与片段对应的内容。

我们也可以写出该片段的长形式，如下所示:

```
import React from "react";export default function App() {
  return (
    <React.Fragment>
      <h1>foo</h1>
      <h2>bar</h2>
    </React.Fragment>
  );
}
```

这是一回事，但如果有必要，我们可以向它传递道具。我们不能用速记版本传递道具。

片段有利于对任何东西进行分组。

# 列表和键

我们可以使用数组的`map`方法将数组数据转换成 React 组件。

例如，我们可以使用下面的`map`方法将数组条目映射到我们在屏幕上显示的组件，如下所示:

```
import React from "react";const names = ["jane", "john", "joe"];export default function App() {
  return (
    <>
      {names.map((n, i) => (
        <p key={i}>{n}</p>
      ))}
    </>
  );
}
```

在上面的代码中，我们有一个`names`数组，我们使用`map`方法将它映射到一个 p 元素的列表中。在回调中，我们返回 p 元素，并将`key`属性设置为唯一值。在这种情况下，我们将`key`的值设置为数组索引，这是唯一的。

这也有利于将数据映射到元素，正如我们在下面的代码中所做的那样:

```
import React from "react";const names = ["jane", "john", "joe"];const Name = ({ name }) => <p>{name}</p>;export default function App() {
  return (
    <>
      {names.map((n, i) => (
        <Name key={i} name={n} />
      ))}
    </>
  );
}
```

在上面的代码中，我们创建了`Names`组件，它呈现了我们之前拥有的 p 元素。

我们以同样的方式使用`map`，但是设置了`Name`组件的`key`道具。

我们需要为每个条目将`key`属性设置为一个惟一的值，这样 React 就可以跟踪用`map`迭代的元素。

如果我们跳过这些键，那么当它们发生变化时，就很难做出反应来判断每个元素应该如何更新。

# 事件和事件处理程序

React 和 HTML 事件处理程序是不同的。情况有所不同。例如，在 HTML 中，我们通过编写以下代码创建一个点击处理程序并将其附加到一个元素上:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>App</title>
    <script>
      const onClick = () => {
        alert("hello");
      };
    </script>
  </head>
  <body>
    <button onclick="onClick()">
      foo
    </button>
  </body>
</html>
```

在上面的代码中，我们有`onClick`函数，它在按钮上触发`click`事件时运行，因为我们有:

```
onclick="onClick()"
```

按我们的按钮。

对于 React，我们有类似的东西。然而，情况是不同的，React 事件与常规的 HTML 事件不一样。他们只在 React 的虚拟 DOM 上工作。虚拟 DOM 是 React 对页面上真实 DOM 的表示。

在 React 应用程序中，我们编写了类似下面这样的代码来为按钮附加一个点击处理程序:

```
import React from "react";export default function App() {
  const onClick = () => alert("hello"); return (
    <>
      <button onClick={onClick}>foo</button>
    </>
  );
}
```

在上面的代码中，我们有`onClick`函数，当 React 的虚拟 DOM 中的按钮上的`click`事件被触发时运行该函数。

此外，我们传递了对`onClick`函数的引用，而不是像我们在 HTML 示例中看到的那样在属性内部运行它。我们从不在 props 内部运行函数，我们总是传入值。否则，React 将无限次重新渲染组件。

![](img/c37140f3b9a03a371dfe729b7bd67bc1.png)

照片由[Duan Smetana](https://unsplash.com/@veverkolog?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

片段让我们将元素和组件组合在一起，而不必呈现包装器元素。

可以通过调用数组上的`map`来创建列表。

通过将事件附加到 React 元素，在 React 应用程序中处理事件。处理函数引用被传递到适当的 prop 中，以便在事件被触发时运行该函数。