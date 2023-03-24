# Typescript 中 React Redux 的容器模式

> 原文：<https://levelup.gitconnected.com/a-container-pattern-for-react-redux-in-typescript-e138a6ac33ca>

## 通过学习如何将组件连接到存储，同时将呈现与状态管理逻辑隔离开来，来增强您的 React 游戏

![](img/5ad63a235c07590e772e06c9197fd4ac.png)

即使您相对来说是编程新手，您也可能听说过容器模式。通过将业务逻辑从用户界面中分离出来，您可以使布局组件**可重用**。在这个过程中，你还必须定义这些组件的道具看起来像什么，提高代码的**清晰度**，并在道具不匹配时让代码**大声打断**。

在本文中，我将向您展示如何按照 Redux 和 Typescript 的类似容器的模式来构造组件，以使它们更易于阅读。这将很容易和立即加强你的反应游戏。

# 要求

假设您已经[建立了一个节点开发环境](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/development_environment)，这里列出了您成功完成本教程所需的一切。

## 用类型脚本反应应用程序

如果你是 React 新手，我建议[在继续之前，看一下文档](https://create-react-app.dev/docs/adding-typescript/)并学习如何用 create-react-app 创建你的第一个 React 应用程序。我将在本教程中输入我的组件，所以了解一点 Typescript 不会有什么坏处。

## Redux 商店

我还假设您已经掌握了一些关于如何使用 Redux 跟踪应用程序状态的知识。我不会在这里详细介绍如何创建你的商店和 reducers。如果您是 Redux 的新手，首先看看如何创建您的商店并将其连接到您的组件。

# 履行

制作一个简单的布局组件，并让它大声清晰地声明它的属性。

```
interface **IProps** {
    **applicationProperty**: string,
    **externalState**: string,
    **functionWithDispatch**: () => void,
}

const **ComponentLayout**: React.FunctionComponent<**IProps**> = props => {
    return <div onClick={props.**functionWithDispatch**}>
        <h1>App state property: {props.**applicationProperty**}</h1>
        <h3>External state: {props.**externalState**}</h3>
    </div>
}

exportdefault **ComponentLayout**
```

现在已经定义了布局，可以用一个连接到 Redux 存储的容器包装它。

首先，创建连接器，并使用 ConnectedProps 提取其类型:

```
const mapStateToProps = (state: MyApplicationState) => {
    return {
        **someProperty**: state.someReducer.someKey,
        // Other properties to be obtained from your state
    }
}

const mapDispatchToProps = (dispatch: Dispatch<AnyAction>) => {
    return {
        **someFunction**: () => {
            dispatch(someAction())
        },
        // Other functions with dispatch
    }
}

const connector = *connect*(mapStateToProps, mapDispatchToProps)
export type **ContainerProps** = **ConnectedProps**<typeof connector>
```

然后，创建一个与连接器具有相同 Props 需求的组件，并让它包装您的布局，传递您映射到连接器上的属性。

```
const **ComponentContainer**: React.FunctionComponent<**ContainerProps**> = props => {

    const [**extState**, setExtState] = useState("default")

    return <ComponentLayout
        externalState={**extState**}
        applicationProperty={props.**someProperty**}
        functionWithDispatch={props.**someFunction**}/>

}

export const ***Component*** = connector(**ComponentContainer**)
```

# 好了

您的布局组件现在完全独立于您的外部逻辑和应用程序状态，并且可以很容易地重用。此外，所有的呈现逻辑现在都与数据获取事务隔离开来，这些事务既可以在容器中完成，也可以在调度的操作中完成。现在可以在呈现函数中直接调用包装的组件:

```
ReactDOM.*render*(
    <React.StrictMode>
        <Provider store={store}>
            **<Component/>**
        </Provider>
    </React.StrictMode>,
    *document*.getElementById('root')
);
```