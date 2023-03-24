# 反应提示—基本挂钩

> 原文：<https://levelup.gitconnected.com/react-tips-basic-hooks-140293672b28>

![](img/c5a4050e61eb149d374f343d50e5e322.png)

[瑞恩·华莱士](https://unsplash.com/@accrualbowtie?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

React 是创建前端应用程序最流行的库之一。它还可以用来创建带有 React Native 的移动应用程序。

在本文中，我们将看看 React 内置的一些基本挂钩，它们使我们的功能组件变得智能。

# 状态和使用状态

我们可以使用`useState`钩子来存储应用程序的状态。因此，它是智能反应功能组件的基本构建块。

为了使用它，我们编写以下代码:

```
import React from "react";export default function App() {
  const [count, setCount] = React.useState(0); return (
    <>
      <button onClick={() => setCount(count => count - 1)}>decrement</button>
      <p>{count}</p>
    </>
  );
}
```

在上面的代码中，我们将`count`状态作为由`useState`钩子返回的数组的第一个元素返回。

然后我们定义了一个`onClick`处理程序，它调用作为由`useState`返回的数组中的第二个元素返回的`setCount`函数。

在那里，我们通过返回现有的`count`值减 1 来更新`count`。

这让我们可以基于状态的前一个值来修改它的值。我们也可以直接传入我们想要设置的任何东西，如果它不依赖于状态的前一个值的话。

传入回调将保证更新是基于先前的状态值完成的。

例如，我们可以将一个值直接传入状态更改函数，如下所示:

```
import React from "react";export default function App() {
  const [text, setText] = React.useState("foo"); return (
    <>
      <button onClick={() => setText("foo")}>foo</button>
      <button onClick={() => setText("bar")}>bar</button>
      <button onClick={() => setText("baz")}>baz</button>
      <p>{text}</p>
    </>
  );
}
```

在上面的代码中，我们有 3 个按钮，这些按钮具有调用`setText`来设置`text`状态值的点击处理程序。

由于我们设置的值不依赖于之前的值，我们可以通过传入我们想要设置`text`的值来直接设置它。

从前面的例子可以看出，状态改变函数可以多次使用。

它可以接受任何类型的值，包括原始值和对象。

# 副作用和使用效果

React `useEffect`钩子是另一个我们不能忽视的重要钩子。

它用于提交副作用，比如从 API 异步更新数据或使用 DOM。

副作用是能够以不可预知的方式改变我们身体组成部分的行为。

它接受在每次渲染时运行的回调函数。为了将回调限制为仅在指定值发生更改时运行，我们传入了第二个参数，该参数包含我们希望监视的值的数组，并在值发生更改时运行回调。

如果我们传入一个空数组作为第二个参数，那么回调只在第一次渲染时运行。

例如，我们可以如下使用它:

```
import React, { useEffect } from "react";export default function App() {
  const [name, setName] = React.useState("");
  const [data, setData] = React.useState({}); const getData = async () => {
    const res = await fetch(`https://api.agify.io/?name=${name}`);
    const d = await res.json();
    setData(d);
  }; useEffect(() => {
    getData();
  }, [name]); return (
    <>
      <input value={name} onChange={e => setName(e.target.value)} />
      <p>
        {data.name} {data.age}
      </p>
    </>
  );
}
```

在上面的代码中，我们有一个输入，当我们在其中键入一些内容时，它会改变`name`状态的值。

然后用`useEffect`钩子，我们观察`name`的变化，这样当`name`值变化时`getData`被调用。

在`getData`函数中，我们调用`setData`来设置数据并显示从 API 调用中获得的数据。

我们应该总是传入一个数组作为第二个参数，这样回调就不会在每次渲染时意外运行。

如果我们需要进行清理，我们应该在`useEffect`回调函数中返回一个函数，并在需要时运行回调函数中的任何清理代码。

我们可以如下使用添加清理代码:

```
import React, { useEffect } from "react";export default function App() {
  const [mousePosition, setMousePosition] = React.useState({});
  const { x, y } = mousePosition; const handleMouseMove = event => {
    const { pageX, pageY } = event;
    setMousePosition({
      x: pageX,
      y: pageY
    });
  }; useEffect(() => {
    window.addEventListener("mousemove", handleMouseMove); return () => window.removeEventListener("mousemove", handleMouseMove);
  }); return (
    <>
      <p>
        ({x}, {y})
      </p>
    </>
  );
}
```

在上面的代码中，我们为名为`handleMousemove`的`mousemove`事件添加了一个事件处理程序，它获取鼠标位置并调用`setMousePosition` 来设置`mousePosition`状态。

然后，我们通过书写来观察鼠标位置:

```
window.addEventListener("mousemove", handleMouseMove);
```

在`useEffect`回调中。在它下面，我们有:

```
return () => window.removeEventListener("mousemove", handleMouseMove);
```

当不再呈现`App`组件时，删除`mousemove`事件监听器。

![](img/1834ba296a4c4bf7a9f8fa4ce1280966.png)

多鲁克·耶梅尼西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

`useState`和`useEffect`钩子是 React 标准库中最有用和最基本的两个钩子。

它们分别让我们更新状态和提交副作用。