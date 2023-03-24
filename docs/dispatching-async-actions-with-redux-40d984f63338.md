# 使用 Redux 调度异步操作

> 原文：<https://levelup.gitconnected.com/dispatching-async-actions-with-redux-40d984f63338>

![](img/3a5672c716e1526fe0a69305f75075f1.png)

照片由 [Quino Al](https://unsplash.com/@quinoal?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有了 Redux，我们可以用它在 JavaScript 应用程序的中央位置存储数据。它可以单独工作，当与 React-Redux 结合使用时，它也是一个受欢迎的 React 应用程序状态管理解决方案。

在本文中，我们将研究如何异步分派动作。

# 异步操作

在 JavaScript 中，很多事情必须异步完成，以避免阻塞主执行线程。

Redux 可以调度异步动作，这样我们就不必总是同步改变状态。

为此，我们使用 Redux-Thunk 中间件。我们可以通过使用 Redux 的`applyMiddleware`函数来应用中间件。

这样，我们可以传入调用`dispatch`而不是普通对象的函数。我们需要这种能力来运行调用`dispatch`的异步动作，并用它填充存储。

我们可以这样做:

```
import { createStore, applyMiddleware } from "redux";
import thunkMiddleware from "redux-thunk";function jokeReducer(state = {}, action) {
  switch (action.type) {
    case "SET_JOKE":
      return action.joke;
    default:
      return state;
  }
}let store = createStore(jokeReducer, applyMiddleware(thunkMiddleware));function fetchJoke() {
  return async dispatch => {
    const response = await fetch("[https://api.icndb.com/jokes/random](https://api.icndb.com/jokes/random)");
    const joke = await response.json();
    dispatch({ type: "SET_JOKE", joke });
  };
}(async () => {
  await store.dispatch(fetchJoke());
  console.log(store.getState().value.joke);
})();
```

在上面的代码中，我们有`jokeReducer`，它和我们之前的代码一样。

然后在创建商店时，我们写道:

```
let store = createStore(jokeReducer, applyMiddleware(thunkMiddleware));
```

与只采取普通对象动作的商店不同的是，我们在`thunkMiddleware`中传递了`applyMiddleware`函数。

`thunkMiddleware`让我们传递函数来分派，而不是传递一个没有函数的普通对象。

在我们传入的函数中，我们可以用普通对象调用`dispatch`来执行同步操作。

然后我们定义了`fetchJoke`函数，它是 or 动作创建器，如下所示:

```
function fetchJoke() {
  return async dispatch => {
    const response = await fetch("[https://api.icndb.com/jokes/random](https://api.icndb.com/jokes/random)");
    const joke = await response.json();
    dispatch({ type: "SET_JOKE", joke });
  };
}
```

在这个函数中，我们返回了一个异步函数，它将 Redux `dispatch`函数作为参数。

然后一旦我们用 Fetch API 得到这个笑话，我们就调用参数中提供的`dispatch`。

这样，Thunk 中间件可以在返回的函数中调用`dispatch`。

我们可以返回任何函数，但是我们需要对异步函数使用 thunks，因为它们不返回普通对象。

普通的动作创建者必须返回普通对象，但是如果我们使用 Redux Thunk 中间件，我们可以返回一个调用`dispatch`和`getState`的函数。

然后，我们调度我们的异步操作，并从中获取值，如下所示:

```
(async () => {
  await store.dispatch(fetchJoke());
  console.log(store.getState().value.joke);
})();
```

我们在上面所做的就是调用`dispatch`,并保证`fetchJoke`函数按照我们上面的定义返回。然后我们使用`store.getState().value.joke`从商店获取笑话的值。

![](img/c9f9bdfb6e0792fa9a49b8e7d9afab37.png)

[真诚媒体](https://unsplash.com/@sincerelymedia?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 在我们的异步动作创建器中分派同步动作之前检查状态

我们可以写一个函数在获取数据之前检查状态。为此，我们可以在保持其他一切不变的情况下进行以下更改:

```
function shouldFetchJoke(state) {
  return !state.value || !state.value.joke;
}function fetchJoke() {
  return async (dispatch, getState) => {
    if (shouldFetchJoke(getState())) {
      const response = await fetch("[https://api.icndb.com/jokes/random](https://api.icndb.com/jokes/random)");
      const joke = await response.json();
      dispatch({ type: "SET_JOKE", joke });
    }
  };
}
```

在上面的代码中，我们定义了`shouldFetchJoke`函数，该函数检查`state.value`和`state.value.joke`以查看值是否已被设置。

然后我们把`fetchJoke`改成叫`shouldFetchJoke`。然后，我们利用`getState`参数获取状态，并检查带有`shouldFetchJoke`的商店中是否已经存在笑话。

如果没有，那么我们就开始理解这个笑话。

# 结论

使用 Redux Thunk 中间件，我们可以将不是普通对象的动作传递给`dispatch`。

要使用它，我们必须用`thunkMiddleware`作为参数来调用`applyMiddleware`。

然后我们可以创建一个动作创建器，它返回一个函数，函数的第一个和第二个参数分别是`dispatch`和`getState`，然后我们可以像往常一样用`getState`得到状态。