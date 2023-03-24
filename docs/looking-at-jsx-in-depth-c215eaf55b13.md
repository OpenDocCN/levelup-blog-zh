# 深入观察 JSX

> 原文：<https://levelup.gitconnected.com/looking-at-jsx-in-depth-c215eaf55b13>

![](img/703125c78f8050216eb05f4c75da8799.png)

约书亚·牛顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 是一个用于创建前端视图的库。React 引入了一种叫做 JSX 的新语法，它使我们能够用 JavaScript 轻松构建 UI 组件。在本文中，我们将深入研究 JSX 语法。

# JSX 是什么？

JSX 只是`React.createElement`方法的语法糖。

例如，以下内容:

```
<Button color="blue">Click Me</Button>
```

被编译为:

```
React.createElement(
  Button,
  {color: 'blue'},
  'Click Me'
)
```

# 指定 React 元素类型

JSX 标签的第一部分决定了 React 元素的类型。

类型必须大写，以便 React 知道它引用的是 React 组件。

这些标签被编译成一个命名变量。如果我们使用 JSX 反应元素，那么它必须在范围内。

例如，如果我们想使用`<Foo />`，那么`Foo`必须在范围内。

# React 必须在范围内

React 必须始终在范围内，因为 JSX 编译为`React.createElement`。既然`React`被引用，那么它一定在范围内。

# JSX 字体使用点符号

我们可以在 JSX 内部使用点符号来引用 React 组件。从导出多个 React 组件的模块中引用 React 元素非常方便。

例如，我们可以创建一个对象来对组件进行分组，并在另一个组件中引用它，如下所示:

```
const Components = {
  Foo: function(props) {
    const { color } = props;
    return <div style={{ color }}>foo</div>;
  }
};class App extends React.Component {
  render() {
    return <Components.Foo color="blue" />;
  }
}
```

在上面的代码中，我们有一个`Components`对象，里面有`Foo`属性。它的值是一个返回 div 的函数。

然后在`App`模块中，我们通过写`Component.Foo`来引用它。

# 用户定义的组件必须大写

我们应该对用户定义的组件使用大写字母，这样就可以将它们与`div`或`span`等元素区分开来。

如果我们有一个以小写字母开头的组件，那么我们应该把它赋给一个以大写字母开头的变量，然后在代码中引用它。

例如，我们可以这样做:

```
function foo(props) {
  const { color } = props;
  return <div style={{ color }}>foo</div>;
}const Foo = foo;class App extends React.Component {
  render() {
    return <Foo color="blue" />;
  }
}
```

我们有一个名为`foo`的组件，然后我们将`foo`赋值给`Foo`，然后在`App`中引用它，而不是直接引用`foo`。

# 在运行时选择类型

我们不能把 JavaScript 表达式作为组件类型。例如，我们不能写:

```
function Foo() {
  return <div>foo</div>;
}function Bar() {
  return <div>foo</div>;
}const components = {
  Foo, Bar
}class App extends React.Component {
  render() {    
    return <components['Foo'] />;
  }
}
```

如果我们想在`App`中引用`components[‘Foo’]`，我们必须先将它赋给一个变量:

```
function Foo() {
  return <div>foo</div>;
}function Bar() {
  return <div>foo</div>;
}const components = {
  Foo,
  Bar
};class App extends React.Component {
  render() {
    const Foo = components["Foo"];
    return <Foo />;
  }
}
```

# 作为道具的 JavaScript 表达式

我们可以通过用`{}`将 JavaScript 表达式括起来作为道具传入。

例如，我们可以写:

```
function Foo(props) {
  return <div>{props.num}</div>;
}class App extends React.Component {
  render() {
    return <Foo num={1 + 2 + 3} />;
  }
}
```

# 字符串文字

我们可以用两种方式传入字符串。

我们可以写:

```
function Foo({ message }) {
  return <div>{message}</div>;
}class App extends React.Component {
  render() {
    return <Foo message={"foo"} />;
  }
}
```

或者:

```
function Foo({ message }) {
  return <div>{message}</div>;
}class App extends React.Component {
  render() {
    return <Foo message="foo" />;
  }
}
```

所以:

```
<Foo message={"foo"} />
```

与以下内容相同:

```
<Foo message="foo" />
```

![](img/f987cc8dd081384188361fb9a905ff3f.png)

照片由 [Austin Neill](https://unsplash.com/@arstyy?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 道具默认为“真”

如果我们没有传入一个 prop 值，那么这个值默认为`true`。

例如，如果我们有以下内容:

```
function Foo({ foo }) {
  return <div>{`Foo is ${foo}`}</div>;
}class App extends React.Component {
  render() {
    return <Foo foo />;
  }
}
```

然后我们看到`Foo is true`显示在屏幕上，因为`foo`是`true`，因为我们没有设置`foo`道具的值。

# 传播属性

我们可以使用 spread 操作符将对象属性扩展到道具中。例如，我们可以写:

```
function Greeting({ firstName, lastName }) {
  return (
    <div>
      {firstName} {lastName}
    </div>
  );
}class App extends React.Component {
  render() {
    const props = { firstName: "Jane", lastName: "Smith" };
    return <Greeting {...props} />;
  }
}
```

把《简·史密斯》搬上银幕。`props`对象作为属性名传入`Greeting`，属性名作为属性名，值作为值。

它与以下内容相同:

```
function Greeting({ firstName, lastName }) {
  return (
    <div>
      {firstName} {lastName}
    </div>
  );
}class App extends React.Component {
  render() {
    return <Greeting firstName="Jane" lastName="Smith" />;
  }
}
```

我们还可以在接收道具的组件中选择特定的道具来消费。

例如，我们可以写:

```
const Button = ({ kind, children, ...other }) => {
  const className = kind === "primary" ? "PrimaryButton" : "SecondaryButton";
  return (
    <button className={className} {...other}>
      {children}
    </button>
  );
};class App extends React.Component {
  render() {
    const props = { kind: "primary", a: 1, b: 2 };
    return <Button {...props}>Click Me</Button>;
  }
}
```

在上面的`Button`组件中，我们只想使用`kind`和`children`道具，忽略其余的。因此，我们可以使用参数中的析构操作符来获取那些，然后使用 rest 操作符将其余部分放入其自己的对象中。

然后我们可以通过`other`直接进入`button`。`other`就是`{ a: 1, b: 2 }`。

# 结论

JSX 是`React.createElement`方法的语法糖。

我们应该首先用大写字母命名我们自己的组件。我们不能用表达式来引用组件名。

还有，我们可以通过各种方式传入道具。它们可以显式传入。它们也可以通过 spread 操作符传入。

同样，可以使用析构操作符来消耗道具，而不直接消耗的道具可以使用 rest 操作符放入一个单独的对象中。