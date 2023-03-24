# React 提示—数据流和纯组件

> 原文：<https://levelup.gitconnected.com/react-tips-data-flow-and-pure-components-5995274eeb54>

![](img/b524777d1d9cbf95bb50d80039bf54e0.png)

照片由 [Ariel Schmunck](https://unsplash.com/@chilipeper?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 是最常用的前端库，用于构建现代的交互式前端 web 应用程序。它还可以用来构建移动应用程序。

在本文中，我们将研究如何在组件之间共享数据，以及如何用纯组件加速渲染。

# 有流量的数据流

Flux architecture 是一种在 JavaScript 应用程序中创建数据层的方法。它的工作原理是在一个集中的数据存储中添加数据层。对存储的更新是通过将更新存储状态的动作分派给存储来完成的。

任何需要数据的组件都可以订阅数据存储的最新更新，以获得相关的状态。

这是组织我们的数据层结构的好方法，因为它消除了在组件之间直接共享数据的需要。所有数据都在一个地方。

这减少了在组件之间共享数据所引起的混乱，特别是当我们需要通过在一系列相关组件之间传递数据来在不相关的组件之间传递数据时。

实现 Flux 架构最常见的库是 Redux。我们可以通过编写以下代码来使用它:

`index.js`:

```
import React from "react";
import ReactDOM from "react-dom";
import { createStore } from "redux";
import App from "./App";
import { Provider } from "react-redux";const counter = (state = 0, action) => {
  switch (action.type) {
    case "INCREMENT":
      return state + 1;
    default:
      return state;
  }
};const store = createStore(counter);const rootElement = document.getElementById("root");
ReactDOM.render(
  <React.StrictMode>
    <Provider store={store}>
      <App />
    </Provider>
  </React.StrictMode>,
  rootElement
);
```

`App.js`:

```
import React from "react";
import { useSelector, useDispatch } from "react-redux";export default function App() {
  const count = useSelector(state => state);
  const dispatch = useDispatch(); return (
    <>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>Increment</button>
      <p>{count}</p>
    </>
  );
}
```

在上面的代码中，我们使用了 React-Redux API 的 React hooks 版本。首先，我们用以下内容创建了商店:

```
const counter = (state = 0, action) => {
  switch (action.type) {
    case "INCREMENT":
      return state + 1;
    default:
      return state;
  }
};const store = createStore(counter);
```

然后，我们将`Provider`组件包装在`index.js`中我们应用程序的入口点周围。我们传入`store`对象，这样我们就可以访问它。

通过首先创建`counter`减速器来创建`store`，减速器更新`state`。然后我们将`counter`减速器传递给`createStore`来创建商店。

然后在`App`中，我们通过使用`useDispatch`钩子来访问存储，这样我们就可以用返回的函数来分派动作。然后我们使用`useSelector`钩子通过回调从我们的存储中返回状态。

一旦我们这样做了，当我们点击增量按钮时，我们调用`dispatch`。在`dispatch`函数调用中，我们用`'INCREMENT'`动作传入了`type`属性，这意味着`counter`减速器中的`'INCREMENT'`动作将被完成。

然后，当我们单击增量时，`count`将会更新，因为每次调度`'INCREMENT'`动作时，我们都会用`state + 1`更新存储。

# 纯成分

纯组件是实现`shouldComponentUpdate`方法的 React 组件。

实现`shouldComponentUpdate`的总是基于类的组件。我们还可以通过返回从传入的属性中派生的结果来创建不实现该功能的组件。

一般来说，如果一个组件的返回值仅仅由它的输入值决定，那么这个组件就是纯的。

我们可以创建一个基于类别的组件，并按如下方式使用它:

```
import React from "react";class Pecentage extends React.PureComponent {
  render() {
    const { score, total } = this.props;
    return (
      <div>
        <span>{Math.round((score / total) * 100)}%</span>
      </div>
    );
  }
}export default function App() {
  return <Pecentage score={70} total={100} />;
}
```

在上面的代码中，我们有`Percentage`组件，它实现了`React.PureComponent`。

它需要两个道具，`score`和`total`，我们返回从`score`除以`total`得到的百分点。

`shouldComponentUpdate`是自动包含的，它对数据进行简单的比较，所以我们不需要自己写一个来做比较，因为两个道具都是数字。因此，只有当属性的值改变时，React 才会重新渲染组件。

另一方面，如果道具是对象，那么我们需要自己实现方法并进行检查。

给定相同的输入，它返回相同的输出，所以它是一个纯组件。

此外，我们可以如下实现纯函数组件:

```
import React from "react";const Pecentage = ({ score, total }) => {
  return (
    <div>
      <span>{Math.round((score / total) * 100)}%</span>
    </div>
  );
};
```

我们只是在需要的时候传入道具，给定相同的输入，它也会返回相同的输出，所以这是一个纯组件。

![](img/8e8144385ac7db2109e2f5ea65466bcf.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Jernej Graj](https://unsplash.com/@jernejgraj?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

如果我们的应用程序有多个组件需要共享数据，那么我们需要 Flux 架构。要实现它，最好的方法是使用像 Redux 这样流行的现有库。

纯组件通过 React 进行优化，以加快渲染速度。他们只在道具改变时才渲染。变化检测是通过浅层比较完成的。