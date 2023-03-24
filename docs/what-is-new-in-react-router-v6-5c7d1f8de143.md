# React 路由器 V6 有什么新功能？

> 原文：<https://levelup.gitconnected.com/what-is-new-in-react-router-v6-5c7d1f8de143>

![](img/0d0c7058d7cb2c19c5b784d084300d03.png)

费伦茨·阿尔马西在 [Unsplash](https://unsplash.com/s/photos/react-router?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

你知道管理 React 应用程序导航的最流行的库有了新版本吗？React Router 是管理 React 应用程序导航的最流行的库，新版本 6 带来了非常有趣的新功能。

版本 6 带来了组件 ***路由*** ，基本上是替换了老的 ***开关*** 组件。根据文档，这个想法是为了让应用程序更容易被看作不同的独立部分，每个部分都存在于一个路径中。

它还带来了新的 ***Outlet*** 组件，该组件用于我们希望将路线声明放在一个地方，但我们希望为每组路线定义一个“布局”的情况。

另一个显著的变化是，即使您能够在 React Router 的早期版本中将路由定义为对象，这种声明路由的方式也已经通过 ***useRoutes*** 钩子 100%集成到 React Router V6 中。

最后但并非最不重要的是，React Router 的这个新版本集成了 React Suspense API，这意味着对于那些我们绝对需要在应用程序中导航的时刻(例如，在发送表单之后)，使用 ***useNavigate*** 钩子代替了 ***useHistory*** 钩子。

正如你所看到的，React 路由器的这个新版本带来了非常吸引人的消息。这个库的新版本通常会打破旧的特性，这就是为什么我们构造和定义应用程序路径的方式会有显著的变化。在我看来，最重要的变化是引入了将路由集视为迷你应用程序。