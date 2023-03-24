# 使用 React 上下文向组件库注入依赖关系

> 原文：<https://levelup.gitconnected.com/using-react-context-to-inject-dependencies-to-your-component-library-9e0c4c1342da>

## 向 React 库添加配置的简单技术。

![](img/c55c60597374c96018d1753219a69907.png)

[丹尼尔](https://unsplash.com/@setbydaniel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我认为你正在编写一个库，它导出了一组可重用的组件。其中一个组件是一个按钮，需要链接到应用程序的另一部分。您正在使用`react-router`的`Link`组件或类似的东西来创建这些链接。但是你不希望你的可重用组件库依赖于`react-router`——事实上，UI 库不应该关心路由，对吗？

所以你需要一种方式对你的`Button`组件说，“嘿，我需要你使用这个基础组件来渲染，这样你就可以和我的应用程序的其余部分相处了。”

一种选择是向该组件传递一个属性来告诉按钮应该如何呈现，例如:

```
<Button {...props} renderComponent={Link} />
```

然后`Button`组件代码会是这样的:

```
// Button.js
const Button = ({ renderComponent, ...otherProps }) => {
  const LinkComponent = renderComponent;
  return <LinkComponent {...otherProps} />;
};
```

不过，这种方法有一些缺点。

最明显的一点是，您需要为应用程序需要使用的每个`Button`实例指定渲染组件。这很麻烦，但也没那么可怕。

但是，如果您的组件库也包含一个名为`NavMenu`的组件，该组件由多个`Button`组成，每个组件都有一个到应用程序不同部分的链接，该怎么办呢？您还需要在您的`NavMenu`组件中实现`renderComponent` prop，这样它就可以将它传递给底层的`Button`组件。

```
// NavMenu.js
const NavMenu = ({ buttonRenderComponent, urlA, urlB }) => (<>
  <Button renderComponent={buttonRenderComponent} href={urlA} />
  <Button renderComponent={buttonRenderComponent} href={urlB} />
</>);
```

这很糟糕，也显示了我们设计中的一个弱点，`Button`实现通过`NavMenu`泄漏。

# 上下文 API 来拯救

React Context API 是一个很好的工具，可以最大限度地减少组件树中的道具传递。当然，这种权力不应该被滥用，但是，对于我们目前的情况来说，它确实很方便。

实施此解决方案的步骤非常简单:

1.  你的库需要公开一个`Context`，定义初始值，
2.  您的`Button`组件将需要使用该上下文，并从那里获取指定的组件来呈现
3.  库用户需要导入库上下文，并在应用程序的根目录中提供值。

在 JavaScript 中:

```
**// library/context.js**
import { createContext } from 'react';const LibraryContext = createContext({
  LinkComponent: undefined
});export default LibraryContext;**// library/button.js**
import React, { useContext } from 'react';
import LibraryContext from './context';const DefaultLinkComponent = ({ to, ...props }) => (
  <a href={to} {...props} />
);const Button = (props) => {
  const {
    LinkComponent = DefaultLinkComponent
  } = useContext(LibraryContext); return <LinkComponent {...props} />;
};**// app.js**
import { BrowserRouter, Link } from 'react-router-dom';
import { Context, Button } from 'my-library';render(
  <Context.Provider value={{ LinkComponent: Link }}>
    <BrowserRouter>
      <Button to="/path" />
    </BrowserRouter>
  </Context.Provider>
);
```

很简单，对吧？现在，您有了配置组件库的方法，更重要的是，您已经成功地扩展了组件，而没有向工具箱添加外部依赖项。

# 附加用例:跨平台渲染

也许这种方法还可以帮助创建一个跨平台的组件库，它可以根据上下文值使用一组不同的“原语”进行渲染。

我还没有研究过，但是我觉得可能可以写一个只依赖于`react`的库，然后它使用`react-dom`、`react-native`甚至一个 [Canvas/WebGL 渲染后端](https://reactpixi.org/)，通过上下文注入。

如果有任何人已经这样做了，请给我指出这个项目！