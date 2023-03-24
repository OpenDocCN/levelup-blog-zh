# 创建我们自己的 Redux 中间件

> 原文：<https://levelup.gitconnected.com/creating-our-own-redux-middleware-4b0aaed8a527>

![](img/37e5d0ba5294b446549f1ce6ee5a24b1.png)

照片由[威利·芬伯格](https://unsplash.com/@willie_f?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有了 Redux，我们可以用它在 JavaScript 应用程序的中央位置存储数据。它可以单独工作，当与 React-Redux 结合使用时，它也可以作为 React 应用程序的一个流行的状态管理解决方案。

在本文中，我们将看看如何创建我们自己的 Redux 中间件。

# 中间件

中间件是一些代码，我们可以把它放在分派动作和动作到达缩减器之间。

为了简洁地做到这一点，我们定义了一个函数，该函数以`store`为参数，返回一个以`next`为参数的函数，该函数与`dispatch`函数相同，然后返回一个以`action`为参数的函数，并返回`next(action)`。

我们可以这样做:

```
import { createStore, applyMiddleware } from "redux";function countReducer(state = 0, action) {
  switch (action.type) {
    case "INCREMENT":
      return state + 1;
    case "DECREMENT":
      return state - 1;
    default:
      return state;
  }
}const logger = store => next => action => {
  console.log("dispatching", action);
  let result = next(action);
  console.log("next state", store.getState());
  return result;
};let store = createStore(countReducer, applyMiddleware(logger));
store.subscribe(() => store.getState());
store.dispatch({ type: "INCREMENT" });
store.dispatch({ type: "DECREMENT" });
```

在上面的代码中，我们有一个`logger`中间件，它是一个接受`store`参数的函数，该参数保存 Redux 存储本身，然后返回一个接受`next`函数的函数，与`store.dispatch`相同。这个函数然后返回一个带有`action`参数的函数，这个参数是我们调用`dispatch`的动作对象。

在取`action`的函数中，首先我们记录`action`，然后我们用`action`调用`next`，然后我们取返回的结果并赋给`result`。

然后我们记录`store.getState()`，并返回`result`。

为了使用我们刚刚定义的`logger`中间件，我们编写:

```
let store = createStore(countReducer, applyMiddleware(logger));
```

然后，当我们运行`dispatch`时，我们将看到记录器的`console.log`输出。

# 用中间件返回非简单对象

我们不必用中间件返回普通对象。

例如，我们也可以如下返回一个承诺:

```
const promiseMiddleware = store => next => action => {
  if (!(action instanceof Promise)) {
    return next(action);
  }
  return (async () => {
    const resolvedAction = await Promise.resolve(action);
    store.dispatch(resolvedAction);
  })();
};
```

在上面的代码中，我们返回了一个承诺，它不是一个简单的对象。

它所做的就是，如果我们向`dispatch`传递一个承诺，它将解析这个承诺，然后调用`store.dispatch`并执行解析后的动作。

当调用`createStore`函数时，我们可以调用`applyMiddleware`函数。

请注意，我们调用了我们的异步函数，我们不只是返回它。我们必须给`store.dispatch`打电话，这样我们行动的价值就会在商店里被设定。

# 定义和使用多个中间件

当我们调用`applyMiddleware`时，我们可以创建和应用多个中间件。为此，我们只需为`applyMiddleware`传递所有的中间件。

例如，如果我们想应用上面例子中的所有中间件，我们可以写:

```
import { createStore, applyMiddleware } from "redux";function countReducer(state = 0, action) {
  switch (action.type) {
    case "INCREMENT":
      return state + 1;
    case "DECREMENT":
      return state - 1;
    default:
      return state;
  }
}const logger = store => next => action => {
  console.log("dispatching", action);
  let result = next(action);
  console.log("next state", store.getState());
  return result;
};const promiseMiddleware = store => next => action => {
  if (!(action instanceof Promise)) {
    return next(action);
  }
  return (async () => {
    const resolvedAction = await Promise.resolve(action);
    store.dispatch(resolvedAction);
  })();
};let store = createStore(
  countReducer,
  applyMiddleware(promiseMiddleware, logger)
);
store.subscribe(() => store.getState());
store.dispatch(Promise.resolve({ type: "INCREMENT" }));
store.dispatch({ type: "DECREMENT" });
```

既然我们有了`promiseMiddleware`，我们现在可以将承诺传递给`dispatch`并在商店中设置值。

因此，我们可以这样写:

```
store.dispatch(Promise.resolve({ type: "INCREMENT" }));
store.dispatch({ type: "DECREMENT" });
```

我们不会得到一个错误。

使用`logger`中间件，我们得到`console.log`输出。

![](img/1fa542f22f6f2b0282431db0b2bc2d34.png)

Mitch Lensink 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以定义中间件来做比 Redux 本身更多的事情。

为此，我们定义了一个以`store`为参数的函数，该函数返回一个以`next`为参数的函数，该函数与`dispatch`函数相同，后者返回一个以`action`为参数的函数，并返回`next(action)`。

该函数必须调用`dispatch`，以便将动作对象传播到缩减器。

我们可以用`applyMiddleware`函数链接中间件，该函数接受一个或多个中间件。