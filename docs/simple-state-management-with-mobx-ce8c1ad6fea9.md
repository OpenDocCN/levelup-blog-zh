# 使用 Mobx 进行简单的状态管理

> 原文：<https://levelup.gitconnected.com/simple-state-management-with-mobx-ce8c1ad6fea9>

![](img/5102b62156f7e47991d955d23a3ac7bc.png)

由 [Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Mobx 是 React 应用程序状态管理的一个更简单的替代方案。它的工作原理是创建一个存储，然后观察它的变化，我们通过直接改变值来更新存储。

在本文中，我们将了解如何使用 Mobx 和 React。

# 装置

我们安装`mobx`和`mobx-react`库来创建我们的 Mobx 存储，然后将它连接到我们的 React 组件。

为此，我们运行:

```
npm install mobx mobx-react
```

Mobx 4 . x 或更早版本支持所有现代浏览器。从版本 5 开始，Mobx 使用代理进行状态更新，因此不支持 Internet Explorer。它还可以与 React Native 和 Node.js 一起使用

# 基本用法

为了开始使用它，我们创建一个存储来保存状态。然后，我们可以将存储传递到组件中。

例如，我们可以使用 Mobx 创建一个商店，然后将该商店注入 React 组件，如下所示:

```
import React from "react";
import ReactDOM from "react-dom";
import { observable } from "mobx";
import { observer } from "mobx-react";class Count {
  @observable count = 0;
}const App = observer(({ store }) => {
  return (
    <div className="App">
      <button
        onClick={() => {
          store.count++;
        }}
      >
        Increment
      </button>
      <button
        onClick={() => {
          store.count--;
        }}
      >
        Decrement
      </button>
      <p>{store.count}</p>
    </div>
  );
});const store = new Count();
const rootElement = document.getElementById("root");
ReactDOM.render(<App store={store} />, rootElement);
```

在上面的代码中，我们通过定义`Count`类创建了一个商店:

```
class Count {
  @observable count = 0;
}
```

然后我们创建一个`Count`类的新实例，这样我们就可以通过编写以下代码将它作为一个道具传递给我们的组件:

```
const store = new Count();
```

并且:

```
ReactDOM.render(<App store={store} />, rootElement);
```

然后，为了定义我们的`App`函数组件，我们在`observer`函数周围包装了一个函数，以便观察来自商店的最新值。

这意味着一旦我们传入了`store`，我们将自动获得最新的值，并且当我们更改之前有`@observable`装饰的商店属性时，该值将被传播到商店。

因此，在我们的`onClick`道具中，我们可以传入一个直接改变`store.count`的函数来更新值，这些值将立即反映在我们添加到`p`元素中的`store.count`中。

我们也可以使用带有 React 类组件的`observer`装饰器，如下所示:

```
import React from "react";
import ReactDOM from "react-dom";
import { observable, computed, autorun } from "mobx";
import { observer } from "mobx-react";class Person {
  @observable firstName = "Jane";
  @observable lastName = "Smith";
  @computed
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}@observer
class App extends React.Component {
  render() {
    const { store } = this.props;
    return (
      <div className="App">
        <p>{store.fullName}</p>
      </div>
    );
  }
}const store = new Person();
const rootElement = document.getElementById("root");
ReactDOM.render(<App store={store} />, rootElement);
```

它的功能与带有反应功能组件的`observer`功能相同。

# 计算值和 Getters

我们可以在 store 类中创建一个函数，返回一个从其他可观察值计算出来的值。

为此，我们可以在我们的方法之前使用`@computed`装饰器。我们可以定义一个并如下使用它:

```
import React from "react";
import ReactDOM from "react-dom";
import { observable, computed } from "mobx";
import { observer } from "mobx-react";class Person {
  @observable firstName = "Jane";
  @observable lastName = "Smith";
  @computed
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}const App = observer(({ store }) => {
  return (
    <div className="App">
      <p>{store.fullName}</p>
    </div>
  );
});const store = new Person();
const rootElement = document.getElementById("root");
ReactDOM.render(<App store={store} />, rootElement);
```

在上面的代码中，我们在`Person`类中有一个方法`fullName`，它是一个 getter 并返回``${this.firstName} ${this.lastName}``，这是我们定义的两个可观察属性的组合。

然后我们可以像在`App`组件中一样，在`store`道具中获得`fullname`属性。

![](img/0b1cf15628b09423c96a0bb44d88c7d4.png)

[Teddy sterblom](https://unsplash.com/@teddyosterblom?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 自定义反应

自定义反应是通过使用`autorun`、`reaction`或`when`函数创建的，方法是向它们传递一个回调。反应是用来产生副作用的。

例如，我们可以按如下方式创建它们:

```
autorun(() => {
  console.log(store.fullName);
});
```

在上面的代码中，我们创建了一个在运行代码时自动运行的反应。

我们从上面记录了`store.fullName`的值，我们可以在我们的组件中使用它，如下所示:

```
const App = observer(({ store }) => {
  autorun(() => {
    console.log(store.fullName);
  }); return (
    <div className="App">
      <p>{store.fullName}</p>
    </div>
  );
});
```

# 结论

我们可以使用 Mobx 创建一个简单的存储来存储我们的值。它附带了 decorators 和函数，让我们可以观察 React 组件的值，并直接操作存储值。

使用`observable`装饰器，我们可以观察来自商店的值，然后直接保存更改的值。

`computed`装饰器让我们从组件的 store 类中的 getters 获取函数。

为了提交副作用，我们可以添加自定义反应。