# 与 React 上下文 API 共享状态

> 原文：<https://levelup.gitconnected.com/sharing-state-with-the-react-context-api-f24fcb9c7ce2>

![](img/59c3cbdd197b9249a1eba49505c44785.png)

照片由[普里西拉·杜·普里兹](https://unsplash.com/@priscilladupreez?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

React 是一个用于创建前端视图的库。它有一个庞大的图书馆生态系统与之合作。此外，我们可以用它来增强现有的应用程序。

React 组件只能通过 props 将数据从父组件传递到子组件。通过允许具有其他关系的组件共享数据，上下文 API 对此进行了补充。

在本文中，我们将研究如何使用它在组件之间共享数据。

# 何时使用上下文

我们应该使用上下文在 React 组件之间共享数据。但是，应该谨慎使用它，因为它会在组件之间产生紧密耦合。

要在一个简单的应用程序中使用它，我们可以编写以下代码:

```
const ColorContext = React.createContext("green");class Button extends React.Component {
  render() {
    return (
      <div>
        <ColorContext.Consumer>
          {value => (
            <button style={{ color: value }}>{this.props.children}</button>
          )}
        </ColorContext.Consumer>
      </div>
    );
  }
}class App extends React.Component {
  render() {
    return (
      <div>
        <ColorContext.Provider value="blue">
          <Button>Click Me</Button>
        </ColorContext.Provider>
      </div>
    );
  }
}
```

在上面的代码中，我们通过编写以下内容创建了一个共享数据的上下文:

```
const ColorContext = React.createContext("green");
```

`createContext`接受一个默认值作为参数，这里我们传入了`'green'`。

然后在`App`组件中，我们将`ColorContext.Provider`组件的`value`属性设置为我们想要共享的值。

在上面的例子中，它将是`'blue'`。我们将它包装在我们想要共享数据的组件周围，以便我们可以从该组件访问值。

在本例中，我们创建了一个新的`Button`组件，它包含了`ColorContext.Consumer`组件。在其中，我们可以从插入到`ColorContext.Consumer`组件中的函数的`value`参数中获得上下文提供者共享的值。

`value`应该设置为`'blue'`，因为这是我们设置为`value`属性的值。

在我们传递给消费者的函数中，我们返回了一个带有 style prop 的`buttom`元素，并将`color`样式设置为`value`，也就是`'blue'`。

# 语境的替代

如果我们想将数据传递到一个深度嵌套的组件中，我们可以将整个组件传递到我们想要的位置。这样，我们就不必担心将道具传递到多个级别来传递一些只有深度嵌套的组件才需要的东西。

例如，如果我们想将颜色属性传递给`Button`组件，它包含在一个`ButtonBar`中。我们可以这样做:

```
class Button extends React.Component {
  render() {
    return (
      <button style={{ color: this.props.color }}>{this.props.children}</button>
    );
  }
}class ButtonBar extends React.Component {
  render() {
    return this.props.buttons;
  }
}class App extends React.Component {
  render() {
    const buttons = [
      <Button color="blue">Click Me</Button>,
      <Button color="green">Click Me 2</Button>
    ];
    return <ButtonBar buttons={buttons} />;
  }
}
```

在`App`组件中，我们有`buttons`数组中的`Button`组件。然后我们将整个数组直接传递给`ButtonBar`组件。

然后`ButtonBar`只是返回我们传入的，也就是`this.props.buttons`。

这也意味着高阶元件更加复杂，因此它可能并不适用于所有情况。

![](img/eca12dd182d1776dbb0eb4e3be299df1.png)

照片由[米米·蒂安](https://unsplash.com/@mimithian?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 从嵌套组件更新上下文

我们可以将函数传递给传入`createContext`的对象，这样我们就可以在拥有上下文消费者组件的组件内部调用它们。

例如，我们可以这样写:

```
const colorObj = {
  color: "green",
  toggleColor: () => {}
};const ColorContext = React.createContext(colorObj);
class Button extends React.Component {
  render() {
    return (
      <div>
        <ColorContext.Consumer>
          {({ color, toggleColor }) => (
            <button onClick={toggleColor} style={{ color }}>
              {this.props.children}
            </button>
          )}
        </ColorContext.Consumer>
      </div>
    );
  }
}class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      color: "blue",
      toggleColor: () => {
        this.setState(state => ({
          color: state.color === "green" ? "blue" : "green"
        }));
      }
    };
  } render() {
    return (
      <div>
        <ColorContext.Provider value={this.state}>
          <Button>Click Me</Button>
        </ColorContext.Provider>
      </div>
    );
  }
}
```

上面的代码从定义`colorObj`对象开始，该对象作为`ColorContext`的默认值传递给`createContext`。

然后在`App`组件中，我们通过使用`toggleColor`函数将`this.state`设置为一个对象，并将`color`属性设置为`'blue'`来初始化`this.state`。

我们将`this.state`作为`ColorContext.Provider`的`value`道具的值传递。

然后我们在`Button`组件中访问`ColorContext.Consumer`组件中的整个对象。

在那里，我们从从`ColorContext.Provider`传入的`this.state`中获得`color`和`toggleColor`属性。然后我们将`toggleColor`传递给`onClick`道具，将`color`传递给我们传递给`style`道具的对象。

然后，当我们单击“单击我”按钮时，文本颜色将在蓝色和绿色之间切换。

# 消费多个上下文

我们可以通过嵌套使用多个上下文。例如，我们可以这样做:

```
const ColorContext = React.createContext("green");
const BorderContext = React.createContext("");class Button extends React.Component {
  render() {
    return (
      <div>
        <ColorContext.Consumer>
          {color => (
            <BorderContext.Consumer>
              {border => (
                <button style={{ color, border }}>{this.props.children}</button>
              )}
            </BorderContext.Consumer>
          )}
        </ColorContext.Consumer>
      </div>
    );
  }
}class App extends React.Component {
  render() {
    return (
      <div>
        <ColorContext.Provider value="blue">
          <BorderContext.Provider value="3px solid green">
            <Button>Click Me</Button>
          </BorderContext.Provider>
        </ColorContext.Provider>
      </div>
    );
  }
}
```

在上面的代码中，我们创建了两个上下文，`ColorContext`和`BorderContext`，并将值传递给两者的`value`属性。我们在`App`组件中嵌套了提供者，这意味着两个上下文都可以被内部的`Button`组件使用。

然后在`Button`组件中，我们让两个上下文的消费者相互嵌套。然后我们可以获得从提供者传入的两个值。

然后我们使用这两个值来设置`button`的样式。

最后，我们有一个蓝色文本和绿色边框的按钮。

# 结论

我们可以使用 React 上下文 API 在组件之间共享数据。

它通过用`React.createContext`创建一个上下文对象来工作。然后，我们将上下文提供者组件包装在我们想要从中消费上下文的组件之外。

然后，在我们放在提供者内部的组件中，我们将上下文消费者组件包装在我们想要应用上下文值的组件之外。

最后，我们可以获得在上下文消费者内部传递的函数内部的值。