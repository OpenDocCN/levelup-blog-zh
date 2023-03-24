# React 提示— JWT 和 Redux，在 setState 之后运行代码，并同步属性和状态

> 原文：<https://levelup.gitconnected.com/react-tips-jwt-and-redux-run-code-after-setstate-and-set-body-styles-893d95209483>

![](img/ed70abeacdeffcea6ed40012ad2e0469.png)

赫伯特·戈特施在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 是一个用于创建 web 应用程序和移动应用程序的流行库。

在本文中，我们将了解一些编写更好的 React 应用程序的技巧。

# setState 比 onChange 落后一步

为了确保我们在 React 组件中按顺序设置我们的状态，我们可以传入一个回调作为第二个参数`setState`。

当第一个参数中的值被设置为新的状态值时，将运行回调。

例如，我们可以写:

```
handleChange(e) {
  this.setState({ name: e.target.value }, this.handleSubmit);
}
```

我们用输入的值更新`name`状态。

然后我们调用`handleSubmit`方法，这是一个在`name`状态被设置后被调用的方法。

在函数组件中，我们可以写:

```
useEffect(() => {
  //...
}, [name]);
```

当`name`状态改变时运行回调。

# 如何使用 React 挂钩将道具同步到状态

为了将属性值同步到函数组件中的状态，我们可以使用`useEffect`钩子来观察属性值的变化。

然后运行我们传入钩子的回调函数，当 prop 值改变时。

例如，我们可以写:

```
import React,{useState , useEffect} from 'react';const Persons = ({ name }) =>  {
  const [nameState , setNameState] = useState(name); useEffect(() => {
    setNameState(name);
  }, [name]) return (
    <div>
       <p>My name is {props.name}</p>
       <p>My name is {nameState}</p>
    </div>
  )
}
```

我们有带`name`道具的`Persons`组件。

我们用`useState`钩子设置初始状态，向它传递我们的属性值。

然后我们添加了一个`useEffect`钩子来监听`name`值的变化。

如果它改变了，回调就会运行。

我们调用`setNameState`用最新的`name`值更新`nameState`。

然后我们显示属性和状态值。

# 使用 Redux 刷新 JSON Web 令牌

要使用 redux 刷新 JSON web 令牌，我们必须创建一个 thunk 并使用 Redux thunk 中间件。

为此，我们可以写:

```
import { createStore, applyMiddleware, combineReducers } from 'redux';
import thunk from 'redux-thunk';
import { reducer } from './reducer';const rootReducer = combineReducers({
  reducer
  //...
})
const store = createStore(rootReducer, applyMiddleware(thunk));
```

在我们的商店中应用 thunk 中间件。

然后，我们可以通过输入以下内容来创建我们的减速器:

`reducer.js`

```
const initialState = {
  fetching: false,
};export function reducer(state = initialState, action) {
  switch(action.type) {
    case 'LOAD_FETCHING':
      return {
        ...state,
        fetching: action.fetching,
      }
   }
}
```

我们有一个存储`fetching` 状态的减速器。

然后我们可以通过写作来创造我们的思维:

```
export function loadData() {
  return (dispatch, getState) => {
    const { auth, isLoading } = getState();
    if (!isExpired(auth.token)) {
      dispatch({ type: 'LOAD_FETCHING', fetching: false })
      dispatch(loadProfile());
    } else {
      dispatch({ type: 'LOAD_FETCHING', fetching: true })
      dispatch(refreshToken());
    }
  };
}
```

我们创建了一个 thunk 来调用`dispatch`并获取状态来检查我们是否需要获取带有刷新令牌的令牌。

thunk 返回一个函数，用`dispatch`函数调度动作，用`getState`函数获取状态。

然后，我们可以通过编写以下代码来调度 thunk:

```
componentDidMount() {
  dispath(loadData());  
}
```

在我们的组件中。

还有 redux-api-middleware 包，我们可以将它作为中间件添加到 React 应用程序中。

我们可以通过运行以下命令来安装它:

```
npm i redux-api-middleware
```

然后我们可以用它来创建一个动作:

```
import { createAction } from `redux-api-middleware`;

const getToken = () => createAction({
  endpoint: 'http://www.example.com/api/token',
  method: 'GET',
  types: ['REQUEST', 'SUCCESS', 'FAILURE']
})export { getToken };
```

`type`是一个字符串，表示发生了什么动作。

我们还传入带有 URL 的`endpoint`，以及带有请求方法的`method`。

我们可以通过编写以下代码来添加中间件:

```
import { createStore, applyMiddleware, combineReducers } from 'redux';
import { apiMiddleware } from 'redux-api-middleware';
import reducers from './reducers';

const reducer = combineReducers(reducers);
const createStoreWithMiddleware = applyMiddleware(apiMiddleware)(createStore);

export default function configureStore(initialState) {
  return createStoreWithMiddleware(reducer, initialState);
}
```

我们导入`reducers`和中间件，并将它们合并到我们的商店中。

然后我们可以使用我们用`createAction`创建的动作。

![](img/43a2d4deb4c80c0d63195a679d053642.png)

照片由[泰勒](https://unsplash.com/@xoutcastx?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

有几种方法可以用 Redux 获得令牌。

另外，我们可以在`setState`完成后运行代码。

我们可以观察 prop 值的变化，并相应地更新状态。