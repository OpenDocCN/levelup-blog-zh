# 反应提示—样式、克隆元素和导航栏

> 原文：<https://levelup.gitconnected.com/react-tips-styles-clone-element-and-nav-bar-9008be95e37d>

![](img/203ff4a85bd1639c6fc5f724d0eaca83.png)

克里斯蒂安·马丁在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 是一个用于创建 web 应用程序和移动应用程序的流行库。

在本文中，我们将了解一些编写更好的 React 应用程序的技巧。

# “Fix”样式属性要求从样式属性到值的映射，而不是字符串错误

我们必须向`style`道具传递一个对象。

例如，我们可以写:

```
<span style={{ float: 'left', paddingRight: '5px' }} >foo</span>
```

我们传入一个具有骆驼外壳属性的对象，而不是烤肉串外壳。

此外，我们可以用`import`关键字从文件中导入样式。

例如，我们可以写:

`styles.css`

```
.color{
  color: "red";
  background: "#0f0";
}
```

然后我们写道:

```
import './styles.css';const Button = (props) => {
  return (
    <div>
      <span className="color">{props.age}</span>
    </div>
  );
};const infos = {
  age: 20
};ReactDOM.render(<Button {...infos} />, mountNode);
```

我们导入`styles.css`，然后样式将被应用。

# React useReducer 异步数据提取

如果我们要改变一个经过大量计算后设定的状态，可以用`useReducer`代替`useState`。

如果我们有复杂的状态逻辑，状态中有多个子值，或者下一个状态依赖于前一个状态，那么我们可以使用这个钩子。

例如，我们可以写:

```
const initialState = {};function reducer(state, action) {
  switch (action.type) {
    case 'profileReady':
      return action.payload;
    default:
      throw new Error();
  }
}function App(props) {
  const [profile, setProfile] = React.useReducer(reducer, initialState); useEffect(() => {
    reloadProfile()
      .then((profileData) => {
        setProfile({
          type: "profileReady",
          payload: profileData
        });
    });
  }, []);  return (
    <div>{profile.name}</div>
  );
}
```

我们有一个设定状态的减速器。

我们有一个可以传入的初始状态。

我们可以将它们都传递到`useReducer`钩子中。

它按照这个顺序返回状态和状态设置函数。

然后我们可以用这个和`useEffect`来获取数据并设置它。

第二个参数中的空数组确保回调仅在组件加载时运行。

# 如何使用 React 路由器链接刷新页面

我们可以在不使用 React 路由器的情况下重新加载页面。

例如，我们可以写:

```
window.location.reload();
```

重新加载页面。

# 用 React 路由器创建一个导航条

为了使用 React Router 创建 navbar，我们使用 React Router 附带的组件来创建路由。

例如，我们可以写:

```
import React from "react";
import {
  BrowserRouter as Router,
  Switch,
  Route,
  Link
} from "react-router-dom";export default function App() {
  return (
    <Router>
      <div>
        <nav>
          <ul>
            <li>
              <Link to="/">Home</Link>
            </li>
            <li>
              <Link to="/about">About</Link>
            </li>
            <li>
              <Link to="/dashboard">Dashboard</Link>
            </li>
          </ul>
        </nav> <Switch>
          <Route path="/about">
            <About />
          </Route>
          <Route path="/dashboard">
            <Dashboard />
          </Route>
          <Route path="/">
            <Home />
          </Route>
        </Switch>
      </div>
    </Router>
  );
}function Home() {
  return <h2>Home</h2>;
}function About() {
  return <h2>About</h2>;
}function Dashboard() {
  return <h2>Dashboard</h2>;
}
```

我们创建了一些用作路线的组件。

然后我们使用`Switch`组件中的来创建我们的路线。

我们将`Route`组件放入其中，并将想要显示的组件嵌套在其中。

导航栏是用`nav`组件创建的。

在它里面，我们有`Link`组件来创建链接以显示路由组件。

`to`是我们想要显示的组件的路径。

并且它应该与`Switch`中的`path`值之一相匹配。

做导航，可以调用`history.push`导航。

例如，我们可以写:

```
<a onClick={() => history.push('dashboard') }>Dashboard</a>
```

以编程方式导航。

# 在为子级提供属性时，将正确的类型分配给 React.cloneElement

我们可以将子组件的类型设置为`React.ReactElement`类型。

例如，我们可以写:

```
return React.cloneElement(child as React.ReactElement<any>, {
  name: this.props.name,
  age: this.props.age
});
```

如果`child`是一个元素。

我们也可以使用`isValidElement`型防护罩。

例如，我们可以写:

```
if (React.isValidElement(child)) {
  return React.cloneElement(child, {
    name: this.props.name,
    age: this.props.age
  });
}
```

这将让 TypeScript 编译器自动确定类型，而无需添加任何数据类型断言。

![](img/74a9ca2329c68d5d55a2bc8703f6358a.png)

照片由 [vaun0815](https://unsplash.com/@vaun0815?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

`cloneElement`可以通过设置正确的类型或使用类型保护在 React 中与子元素一起使用。

造型可以用各种物品来完成。

我们可以使用 React 路由器轻松创建导航栏。