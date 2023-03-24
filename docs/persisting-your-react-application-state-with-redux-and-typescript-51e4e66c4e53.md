# 用 Redux 和 Typescript 持久化 React 应用程序状态

> 原文：<https://levelup.gitconnected.com/persisting-your-react-application-state-with-redux-and-typescript-51e4e66c4e53>

## 如何使用 redux-persist 管理应用程序状态并将其保存到任何类型的存储中

![](img/3e30bd1d92a324f820cb7e8753c05fe2.png)

如果您曾经开发过 React 应用程序，并选择使用 Redux 来维护您的状态，在某些时候，您可能会考虑将它保存在本地存储中，以便您的用户可以在关闭浏览器或离开您的网站之前总是返回到相同的位置。redux-persist 包使这变得非常容易，在本文中我将向您展示如何做到这一点。

# 要求

假设你已经[建立了一个节点开发环境](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/development_environment)，这里列出了你成功完成本教程所需要的一切。

## 用类型脚本反应应用程序

如果你是 React 新手，我建议[看一下文档](https://create-react-app.dev/docs/adding-typescript/)，在继续之前学习如何用 create-react-app 创建你的第一个 React 应用程序。我将在本教程中输入我的组件，所以了解一点 Typescript 不会有什么坏处。

## Redux 商店

我还假设您已经掌握了一些关于如何使用 Redux 跟踪应用程序状态的知识。如果您是 Redux 的新手，首先看看如何创建您的商店并将其连接到您的组件。

## redux-持久化

这是我们将用来为我们做所有存储和检索的包。

```
npm install redux-persist @types/redux-persist --save
```

# 国家

出于本教程的目的，我们将假设我们的应用程序状态具有由以下接口定义的结构:

```
export interface **IAppState** {
    authentication?: **IAuthenticationState**,
    extras: {
        loading: boolean,
    }
}export interface **IAuthenticationState** {
    token: string,
    expires: number
    user?: {
        name: string,
        email: string,
    }
}
```

身份验证密钥包含用于对我们感兴趣的某种服务进行身份验证的凭证。在持久化我们的状态时，我们将尽量不在本地持久化用户的个人信息，而是在每次状态恢复时获取它。额外的内容只包含不稳定和不相关的信息，我们不想保留这些信息。

# 持久性还原剂

要将您的状态保存到本地存储器，您必须通过“ *persistReducer* ”功能运行您的 Reducer。除了缩减器本身之外，它只需要一个参数，即定义您希望如何持久化状态以及持久化状态的哪些部分的配置参数。我们的看起来会像这样:

```
import ***storage*** from "redux-persist/lib/storage";export const config = {
    **key**: 'root',
    **storage**: storage,
    **blacklist**: ['extras'],
    **transforms**: [*TransformCredentials*]
};
```

*   **key** :你的状态应该持久化的顶层 key。在这种情况下，“根”意味着整个状态被持久化。
*   **存储**:表示使用什么样的存储，有很多选项可供选择，你可以在这里查看[。](https://github.com/rt2zz/redux-persist)
*   **黑名单**:你可以选择跳过你所在州的子树，只需将它们的键添加到黑名单中。在这种情况下，我们选择不存储我们的 extras，这意味着它们的初始状态将总是重新开始。白名单功能也是可用的，请务必查看他们的文档。
*   **转换**:您可以将转换函数应用于入站和出站状态，这对于持久化通常不可 JSON 序列化的类型特别有用。

我们还使用了一个 *TransformCredentials* 函数来隐藏本地存储中的用户信息，并在每次状态恢复时获取这些信息。使用一对修改给定键的入站和出站状态的函数来创建转换。

```
import {**createTransform**} from "redux-persist";export const *TransformCredentials* = **createTransform**(
    (**inboundState**: IAuthenticationState, key): any => {
        return {
            ...inboundState,
            user: undefined,
        };
    },
    (**outboundState**: any, key) => {
        return {
            ...outboundState,
            user: fetchUser(outboundState.token)
        };
    },
    {
        **whitelist**: ['authentication']
    }
);const fetchUser = (token : string) => {
    // Fetch the user data from some service
    // ...
    return user;
}
```

*   **inboundState** :持久化时应用于状态的函数。
*   **outboundState** :应用于任何对象的函数，当它在返回到你的状态对象的过程中被持久化。
*   **白名单**:应该应用转换的键
*   **createTransform** :用于将转换函数放在一起的函数

现在配置已经就绪，您可以持久化您的应用程序的根缩减器，并使用它的持久化版本创建您的存储:

```
import {**persistReducer**} from "redux-persist";const rootReducer = combineReducers<IAppState>({
    authentication: myAuthenticationReducer,
    extras: myExtrasReducer
});const persisted = **persistReducer**(rootConfig, rootReducer);const*store = createStore*(persisted);
```

# 持久存储

既然已经正确配置了持久化缩减器，那么是时候持久化我们刚刚创建的存储了。简单到将它传递给一个“ *persistStore* ”函数，并将其作为道具提供给一个 PersistGate 组件，您可以从 redux-persist 包中使用它。

```
import {PersistGate} from "redux-persist/integration/react";
import {persistStore} from "redux-persist";//...const persistor = **persistStore**(store);

ReactDOM.***render***(
    <React.StrictMode>
        <Provider store={store}>
            <PersistGate loading={null} persistor={persistor}>
                ...
            </PersistGate>
        </Provider>
    </React.StrictMode>,
    ***document***.getElementById('root'));
```

# 全部完成！

好了，现在 *PersistGate* 中组件发生的所有状态变化都将根据我们提供的配置自动保存到本地存储中。

还有许多其他的设置和方法可以定制和微调您的持久性行为。请务必查看完整的文档，看看哪一个更适合您的偏好和要求。

## 资源

*   [Redux 文档](https://redux.js.org/introduction/getting-started)
*   [redux-persist 存储库](https://github.com/rt2zz/redux-persist)