# React 本机认证流程，最简单有效的方式

> 原文：<https://levelup.gitconnected.com/react-native-authentication-flow-the-simplest-and-most-efficient-way-3aa13e80af61>

## 与 API 连接，保存数据，并在将来的会话中恢复它们

![](img/52660434fadc3beddd5f044ba38e809a.png)

克里斯·帕纳斯在 [Unsplash](https://unsplash.com/) 上的照片

几乎所有的应用程序都需要认证流程，因为它们包含只有授权用户才能访问的内容。

本文将解释如何创建一个 React 本机身份验证流，该流连接到 API，持久保存要在未来会话中恢复的数据，并为整个应用程序提供一种订阅身份验证状态更改的有效方式。

这里的重点是为上述流程构建整个结构，而不是从头开始创建应用程序。但是你可以在这里 访问完整的代码 [**库。**](https://github.com/LucasGarcez/react-native-auth-flow)

## 经过验证和未经验证的用户

在 React Native 中，区分这些用户的一种常见方法是创建不同的“组”屏幕。一个用于未经身份验证的用户，包含登录和注册等屏幕，另一个用于经过身份验证的用户，包含主页、设置和所有其他与用户内容相关的屏幕。

[react-navigation](https://reactnavigation.org/docs/getting-started) 库将帮助创建这些组，更具体地说， **createStackNavigator** 函数。第一组 **AppStack** 将包含[主屏幕](https://github.com/LucasGarcez/react-native-auth-flow/blob/master/src/screens/HomeScreen.tsx)，而 **AuthStack** 将包含[标志屏幕。](https://github.com/LucasGarcez/react-native-auth-flow/blob/master/src/screens/SignInScreen.tsx)

创建一个**路由器**组件，负责根据用户是否通过身份验证来选择显示哪个堆栈。堆栈需要在 **NavigationContainer** 中，这是 react-navigation 的一个组件，用于管理导航树和导航状态。

**authData** 表示用户认证状态，换句话说，是否有认证用户。 **authData** 不能是来自**路由器**的简单属性，因为其他组件可以从应用程序中的不同位置更新它，所以需要另一个解决方案。一个很好的解决方法是使用 [React 上下文](https://reactjs.org/docs/context.html)。

> 上下文提供了一种通过组件树传递数据的方法，而不必在每一层手动向下传递属性，树中的任何组件都可以“监听”上下文的变化。

## 使用 React 上下文来提供 authData

上下文需要提供 **authData** 本身、登录函数、退出函数和加载状态。登录函数将与 API 连接，并根据响应更新 **authData** 。注销功能将清理数据，在某些情况下，与 API 连接以使令牌或类似的东西无效。加载状态是必需的，因为身份验证过程是一个异步任务，通过 API 作为本地持久连接。因此，数据形状可以是这样的:

有了这些**类型**来定位，我们就可以创建由 **AuthContext、**调用的上下文和由 **AuthProvide 调用的相关提供者。**

下面的代码稍微长一点，但是我们可以从上到下阅读。每一部分都有一个注释，解释什么是什么和意图是什么。

对于任何组件监听器认证状态的改变和调用 sing-in 或 sing-out 函数， **App** 根组件要用 **AuthProvider** 封装**路由器**。

为了方便对 **AuthContext** 的访问，我们可以创建一个简单的 [React 钩子](https://reactjs.org/docs/hooks-custom.html)来抽象上下文连接逻辑。

**路由器**组件将使用 **useAuth** 钩子来决定显示哪个正确的堆栈，如果数据未准备好，则显示一个 [**加载**](https://github.com/LucasGarcez/react-native-auth-flow/blob/master/src/components/Loading.tsx) 组件。

屏幕，也可以使用挂钩来登录和退出。

## 持久化数据

最后要解决的问题是持久性。到目前为止，当 App 关闭和打开时，所有数据都会丢失，因为上下文只存在于 App 的内存中。[@ react-native-async-storage/async-storage](https://react-native-async-storage.github.io/async-storage/)库将对此进行处理并保存数据。由于 **AppProvider** 集中了身份验证状态的变化，所以这是放置持久性逻辑的最合适的位置。

*   App 启动时:检查存储；如果有数据，更新 **authData** 状态。
*   当用户登录时:将来自 API 的 **authData** 保存在存储器中。
*   当用户退出时:从存储器中删除 **authData** 。

就这些了，伙计们！现在您有了一个完整的身份验证流程，具有良好分离的职责、持久性和 API 连接。同样，您可以在 GitHub [库](https://github.com/LucasGarcez/react-native-auth-flow)访问这个项目的全部代码。

感谢来到这里；如果有任何问题，请留下评论，我将很乐意帮助您。