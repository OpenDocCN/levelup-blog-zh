# 反应提示—反应和还原、导航、下拉值

> 原文：<https://levelup.gitconnected.com/react-tips-react-and-redux-navigation-drop-down-values-5458db9d7924>

![](img/e08d902b810c955c358309c3cc0aa9eb.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Bambi Corro](https://unsplash.com/@bambicorro?utm_source=medium&utm_medium=referral) 拍摄的照片

在本文中，我们将了解一些编写更好的 React 应用程序的技巧。

# 改变下拉值时的设置状态

我们可以通过动态设置键并将值设置到`event.target.value`来将下拉列表中选择的值设置为状态值。

例如，我们可以写:

```
class App extends React.Component { constructor(props) {
    super(props);
    this.state = { fruit: '' };
  } handleChange = (event) => {
    this.setState({ [event.target.name]: event.target.value });
  } render() {
    return (
      <select value={this,state.fruit} name='fruit' handleChange={this.handleChange}>
        <option value="apple">Apple</option>
        <option value="banana">Banana</option>
        <option value="cranberry">Cranberry</option>
      </select>
    )
  }}
```

我们用`handleChange`方法将`fruit`状态设置为选定的值。

它只是调用`setState`用值设置`fruit`状态，该值存储在`event.target.value`中。

# 在 React 中更新嵌套状态属性

我们可以用`setState`设置嵌套状态属性。

它用以前的状态接受一个回调，然后我们用新的状态返回一个对象。

例如，我们可以写:

```
this.setState(state=> ({ ...this.state.someProp, flag: false} }));
```

或者我们可以写:

```
this.setState({ someProperty: { ...this.state.someProperty, flag: false} });
```

无需回调即可更新状态。

在任一示例中，我们使用 spread 操作符将新值合并到旧对象中。

# 从数组中获取前 N 个元素

我们可以用`slice`方法从数组中获取前 N 个元素。

例如，我们可以写:

```
const size = 5;
const items = list.slice(0, size).map(i => {
  return <Foo item={i} key={i.id} />
}return (
  <div>
    {items}
  </div>   
)
```

在`render`方法或函数组件内部。

我们用`slice`方法得到前 5 项。

然后我们使用`map`返回要渲染的项目列表。

然后我们可以把它放在任何地方。

# 在 React 路由器中推送历史记录

如果我们将 React Router 的`this.props.history.push`方法与类组件一起使用，我们可以使用它。

例如，我们可以写:

```
import React from "react";
import { withRouter } from "react-router-dom";class Foo extends React.Component {
  //...
  go() {
    this.props.history.push("/some/path");
  }
  //...
}
export default withRouter(Foo);
```

我们在 React 组件中使用了`go`方法来调用`this.prop.history.push`中我们想要到达的路径。

它是可用的，因为我们调用了`withRouter`高阶组件来添加方法。

使用 React 路由器的 hooks 版本，我们可以编写:

```
import { useHistory } from "react-router-dom";function App() {
  const history = useHistory(); const goHome = () => {
    history.push("/home");
  } return (
    <button type="button" onClick={goHome}>
      Go home
    </button>
  );
}
```

我们使用`useHistory`钩子来获取历史对象，并在其上调用`push`来转到我们想要的路径。

# React Redux mapDispatchToProps

使用 React Redux，我们使用`mapDispatchToProps`方法将分派动作映射到道具。

例如，我们可以写:

```
class App extends Component {
  sendAlert(){
    this.props.sendAlert()
  } render() {
    <div>
      <h1>alert: {this.props.alert}</h1>
      <Button onClick={sendAlert}/>
    </div>
  }
}function mapDispatchToProps(dispatch) {
  return({
    sendAlert: () => {dispatch(ALERT_ACTION)}
  })
}function mapStateToProps(state) {
  return { alert: state.alert }
}export const FancyButtonContainer = connect(
  mapStateToProps, mapDispatchToProps)(
  App
)
```

我们有`this.propss.sendAlert`方法，它是用`nmapDispatchToProps`返回的。

`mapDispatchToProps`是一个我们可以和`connect`高阶组件一起使用的函数，用于将分派动作映射到道具。

同样，`mapStateToProps`让我们从 Redux 存储中返回状态，并使其在组件中作为道具可用。

所以`this.props.alert`是返回对象中`alert`属性的值。

# 更新父级的状态

我们可以更新父节点；通过将一个函数作为道具从父节点传递给子节点。

然后在子组件中，我们可以调用它来更新状态。

例如，我们可以写:

```
class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.handler = this.handler.bind(this);
  } handler() {
    this.setState({
      foo: 'bar'
    })
  } render() {
    return <Child handler = {this.handler} />
  }
}class Child extends React.Component {
  render() {
    return <button onClick={this.props.handler}>click me</button>
  }
}
```

我们将`this.handler`方法传递给`Child`组件。

我们必须记住调用带有`this`的`bind`，这样`this`的值就是父值。

然后我们可以在`Child`中调用它，把它作为`this.props.handler`传递给`onClick` prop。

![](img/6a181c3452f5285ec30012c0ba5c0fc4.png)

丹尼尔·弗兰克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们可以将父组件的功能传递给子组件。

我们可以设置下拉菜单改变时的状态。

React 路由器有几种导航方式。

Redux 可用于将动作和状态映射到组件的道具。