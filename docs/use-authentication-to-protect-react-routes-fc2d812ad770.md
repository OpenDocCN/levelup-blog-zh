# 使用身份验证来保护反应路由

> 原文：<https://levelup.gitconnected.com/use-authentication-to-protect-react-routes-fc2d812ad770>

![](img/4cdabfd6a20c77fb97a9ff692401cf25.png)

这张照片是我自己拍的！

你看了标题，就知道你为什么来了！想在 2020 年做出受保护的反应路线？我们走吧！(要遵循本指南，您需要熟悉路由器)

首先有一个 react 项目。我喜欢把我的路由器从我的 app.js 文件分离到它自己的 router.js 文件中。我的 app.js 文件如下所示:

```
//App.js
import './App.css'
import React from 'react'
import Router from './components/application/router.js'export default function App() {
  return (
    <div className='App'>
      <Router />
    </div>
  )
}
```

没错，我不用分号；除了我的英语。

让我们设置 router.js 文件:

```
//router.js
//pretend we imported all of our components
import React from 'react'
import { BrowserRouter, Switch, Route, Redirect } from 'react-router-dom'export default function Router() {
  return (
      <BrowserRouter>
        <Navigation /> 
        // I like to put my nav links into a seperate file
          <Switch>
            <Route exact path='/'>
              <PublicComponent />
            </Route>
          </Switch>
      </BrowserRouter>
  )
}
```

这里我们有一个简单的路由器，只有一条路由，根路由是公共的。为了给即将创建的私有路由添加身份验证，我们需要为路由器创建一个上下文。如果你遵循这个教程，你不需要对一个上下文有很强的理解，但这里是官方的描述:

> “上下文提供了一种通过组件树传递数据的方式，而不必在每一级手动向下传递属性。”

