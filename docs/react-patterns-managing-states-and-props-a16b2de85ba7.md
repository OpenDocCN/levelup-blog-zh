# 反应模式—管理状态和道具

> 原文：<https://levelup.gitconnected.com/react-patterns-managing-states-and-props-a16b2de85ba7>

![](img/c1d4247041c88255b36e39bf1b42bd05.png)

照片由[宝琳·巴斯维尔](https://unsplash.com/@paubvl?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 是一个用于创建 web 应用程序和移动应用程序的流行库。

在这篇文章中，我们将看看如何管理状态和道具。

# 管理状态

为了让我们的 React 应用程序有用，我们必须更新状态。为此，我们必须存储一个内部或共享状态。我们可以在没有外部库的情况下编写有状态 React 应用程序。我们只需要正确地使用它们。

# 国家如何运作

要设置状态，我们可以使用`useState`钩子。

我们可以通过书写来使用它:

```
import React, { useState, useEffect } from "react";export default function App() {
  const [msg, setMsg] = useState(""); useEffect(() => {
    setMsg("foo");
  }, []); return <p>{msg}</p>;
}
```

我们有返回`msg`状态的`useState`钩子和设置`msg`状态的`setMsg`函数。

`useEffect`用于设置组件加载时的`msg`。

第二个参数中的空数组表示回调在第一次加载时运行。然后我们看到当我们渲染 p 元素时显示的`msg`值。

# 异步的

`useState`不同步。为了确保我们可以一个接一个地设置状态，我们可以观察前一个状态的值，然后更新下一个状态:

```
import React, { useState, useEffect } from "react";export default function App() {
  const [msg, setMsg] = useState("");
  const [bar, setBar] = useState(""); useEffect(() => {
    setMsg("foo");
  }, []); useEffect(() => {
    setBar("bar");
  }, [bar]); return (
    <p>
      {msg} {bar}
    </p>
  );
}
```

我们通过将值传递给第二个参数中的数组来观察`bar`的值。然后我们可以设置值。

# 反应伐木工人

我们可以使用 React Lumberjack 库来跟踪状态值，这样我们就可以前后移动它们。

这是一个实验性的库，已经被 React Developer tools 继承，它具有相同的特性。

# 计算状态

我们应该创建一个函数来计算从其他状态导出的状态。

例如，如果我们有两个状态，那么我们可以将它们组合如下:

```
import React, { useState, useEffect } from "react";export default function App() {
  const [taxRate] = useState(0.1);
  const [price] = useState(1);
  const [total, setTotal] = useState(1); useEffect(() => {
    setTotal(price * (1 + taxRate));
  }, [taxRate, price]); return <p>{total}</p>;
}
```

我们有`taxRate`和`price`状态。

然后，我们使用`useEffect`来观察这两个值，并通过将`taxRate`和`price`组合在一起，调用`setTotal`来更新`total`。

# 道具类型

为了使组件易于重用，我们必须以一种清晰的方式定义它们的接口。为此，有一个`prop-types`包让我们验证道具。

例如，我们可以写:

```
import React from "react";
import PropTypes from "prop-types";const Button = ({ text }) => {
  return <button>{text}</button>;
};Button.propTypes = {
  text: PropTypes.string
};
```

然后我们可以将`propTypes`添加到我们的按钮中，并用`PropTypes.string`验证道具的类型。

为了确保它是必需的，我们可以这样写:

```
import React from "react";
import PropTypes from "prop-types";const Button = ({ text }) => {
  return <button>{text}</button>;
};Button.propTypes = {
  text: PropTypes.string.isRequired
};
```

我们只是在道具类型后面加上`isRequired`。如果我们想要自定义验证，`prop-types`也提供了。

我们可以使用`shape`属性来验证对象的形状:

```
import React from "react";
import PropTypes from "prop-types";const Button = ({ values: { text } }) => {
  return <button>{text}</button>;
};Button.propTypes = {
  values: PropTypes.shape({
    text: PropTypes.string.isRequired
  })
};
```

`Button`带一个`values`道具，道具是一个内部带有`text`属性的对象。我们也可以把它设置成一个函数。

例如，我们可以写:

```
import React from "react";
import PropTypes from "prop-types";const Person = ({ person: { age } }) => {
  return <p>{age}</p>;
};Person.propTypes = {
  person: PropTypes.shape({
    age: (props, propName) => {
      if (!(props[propName] > 0 && props[propName] < 200)) {
        return new Error(`${propName} must be between 1 and 199`);
      }
      return null;
    }
  })
};
```

现在，如果我们把一个 1 到 199 之间的非数字转换成`Person`，我们会得到一个错误。

![](img/8d56a1877eef7a3ab56d75cd85968a1d.png)

Krzysztof Niewolny 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

为了使组件有用，我们必须管理状态和道具。

我们可以使用`useState`和`useEffects`来管理状态。

我们从组件的第一个参数中得到道具。