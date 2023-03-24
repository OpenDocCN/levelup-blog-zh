# 使用 TypeScript 还原挂钩

> 原文：<https://levelup.gitconnected.com/redux-hooks-with-typescript-4fba08f160ad>

![](img/60fd18282da9ba916dc246df98404c40.png)

照片由[安朵斯·瓦斯](https://unsplash.com/@wasdrew?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 简介:

本教程假设您对 NPM、JavaScript、React、Redux 和 TypeScript 有基本的了解，主要是为了帮助那些想将新的 Redux 挂钩集成到 React TypeScript 项目中的人。

它旨在对初学者相对友好，所以如果您已经熟悉使用 create-react-app 启动一个新的 React 项目，或者建立一个 Redux 商店，请随意跳过。

# 开始使用:

通过键入以下命令，使用 TypeScript 创建一个新的 React 项目:

```
npx create-react-app redux-hooks-with-typescript --template typescript
```

通过导航到新创建的目录并运行以下命令，检查以确保所有内容都已正确安装:

```
cd redux-hooks-with-typescript
npm start
```

如果你的浏览器打开一个新的标签到 localhost:3000，你会看到旋转的 React 标志，那么我们就可以开始了。

# 依赖关系:

要安装我们将需要的所有依赖项，请在您的终端中键入以下行:

```
npm install redux react-redux @types/react-redux redux-devtools-extension
```

每个人都有自己喜欢的文件夹结构，所以很明显你可以按照自己的想法来组织东西，但一般来说，我的方法通常是这样的:

我会在 *src* 文件夹里做一个 *redux* 文件夹，把我所有的 redux 东西放在同一个地方。然后在那个 *redux* 文件夹中，我将创建三个新文件夹，分别命名为 *actions* 、 *reducers* 和 *store* 。所以它看起来会像这样:

```
--src
    --redux
        --actions
        --reducers
        --store
```

# 1)商店:

在 *store* 文件夹中，触摸一个名为 **store.ts** 的新文件，打开它并键入以下代码:

```
import { createStore } from 'redux'
import rootReducer from '../reducers/rootReducter'
import { devToolsEnhancer } from 'redux-devtools-extension'const store = createStore(rootReducer, devToolsEnhancer({}));export default store;
```

不要担心 *rootReducer* ，我们马上就要创建它了。

我们现在做的是使用“redux”包中的 *createStore* 函数来设置我们的 redux 商店。我们还将 *devToolsEnhancer* 添加到商店中，这样我们就可以在浏览器中看到所有可爱的 Redux devtools。

> 你需要在 chrome 中安装 Redux dev 工具，以便在浏览器中使用它们。您可以在此处安装它们:
> 
> [https://chrome . Google . com/web store/detail/redux-dev tools/lmhkpmbekcpmnklioeibfkpmmfibljd？hl=en](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en)

我们传递给 *devToolsEnhancer* 的空对象只是表明我们没有为 redux 开发工具使用任何特定的选项。我们只需要传递一些东西给它，因为函数需要一个对象，否则就不能工作。

下一步是向我们的 React 应用程序提供对该商店的访问。

# 2)提供商:

打开 src 目录中的 **index.tsx** 文件，从“react-redux”包中导入提供者组件，以及我们刚刚创建的新的 **store.ts** 文件。

> 如果您想避免“商店/商店”导入，只需键入，您也可以将**商店. ts** 文件重命名为**索引. ts** 。/redux/store '

然后用<provider>组件包装<app>组件，并将我们刚刚创建的**存储**传递给<提供者/ >组件需要的存储参数。</app></provider>

您的代码应该如下所示:

```
import { Provider } from 'react-redux'
import store from './redux/store/store'...<Provider store={store}>
    <App/>
</Provider>...
```

# 3)减速器:

在 *reducers* 文件夹中，我们继续创建三个不同的文件，这样我们就可以看到如何将 reducers 合并成一个 *rootReducer。*分别命名为 **nameReducer.ts** 、 **countReducer.ts、**和 **rootReducer.ts** 。

在 **nameReducer.ts** 中输入以下代码:

```
import { NameActions } from "../actions/nameActions"type NameState = {
name: string;
}const initialState: NameState = {
name: '',
}const NameReducer = (state: NameState = initialState, action: NameActions) => {
    switch(action.type) {
        case 'SET_NAME':
            return {
                ...state,
                name: action.payload,
            }
        default:
            return state;
    }
}export default NameReducer;
```

在 **countReducer.ts** 中输入以下代码:

```
import { CountActions } from "../actions/countActions";type CountState = {
    count: number;
}const initialState: CountState = {
    count: 0,
}const countReducer = (state: CountState = initialState, action: CountActions) => {
    switch(action.type) {
        case 'DECREMENT':
            return {
                ...state,
                count: state.count - 1,
            }
        case 'INCREMENT':
            return {
                ...state,
                count: state.count + 1,
            }
        default:
            return state;
    }
}export default countReducer;
```

在前两个代码块中，我们刚刚设置了两个非常基本的 Redux reducers。你会注意到它们都有非常相似的结构:我们在顶部声明 TypeScript 类型，后面是用于每个特定 reducer 的实际初始状态对象，然后 reducer 函数本身只是一个接受两个参数的函数——一个**状态**对象(我们刚刚在上面定义了)和一个**动作**对象。

> 当我们分派动作时，action 对象被提供给 reducer，您将在下面看到。我们还将为下面的操作定义 TypeScript 类型。我保证，一切都会联系起来的！

然后我们在**动作上运行一个 switch 语句，并根据我们匹配的案例返回一个新的对象。该新对象将替换先前保存在**状态**变量中的对象。这就是 Redux 更新状态的方式。这也是我们通常做逻辑计算之类的地方。只要我们从每个 case 中返回一个对象，Redux 就会很高兴。**

接下来，我们只需要制作一个 reducer 来将所有的 reducer 连接成一个，因为这是 Redux 喜欢的方式(还记得我们在设置 **store.ts** 文件时—*createStore()*函数只接受一个 reducer 的单个参数)。我们将称这个新的减速器为 **rootReducer。**

打开 **rootReducer.ts** 并输入以下代码:

```
import { combineReducers } from 'redux'
import countReducer from './countReducer'
import nameReducer from './nameReducer';const rootReducer = combineReducers({
    count: countReducer,
    name: nameReducer,
})export type AppState = ReturnType<typeof rootReducer>export default rootReducer;
```

在这个代码块中，我们使用了 *combineReducers()* 函数将缩减器组合成一个单独的 **rootReducer** 。我们只是给这个函数传递一个带有键的对象，我们希望用这些键来标识每个缩减器。我选择用“计数”来表示 **countReducer** ，用“名称”来表示 **nameReducer，**，但是您可以随意使用任何对您有意义的名称。

然后我们还定义了一个名为 **AppState** 的新 TypeScript 类型，并将其定义为新的 **rootReducer 的返回类型。**这只是定义 **AppState** 的一种简单方法，当我们将来添加更多的 reducers 时，AppState 会自动更新。

# 4)行动:

在 *actions* 文件夹中，添加两个名为 **countActions.ts** 和 **nameActions.ts** 的文件

打开 **countActions.ts** ，输入以下代码:

```
export interface IIncrementCountAction {
    readonly type: 'INCREMENT';
}export interface IDecrementCountAction {
    readonly type: 'DECREMENT';
}export type CountActions =
| IIncrementCountAction
| IDecrementCountAction
```

打开 **nameActions.ts** 并输入以下代码:

```
export interface ISetNameAction {
    readonly type: 'SET_NAME';
    payload: string;
}export type NameActions =
| ISetNameAction
```

在这里，我们只是定义了可以分派给我们的 reducer 的所有动作类型(当我们分派一个动作时，您将在下面看到， **action** 对象被传递给我们的每个 reducer)。这是我们定义物体形状的地方。

> 每个动作都必须有一个“type”键，因为我们设置的 reducers 将使用这个“type”作为 switch 语句，来决定采取哪个动作。(希望事情现在开始变得更有意义了！)

我们还将所有的动作接口合并成一个单一的类型脚本类型。当我们开始分派动作时，这将非常方便，您将在下一节看到这一点。

> 对于熟悉 Redux 的人来说，您会注意到我没有创建任何动作创建器或动作常量——这是有意的。通过使用 TypeScript 类型，您可以确保 redux 动作类型总是被正确使用，并且不包含任何拼写错误，您甚至可以访问它们的 IntelliSense 完成功能！所以动作创建器和常量有些不必要，但是，如果你真的喜欢动作创建器的工作流程，可以像往常一样随意合并它们。

# 5)连接一切:

打开 *src* 文件夹中的 **App.tsx** 组件，将文件中的所有代码替换为以下代码:

```
import React, { Dispatch } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { AppState } from './redux/reducers/rootReducter';
import { CountActions } from './redux/actions/countActions';
import { NameActions } from './redux/actions/nameActions';function App() {
    const { count } = useSelector((state: AppState) => state.count);
    const { name } = useSelector((state: AppState) => state.name);
    const countDispatch = useDispatch<Dispatch<CountActions>>();
    const nameDispatch = useDispatch<Dispatch<NameActions>>(); const handleIncrement = () => {
        countDispatch({type: 'INCREMENT'});
    } const handleDecrement = () => {
        countDispatch({type: 'DECREMENT'});
    } const handleSetName = 
        (e: React.ChangeEvent<HTMLInputElement>) => {
        nameDispatch({type: 'SET_NAME', payload: e.target.value})
    } return (
        <div>
            <div>
                <button onClick={handleIncrement}>+</button>
                {count}
                <button onClick={handleDecrement}>-</button>
            </div>
            <div>
                <input type="text" onChange={handleSetName}/>
                {name}
            </div>
        </div>
    );
}export default App;
```

所以，我们终于到了 Redux 挂钩！

## 使用选择器:

首先，我们使用 **useSelector** 钩子从 Redux 获取 **AppState** 。它使用对象析构语法，很像其他 React 钩子。

**useSelector** 钩子期待一个回调函数，并将来自 **rootReducer** 的**状态**对象提供给那个回调函数(这就是我们在 **rootReducer.ts** 文件中定义的 **AppState** 类型派上用场的地方)。

回调函数非常简单——它只返回那个**状态**对象，或者在我们的例子中，返回对 **countReducer 中的**状态**对象的引用的 **state.count，**。**

因此，我们只需从返回的对象中析构计数**并像平常一样在组件中使用它。**

> 记得我们在 **rootReducer** 中将 **countReducer** 重命名为“计数”。

我们以非常相似的方式从**名称缩减器**中访问**名称**。

## 使用补丁:

接下来，我们使用 **useDispatch** 钩子来设置我们的动作调度程序。没有 TypeScript，这个钩子的语法简单得令人难以置信:

```
const dispatch = useDispatch();
```

但是——通过使用 TypeScript，我们获得了 Redux 操作的类型检查和智能感知自动完成的全部能力！TypeScript 语法如下所示:

```
const countDispatch = useDispatch<Dispatch<CountActions>>();
const nameDispatch = useDispatch<Dispatch<NameActions>>();
```

**分派**类型实际上来自 React，所以我们不用担心那个，然后 **CountActions** 来自我们上面在 **countActions.ts** 中定义的动作类型。

要使用分派，我们只需传入一个我们在 **countActions.ts** 和 **nameActions.ts** 文件中定义的 action 对象(在这里，您还可以使用 action creator 函数和常量，如老派的 Redux，但使用 TypeScript，这有点多余)。该语法如下所示:

```
...countDispatch({type: "INCREMENT"});...nameDispatch({type: "SET_NAME", payload: e.target.value})...
```

现在一切终于都应该设置好了！运行 npm start 并查看结果。打开开发者工具窗口中的 *Redux* 选项卡，查看所有工作。

# 结论:

我希望这能帮助你更好地理解带有 TypeScript 的 Redux 挂钩！我知道这看起来像是做了很多准备工作，但是你做得越多，事情就越简单，Redux 为大型项目提供的便利是绝对难以置信的，TypeScript 添加的类型检查和智能感知使整个事情感觉更加直观。我已经完全爱上了这个工作流程，我希望你也一样！

我相信这篇文章有很多方法可以改进或扩展，所以请随意分享任何想法/问题/顾虑。我和其他人一样喜欢学习新东西！