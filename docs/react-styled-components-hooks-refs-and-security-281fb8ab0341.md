# 反应样式组件—挂钩、引用和安全性

> 原文：<https://levelup.gitconnected.com/react-styled-components-hooks-refs-and-security-281fb8ab0341>

![](img/ca53dc73a1d21d520f477e59526c5fb7.png)

照片由[王玮华](https://unsplash.com/@wangooo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 是构建现代交互式前端 web 应用最常用的前端库。它还可以用来构建移动应用程序。

在本文中，我们将研究如何用钩子访问主题，用 refs 引用 DOM 元素，以及处理安全性。

# 使用 useContext React 挂钩访问主题

我们可以使用`useContext` React 钩子和`ThemeContext`来访问带有钩子的主题属性。

例如，我们可以这样做:

```
import React, { useContext } from "react";
import { ThemeProvider, ThemeContext } from "styled-components";const baseTheme = {
  color: "green",
  backgroundColor: "white"
};const Foo = ({ children }) => {
  const themeContext = useContext(ThemeContext);
  return <div style={themeContext}>{children}</div>;
};export default function App() {
  return (
    <div>
      <ThemeProvider theme={baseTheme}>
        <Foo>foo</Foo>
      </ThemeProvider>
    </div>
  );
}
```

在上面的代码中，我们使用传入了`ThemeContext`的`useContext`来访问主题。`themeContext`与`baseTheme`具有完全相同的属性和值。因此，我们可以直接将`themeContext`作为`style`属性的值传入。

在`App`中，我们用设置为`baseTheme`的`theme`包装`ThemeProvider`，这样我们就可以访问`baseTheme`中的主题属性。

因此，我们将看到“foo”显示为绿色。

# 主题道具

我们可以用`theme`道具将主题传递给子组件。这适用于用`styled-components`创建的组件。例如，我们可以这样做:

```
import React from "react";
import styled, { ThemeProvider } from "styled-components";const Button = styled.button`
  font-size: 12px;
  margin: 5px;
  padding: 6px;
  border-radius: 3px;color: ${props => props.theme.main};
  border: 2px solid ${props => props.theme.main};
`;const theme = {
  main: "green"
};export default function App() {
  return (
    <div>
      <ThemeProvider theme={theme}>
        <Button theme={{ main: "blue" }}>Foo</Button>
        <ThemeProvider theme={theme}>
          <div>
            <Button>Baz</Button>
            <Button theme={{ main: "red" }}>Bar</Button>
          </div>
        </ThemeProvider>
      </ThemeProvider>
    </div>
  );
}
```

在上面的代码中，我们创建了一个具有各种样式的按钮。颜色是从主题传入的，我们用`prop.theme.main`属性访问它。

然后在`App`中，我们使用`theme`道具为一些按钮传入不同的颜色。Foo 按钮是蓝色的，Bar 按钮是红色的。

它们将覆盖在`theme`中指定的原始颜色值，即`green`。

因此，我们会看到按钮有红色、绿色或蓝色的文本边框和颜色。Foo 是蓝色的，Baz 是绿色的，Bar 是红色的，

# 参考文献

我们可以将引用传递给样式化组件，以访问给定样式化组件的 DOM 属性和方法。

例如，我们可以这样做:

```
import React from "react";
import styled from "styled-components";const Input = styled.input`
  padding: 0.5em;
  margin: 0.5em;
  background: greenyellow;
  border: none;
  border-radius: 3px;
`;export default function App() {
  const inputRef = React.useRef();
  React.useEffect(() => {
    inputRef.current.focus();
  }, []);
  return (
    <div>
      <Input ref={inputRef} />
    </div>
  );
}
```

在上面的代码中，我们创建了一个名为`Input`的样式化输入。然后我们向`Input`传递一个用`useRef`钩子创建的 ref。

然后在`useEffect`回调中，我们调用`inputRef.current.focus();`来聚焦元素。`useEffect`的第二个参数中的空数组表示我们在`App`第一次加载时加载回调。

因此，当我们第一次加载页面时，`Input`将被关注。

![](img/adae84ddb8deff3703d9c370d258f60b.png)

[Icons8 团队](https://unsplash.com/@icons8?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 安全性

当用`styled-components`创建样式化组件时，安全性是一个需要关注的问题，因为如果我们通过道具、主题或其他方式传递文本，所有的文本都会被插入到我们的样式化组件中。

因此，我们应该净化任何传入的输入。例如，以下情况是不好的:

```
import React from "react";
import styled from "styled-components";const userInput = "/steal-money";const ArbitraryComponent = styled.div`
  background: url(${userInput});
`;export default function App() {
  return (
    <div>
      <ArbitraryComponent />
    </div>
  );
}
```

因为我们在代码中加载了 URL，这并不好。所以要加一些检查，避免这种情况的发生。

# 结论

我们可以用`useContext`钩子访问主题。此外，我们可以传入`theme`属性来覆盖基本主题中的值。

`styled-components`将把 refs 转发给我们的 HTML 元素，这样我们就可以通过 refs 获得用包创建的样式化组件中的 HTML 元素。

最后，当我们在样式代码中插入字符串时应该小心，以避免任意代码执行攻击。