请阅读[上下文—反应(reactjs.org)](https://reactjs.org/docs/context.html)了解更多详情。

好的，我们将开始创建一个上下文和一个钩子，你可以在这里读到:[介绍钩子——React(reactjs.org)](https://reactjs.org/docs/hooks-intro.html)。

```
//hooks/auth.js
import React, { useState, useEffect, useContext, createContext } from 'react'const authContext = createContext()export function ProvideAuth({ children }) {
  const auth = useProvideAuth()
  return <authContext.Provider value={auth}> { children } </authContext.Provider>
}export const useAuth = () => useContext(authContext)function useProvideAuth() {
  return []
}
```

这是我们的上下文和钩子。来说说我们目前掌握的情况，(提示:不多)。

导入必要的需求后，我们创建一个上下文。然后，我们将上下文作为组件导出。不要太担心这些是如何工作的，这超出了我们的讨论范围。真正要注意的最重要的事情是，无论什么由`useProvideAuth()`返回，现在都可以通过使用`useAuth()`钩子来访问。

考虑到这一点，让我们创建一个有用的返回值！

```
//hooks/auth.js//we need a base URL for our sign in function
const baseUrl = process.env.REACT_APP_BASE_URL***function useProvideAuth() {
  const [user, setUser] = useState(null) const signin = (credentials) => {
    fetch(baseUrl + 'login', {
      method: 'POST',
      credentials: 'include',
      headers: {
        'Content-Type': 'application/json',
        'Accepts': 'application/json'
      },
      body: JSON.stringify(credentials)
    })
    .then(res => res.json())
    .then(json => setUser(json.user))
  }useEffect(() => {
    //check authentication
  }, [])return {
    user,
    signin
  }
}
```

我们使用 React 的`useState()`功能来设置一个名为 user 的常量，并创建一个函数来更新它。如你所见，在我们的`fetch()`之后的第二个`.then()`中，我们有`setUser(json.user)`。

如果您想包含检查用户访问网站时是否登录的功能，应该在`useEffect()`回调中完成。

回到路由器，让我们使用新创建的上下文:

```
//router.js***export default function Router() {
  return (
    <ProvideAuth>
      <BrowserRouter>
        <Navigation /> 
        // I like to put my nav links into a seperate file
          <Switch>
            <Route exact path='/'>
              <PublicComponent />
            </Route>
          </Switch>
      </BrowserRouter>
    </ProvideAuth>
  )
}
```

接下来呢？我们如何在上下文中使用 auth 变量的值？让我们创建一条私人路线。如果你不知道你可以随时创建自己喜欢的定制路线，那么，你的想法就要被打破了！

```
//router.js***function PrivateRoute({ children, ...rest }) {
  const auth = useAuth()
  return (
    <Route {...rest} render={({ location }) =>
        auth.user ? (children) :
        (<Redirect to={{ pathname: '/login', state: { from: location } }} />)
      }
    />
  )
}
```

路由基本上只是组件！第一个参数总是路由的子节点。`...rest`是路线标签中剩余的参数(如果有的话)。如你所见，我们初始化了我们的`useAuth()`钩子。

在我们的返回值中，我们只需要一个标准的 React route 组件，并在渲染参数中给它`...rest`,我们传入一个可爱的函数，该函数将位置作为参数。这个函数返回一个三元“if”语句，该语句检查`auth.user`的值，并返回嵌套在`<PrivateRoute></PrivateRoute>`之间的组件，或者将我们重定向到登录路径。

回到我们的路由器，我们可以实现`PrivateRoute`:

```
//router.js***export default function Router() {
  return (
    <ProvideAuth>
      <BrowserRouter>
        <Navigation /> 
        // I like to put my nav links into a seperate file
          <Switch>
            <Route exact path='/'>
              <PublicComponent />
            </Route>
            <PrivateRoute path='private'>
               <PrivateComponent />
            </PrivateRoute>
          </Switch>
      </BrowserRouter>
    </ProvideAuth>
  )
}
```

是的，就是这么简单！但是我们还没有完成。

在我们的登录组件中，(你自己处理这个)我们需要包含一个对我们的登录函数的引用。

```
//hypotheticalLoginComponent.jsconst auth = useAuth()*** const onSubmit = () => {
 auth.signin(credentials)
}
```

在您的登录组件中，您应该使用`useAuth()`将登录凭证提交给我们出色的 auth hook。

如果您想将所有 API 调用抽象到另一个文件，您也可以只让您的`signin()`函数调用`setUser()`，并在 API 调用解析后调用那个登录函数。

这里是`router.js`和`auth.js`的最终文件

```
//router.js
import React from 'react'
import { BrowserRouter, Switch, Route, Redirect } from 'react-router-dom'
import { ProvideAuth, useAuth } from '../../hooks/auth.js'export default function Router() {
  return (
    <ProvideAuth>
      <BrowserRouter>
        <Navigation /> 
        // I like to put my nav links into a seperate file
          <Switch>
            <Route exact path='/'>
              <PublicComponent />
            </Route>
            <PrivateRoute path='private'>
               <PrivateComponent />
            </PrivateRoute>
          </Switch>
      </BrowserRouter>
    </ProvideAuth>
  )
}function PrivateRoute({ children, ...rest }) {
  const auth = useAuth()
  return (
    <Route {...rest} render={({ location }) =>
        auth.user ? (children) :
        (<Redirect to={{ pathname: '/login', state: { from: location } }} />)
      }
    />
  )
}
```

```
import React, { useState, useEffect, useContext, createContext } from 'react'const baseUrl = process.env.REACT_APP_BASE_URLconst authContext = createContext()export function ProvideAuth({ children }) {
  const auth = useProvideAuth()
  return <authContext.Provider value={auth}> { children } </authContext.Provider>
}export const useAuth = () => useContext(authContext)function useProvideAuth() {
  const [user, setUser] = useState(null)const signin = (credentials) => {
    fetch(baseUrl + 'login', {
      method: 'POST',
      credentials: 'include',
      headers: {
        'Content-Type': 'application/json',
        'Accepts': 'application/json'
      },
      body: JSON.stringify(credentials)
    })
    .then(res => res.json())
    .then(json => setUser(json.user))
  }useEffect(() => {
    //check authentication
  }, [])return {
    user,
    signin
  }
}
```

希望这些对你有用。祝您愉快，感谢您的阅读！