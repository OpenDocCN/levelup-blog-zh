# 用 React 的 useContext & useReducer 钩子实现“Redux 风格”的状态管理

> 原文：<https://levelup.gitconnected.com/implementing-redux-style-state-management-with-reacts-usecontext-usereducer-hooks-c1c5596d9619>

![](img/66ea292bf6e84040ff0c25e8fd49ff40.png)

对于那些还不知道的人，React 团队最近[引入了钩子](https://reactjs.org/docs/hooks-intro.html)作为一种采用更功能化的方法来编写组件的方式，它们已经在 v 16 . 8 . 0-alpha 1 中存在了几个月。不过，他们现在已经得到了 [**和**](https://github.com/facebook/react/pull/14679) 的正式批准，将会包含在 React 即将推出的版本中🎉

(如果有一个视频是你*有*可以看的，那就是这个[一个](https://www.youtube.com/watch?v=dpw9EHDh2bM&feature=youtu.be))

关于钩子将如何影响 React 生态系统有很多观点，特别是关于我们在 React 应用程序中管理状态的方式。如果你对 React 最近的活动有所了解，我敢肯定你已经看到了一篇名为“*钩子是冗余杀手吗？？？*”或大意如此的话。我的看法，不是。

Redux 肯定有它的学习曲线，在不真正需要它的小规模应用程序中很难理解它的神奇之处。有很多样板文件和我想称之为“仪式化”的代码，你必须编写这些代码来用 Redux 设置好一切，但是一旦它被正确地配置，它是非常惊人的。围绕 Redux 的工具，尤其是它的[开发者工具](https://www.npmjs.com/package/redux-devtools-extension)，也是非常出色的，在 Redux 实现之后，调试 React 应用程序会变得更加容易。然而，对于可能无法保证 Redux 的小型应用程序，可以使用替代方法(就像我下面将要描述的)。

通过从我在教程和关于钩子的演练中看到的方法中挑选和提取，我已经精心制作了一种方法来应用我们在 Redux 中看到使用的状态管理风格，但是只使用了*和* React。完全透明，我在其中使用该系统的应用程序规模相对较小，我认为有许多开发人员的大型应用程序肯定会从使用 Redux 中受益更多。

该系统利用`[useContext](https://www.npmjs.com/package/redux-devtools-extension)`和`[useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer)`挂钩。前者，如 React 文档中所述，“*接受一个上下文对象(从* `*React.createContext*` *返回的值)并返回当前上下文值，该值由给定上下文*的最近上下文提供者给出”，而后者“*接受一个类型为* `*(state, action) => newState*` *的缩减器，并返回与一个* `*dispatch*` *方法配对的当前状态。(如果你熟悉 Redux，你已经知道这是如何工作的。).*”

我的这些项目的文件结构如下所示:

```
> public
> src
 - components/
 - state/
   - actions/
   - reducers/
   - context/
   - constants.js
 - hooks/
```

在`context/index.js`中，我使用`React.createContext`创建了一个上下文实例:

```
import { createContext } from "react";const UserContext = createContext({
  currentUser: localStorage.getItem("authenticated-user")
  ? JSON.parse(localStorage.getItem("authenticated-user"))
  : {}
 });export default UserContext;
```

然后，在`App.js`中，我定义了我的`initialState`，在其中存储了`useContext`钩子的返回值:

```
const App = () => {
const initialState = useContext(UserContext);
const [{ currentUser }, dispatch] = useReducer(
UserReducer,
initialState
);return (
<UserContext.Provider *value*={{ currentUser, dispatch }}>
  <Nav />
  <Router>
    <Login *path*="/" />
    <Register *path*="/register" />
    <Dashboard *path*="/user/:id" />
  </Router>
</UserContext.Provider>
)};
```

请注意，在我的`App`组件的顶部，我从从`useReducer`的返回值中解构的状态中解构了`currentUser`(~*解构启始* ~)。然后，我将从`useReducer`接收到的`currentUser`和`dispatch`函数(我们将在一分钟后讨论)传递给上下文提供者，使其可用于我的应用程序中的每个组件。对于不熟悉解构的人，同样可以通过以下方式完成:

```
const [state, dispatch] = useReducer(UserReducer, initialState)
...
<UserContext.Provider value={{ currentUser: state.currentUser, dispatch }}
```

现在让我们来看看我在`reducer/index.js`中的减速器:

```
import { REGISTER_USER, LOGIN_USER, LOGOUT_USER } from "./constants";const initialState = {
  currentUser: localStorage.getItem("authenticated-user")
  ? JSON.parse(localStorage.getItem("authenticated-user"))
  : {},
  errorMessage: ""
};const UserReducer = (state = initialState,
{ type, registeredUser, loggedInUser, success, message, boards }
) => {switch (type) {
  case REGISTER_USER:
    console.log(
     `%c {type: REGISTER_USER, registeredUser: ${JSON.stringify(
       registeredUser )}}`, "color: yellow; font-weight: bold");return success ? { ...state, newUser: true }
 : { ...state, errorMessage: message };

  case LOGIN_USER:
    console.log(`%c {type: LOGIN_USER, loggedInUser:
      ${JSON.stringify(loggedInUser)}}`, "color: teal; font-weight:
       bold"); return success ? { ...state, currentUser: loggedInUser }
  : { ...state, errorMessage: message } case LOGOUT_USER:
  console.log(
    `%c {type: LOGOUT_USER, currentUser: {}} `,
      "color: pink; font-weight: bold" );return { ...state, currentUser: {} };default:
  return state;
}
};export default UserReducer;
```

有几件事需要注意…首先，为什么我在文件顶部定义我的`initialState`时要重复代码？为什么我不能像以前一样给`initialState`赋值`useContext(UserContext)`的返回值，或者为什么我不能在别处定义它，导出它，然后导入到我的 reducer 文件中？关于调用钩子的一个重要警告(来自 React 文档):" ***不要在循环、条件或嵌套函数中调用钩子。相反，总是在 React 函数的顶层使用钩子。通过遵循这条规则，您可以确保每次组件呈现时都以相同的顺序调用钩子。这就是为什么 React 能够正确保存多个* `*useState*` *和* `*useEffect*` *调用之间的钩子状态。*”换句话说，你只能在 *React* 函数的顶层调用一个钩子，而不是普通的 JavaScript 函数。**

还有，这个 console.log 到底是什么东西？好吧，如果你曾经使用过 [redux-logger](https://www.npmjs.com/package/redux-logger) 中间件，这是我模仿该功能的廉价尝试，让我知道每个缩减器何时被调用。

如果你还不知道，我是一个解构主义的超级粉丝，我已经在我的 reducer 函数的参数中解构了我的“有效载荷”(如果你熟悉 Redux 的话)。但是，请注意，总的来说，这遵循了与 Redux reducer 非常相似的结构——主要是因为它只是普通的 JavaScript。

下面是我在`actions/index.js`中的一个动作:

```
*//user registration* export const registerUser = async (user, dispatch) => {
  const { username, email, password } = user;try {
 const registerRes = await fetch("/user/register", { method: "POST",
  mode: "cors", cache: "no-cache", headers: { "Content-Type":
  "application/json"
 },
 redirect: "follow", referrer: "no-referrer", body: JSON.stringify({
   username, email, password })
 });const { token, success, user: registeredUser } = await 
 registerRes.json();if (success) {
  dispatch({ type: REGISTER_USER, registeredUser, success });
   console.log(`${user.username}'s account was REGISTERED and set in 
   localstorage`);return localStorage.setItem( "authenticated-user", JSON.stringify({
  token, username: registeredUser.username, id: registeredUser._id
 })
);
}} catch (error) {
  console.error(error);
  return dispatch({ type: ERROR, message: error.message });
 }
};
```

如果你曾经使用过 redux-thunks，那基本上是我的每个动作创建者的形式。注意我是如何接受`dispatch`函数作为参数的，它从调用它的组件传递给动作。

那么，你到底是怎么利用这些的呢？嗯，下面是我如何在我的`components/Register.js`中使用它(利用上面定义的动作创建器):

```
import React, { useState } from "react";
import { navigate } from "@reach/router";
import { registerUser } from "../state/actions/user_actions";
import { useContext } from "react";
import UserContext from "../state/context";const initialFormState = {
  username: "",
  email: "",
  password: "",
  organization: ""
};const Register = () => {
  const [user, setUser] = useState(initialRegisterFormState);
  const { dispatch } = useContext(UserContext); const handleInput = e => {
    setUser({ ...user, [e.target.name]: e.target.value });
  }; const handleSubmit = e => {
    e.preventDefault();
    setUser(initialFormState);
    registerUser(user, dispatch);
    navigate("/");
 };...
```

我能够从`useContext`钩子的返回值中解构我的`dispatch`函数，然后将它传递给我的`registerUser`动作。很简单。注意我是如何利用`[useState](https://reactjs.org/docs/hooks-state.html)`钩子来管理组件的本地状态的。

也就这样了！除了 React，不需要其他依赖。提醒一下，这是我一直在玩的一个系统，我自己也还在学习如何使用钩子，尤其是在管理状态的时候。正如我之前提到的，当涉及到大规模应用程序时，我会说 Redux 可能是一个更好的选择，但是我鼓励您探索钩子的世界，以及它们如何允许您以多种方式管理应用程序的状态！

感谢您的倾听。

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/react) [## 学习 React -最佳 React 教程(2019) | gitconnected

### 排名前 49 的 React 教程-免费学习 React。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/react)