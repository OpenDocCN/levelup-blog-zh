# 迁移到 react-router v6 时，他们没有解释什么

> 原文：<https://levelup.gitconnected.com/what-they-dont-explain-when-migrating-to-react-router-v6-2e8fb63c64de>

## 如何在使用神奇的 renderRoutes 时迁移代码

![](img/5eb5dd16e733337e44c3e7a0bed17867.png)

泰勒·卢瑟福在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我喜欢[反应路由器](https://reactrouter.com/)。它很容易使用，有很多功能，从 v5 开始他们开始使用 React 钩子。

我也喜欢从一开始就定义我的所有路线，所以我使用了他们的包 [react-router-config](https://github.com/remix-run/react-router/tree/v5/packages/react-router-config) 和神奇的函数`renderRoutes`来呈现嵌套的路线。

然后 v6 出来了。改进看起来很棒。甚至还有一个迁移指南，乍一看似乎足够详细地介绍了如何迁移到 v6，还有一个专门的部分是为像我这样使用 [react-router-config](https://reactrouter.com/docs/en/v6/upgrading/v5#use-useroutes-instead-of-react-router-config) 的人准备的。

但是读了又读，读了又读，我找不到一篇关于如何迁移`renderRoutes`代码的参考文献。一点风声也没有。包裹`react-router-config`也不见了，我被卡住了。

现在我开始出汗了。如果我呈现嵌套路由的主要功能消失了，我该如何迁移到 v6？我应该放弃我喜欢的定义路线的方式，开始使用`<Route />`或`<Switch />`吗？一定有办法的。React Router 的团队不会放弃这项功能。

我休息了一会儿，然后又开始查看他们的文档。我找到了这个关于路由对象的[指南](https://reactrouter.com/docs/en/v6/examples/route-objects)，它引导我找到了关于它们如何实现它的代码。然后我从他们的 [react-router-dom](https://github.com/remix-run/react-router/tree/main/packages/react-router-dom) : `<Outlet />`中看到了一个新组件。

有了这些新信息，我回到迁移指南，在这里找到了对`<Outlet />` [的引用](https://reactrouter.com/docs/en/v6/upgrading/v5#advantages-of-route-element)。为什么我以前没有发现？正如我之前所说，我不使用`<Route />`来建立我的路线。

在我看来，迁移指南遗漏了一个解释如何使用`renderRoutes`更新代码的例子。至少 React Router 的团队应该提到过，在使用`<Route />`时，需要用和我们一样的方式来完成。

理想情况下，类似这样的事情会省去我很多麻烦:

```
// define routes
const Routes = () => {
  const route = useRoutes([
    {
      element: <App />,
      path: '/',
      children: [
        {
          path: '/',
          element: <Home />
        },
        {
          path: '/dashboard',
          element: <Dashboard />
        },
      ]
    }
  ]) return route
}// render nested routes
const App = () => {
  return (
    <Outlet />
  )
}
```

不管怎样，问题解决了，迁移到 v6 不再是一件痛苦的事情。

这里是对[出口](https://reactrouter.com/docs/en/v6/api#outlet)组件的 API 引用。