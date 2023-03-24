# React 上下文简介

> 原文：<https://levelup.gitconnected.com/introduction-to-react-context-f3009e98f34e>

![](img/da7152bd7bb2b036200c59bbbfb537b5.png)

照片由[费德里科·贝卡里](https://unsplash.com/@federize)拍摄

在本帖中，我们将讨论 React 16 中引入的`Context` API，以及如何使用它们。

# 什么是语境？

查看来自 [react docs](https://reactjs.org/docs/context.html#reactcreatecontext) 的定义，它将它描述为一种通过组件树传递数据的方式，而不必在每一层手动向下传递属性。

换句话说，上下文是 react 应用程序的通用数据(或者根据 redux 定义，是 react 应用程序的集中存储)。您在上下文中设置的状态在 react 树中的任何地方都可以访问。

# 什么时候应该使用上下文？

上下文的思想是解决属性钻取的问题，避免在组件树中的许多组件中传递属性，因为最底层的子组件需要一些中间组件没有使用的数据。

在我看来，如果你对不得不在组件树中传递超过 3 层的属性而感到烦恼，并且最终有太多的属性没有被中间的组件使用，那么就应该使用上下文。因此，这是上下文通过在消费者中提供状态而发挥最大作用的地方。

然而，在调试应用程序时，这可能会有一些影响，因为不像 props 那样，您知道数据来自哪里以及它是如何被更改的，您不会从上下文中一眼看出这一点，因为您是从组件中获取显式性的。

让我们通过一个例子来看看上下文是如何工作的。

# 应用程序接口

# React.createContext 上下文

首先，我们创建一个新的上下文对象来描述数据的外观(我们也可以在状态中存储函数)。
无论我们在上下文对象中放入什么，都是我们想要在消费者组件中使用的

```
//NameContext.js
import React from 'react';const NameContext = React.createContext({
  name: '',
  handleNameChange() {},
});export const Provider = NameContext.Provider;
export const Consumer = NameContext.Consumer;
```

`createContext`将为我们提供两个组件`Provider`和`Consumer`，我们可以在我们的应用程序中使用它们。

# 语境。供应者

在我们创建了上下文之后，我们就可以使用提供者了，但是在此之前，我们先来看看什么是提供者和消费者组件，以及它们是如何与上下文一起工作的。

想象一下，你如何使用火车从 A 点通勤到 B 点，你去车站入口并在通过大门(提供商)时提供车票(上下文)以便登上火车。一旦你到达目的地，你把你的票(上下文)交给出口(消费者)去消费。

换句话说，我们包装了提供者组件，并将上下文作为值 prop 传递给我们希望使用消费者组件来消费上下文的区域

```
//App.js
import React from 'react';
import { render } from 'react-dom';
import { Provider } from './NameContext';
import Child from './Child';class App extends React.Component {
  constructor(props) {
    super(props);
    this.handleNamechange = this.handleNamechange.bind(this);
    this.state = {
      name: 'Malik',
      handleNameChange: this.handleNamechange,
    };
  }
  handleNamechange(event) {
    let nameValue = event.target.value;
    this.setState(function() {
      return {
        name: nameValue,
      };
    });
  }
  render() {
    return (
      <Provider value={this.state}>
        <h1>Hello</h1>
        <Child />
      </Provider>
    );
  }
}
```

请注意我们是如何将从 NameContext 导入的`Provider component`包装在`Child component`周围，并使用应用程序状态作为其值传递给`value prop`，这样 Provider 中的任何内容都可以使用`Consumer component`访问上下文。

# 语境。消费者

在我们将 Provider 组件包装在我们想要使用上下文的应用程序部分之后。消费者组件将使用功能组件订阅上下文更改。

换句话说，为了使用上下文，组件将需要一个函数作为子组件，该子组件将获得当前上下文值作为参数。该函数将返回一个 React 节点，该节点将基于上下文值进行呈现。

```
//Child.js
import React from 'react';
import { Consumer } from './NameContext';
import GrandChild from './GrandChild';class Child extends React.Component {
  render() {
    return (
      <Consumer>
        {function(context) {
          return (
            <div>
              <h2>{context.name}</h2>
              <input
                onChange={context.handleNameChange}
                value={context.name}
                placeholder="Name"
              />
              <GrandChild />
            </div>
          );
        }}
      </Consumer>
    );
  }
}export default Child;
```

我们已经讨论了如何在渲染函数中使用上下文，如果你想在渲染之外的其他函数中使用上下文来执行你的逻辑呢？我们可以用消费者组件包装导出的组件，并将上下文作为道具传递给组件，以便其他功能可以访问它

```
//GrandChild.js
import React from 'react';
import { Consumer } from './NameContext';class GrandChild extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick() {
    console.log(this.props);
  }
  render() {
    return (
      <div>
        <h3>Gabroun</h3>
        <button onClick={this.handleClick}>Submit</button>
      </div>
    );
  }
}export default function GrandChildWithContext() {
  return (
    <Consumer>
      {function(context) {
        return <GrandChild data={context} />;
      }}
    </Consumer>
  );
}
```

通过将一个提供者包装在另一个提供者周围来访问上下文，可以有多种上下文。

# 最后

当 props drilling 变得难以处理时，Context API 是一种在树中的组件之间共享应用程序状态的好方法，这是一种有助于解决该问题的好工具。