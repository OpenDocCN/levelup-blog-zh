# 我们应该知道的一些简单的反应技巧

> 原文：<https://levelup.gitconnected.com/some-easy-react-tricks-we-should-know-cdf8c8dc9232>

![](img/12364b83d6ae1cd4f839316f3cac1788.png)

丰西·费尔南德斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 是一个用于创建前端视图的库。它有一个庞大的图书馆生态系统与之合作。此外，我们可以用它来增强现有的应用程序。

在本文中，我们将了解一些使 React 应用程序开发更容易的技巧。

# 使用带有片段的密钥道具

我们可以在列表中使用片段。但是，我们还需要添加`key`道具。

这样，我们不需要在列表条目之外添加容器元素。

如果我们想在列表中使用片段，那么我们不能使用如下简写:

```
import React from "react";export default function App() {
  return (
    <div className="App">
      {[1, 2, 3].map(a => (
        <>{a}</>
      ))}
    </div>
  );
}
```

如果我们这样做，我们会得到错误“警告:列表中的每个孩子都应该有一个唯一的“key”属性。”

因此，我们不能使用 React 片段组件的简写版本。我们必须将它明确地写出来，如下所示:

```
import React from "react";export default function App() {
  return (
    <div className="App">
      {[1, 2, 3].map(a => (
        <React.Fragment key={a}>{a}</React.Fragment>
      ))}
    </div>
  );
}
```

在上面的代码中，我们使用了`React.Fragment`组件代替空标签来添加一个空片段，这样我们就可以添加`key`道具。

那么我们不会在控制台中得到任何错误。

# HTML 元素形式的字符串值

我们可以通过设置一个道具的`Component`属性来用字符串设置标签名。

例如，我们可以编写以下代码来实现这一点:

```
import React from "react";
const Component = ({ children }) => <p>{children}</p>;const Button = ({ Component = "button", ...props }) => <Component {...props} />;
const Paragraph = ({ Component = "p", ...props }) => <Component {...props} />;export default function App() {
  return (
    <div className="App">
      <Button>foo</Button>
      <Paragraph>bar</Paragraph>
    </div>
  );
}
```

在上面的代码中，我们有一个使用`children`道具的`Component`组件。

然后使用`Component`组件，我们通过创建一个具有`Component`属性的函数来创建 2 个新组件，该属性分别被设置为`'button'`和`'p'`，我们按原样传入其他道具。进入`Component`。

我们所做的是用一个字符串来设置`Component`的 HTML 元素名。这只适用于 HTML 元素，不适用于定制组件。

这让我们可以创建一个通用组件，我们可以用它来创建其他组件。

因此，我们可以在`App`中使用它们，它们将分别呈现一个按钮和 p 元素。

# 将函数传递给设置状态的函数

我们可以向从钩子返回的函数传递一个回调来设置依赖于前一个状态的状态。

例如，我们可以这样做:

```
import React, { useState } from "react";export default function App() {
  const [count, setCount] = useState(0);
  return (
    <div className="App">
      <button onClick={() => setCount(count => count + 1)}>increment</button>
      <p>{count}</p>
    </div>
  );
}
```

在上面的代码中，我们有了用回调`count => count + 1`调用`setCount`的`onClick`处理程序。然后，我们根据之前的计数更新`count`状态。该参数保证是以前的值。

因此，我们可以用它返回一个新值来更新它。

这对于在数组中添加和删除项目很有用。例如，我们可以执行以下操作将项目添加到数组中:

```
import React, { useState } from "react";export default function App() {
  const [arr, setArr] = useState([]);
  return (
    <div className="App">
      <button onClick={() => setArr(arr => [...arr, "foo"])}>add</button>
      <div>
        {arr.map((a, i) => (
          <p key={i}>{a}</p>
        ))}
      </div>
    </div>
  );
}
```

在上面的代码中，我们使用从`useState`钩子返回的`setArr`函数向数组末尾添加一个新项，并返回插入了新项的数组。

我们必须这样做，因为我们需要当前数组，这样我们就可以向它添加新的条目。我们必须使用 spread 操作符来代替`push`，因为`push`返回新插入的条目。

要删除一个项目，我们可以使用如下相同的值:

```
import React, { useState } from "react";export default function App() {
  const [arr, setArr] = useState(["foo", "bar", "baz"]);
  return (
    <div className="App">
      <div>
        {arr.map((a, i) => (
          <div key={i}>
            {a}
            <button
              onClick={() => setArr(arr => arr.filter((a, j) => j !== i))}
            >
              remove
            </button>
          </div>
        ))}
      </div>
    </div>
  );
}
```

在上面的代码中，我们使用了`filter`方法来返回一个没有给定索引的条目的新数组，并返回它，这样它将被设置为`arr`的新值。

因此，一旦我们在给定条目上单击 remove，我们就再也看不到该条目了。

![](img/7d504bc3c8c0688c8e42d8949802eab7.png)

[克里斯托夫辊](https://unsplash.com/@krisroller?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 结论

如果我们想在列表项中使用片段，那么我们必须使用带有`key`属性的`React.Fragment`组件，这样 React 才能正确地呈现列表项。

要在 React 中设置 HTML 元素的名称，我们可以将其设置为字符串。

为了基于当前值更新值，我们可以将回调传递给由`useState`返回的函数。