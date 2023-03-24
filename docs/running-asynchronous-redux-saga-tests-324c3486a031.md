# 运行异步 redux-saga 测试

> 原文：<https://levelup.gitconnected.com/running-asynchronous-redux-saga-tests-324c3486a031>

## 显而易见的解释

![](img/2c81bc74a7026691f1714ecdfc4da493.png)

米哈伊尔·瓦西里耶夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

有几种方法可以为您的 React 应用程序编写异步逻辑。我有经验的两个是 [redux-thunk](https://redux.js.org/usage/writing-logic-thunks) 和 [redux-saga](https://redux-saga.js.org/) 。我个人更喜欢 redux-saga 而不是 redux-thunk。对我来说，它比 redux-thunk 更容易阅读和维护代码。

一开始，为 redux-saga 编写测试可能是一个挑战。令我惊讶的是，redux-saga 医生实际上很擅长解释如何做。

> [测试 saga 有两种主要方法:逐步测试 saga 生成器功能或运行完整的 saga 并断言副作用。](https://redux-saga.js.org/docs/advanced/Testing)

我将重点测试整个传奇。这个例子足够清楚了。

你有一个这样的传奇:

```
function* callApi(url) {
  const someValue = yield select(somethingFromState);
  try {
    const result = yield call(myApi, url, someValue);
    yield put(success(result.json()));
    return result.status;
  } catch (e) {
    yield put(error(e));
    return -1;
  }
}
```

你的测试是这样的:

```
const dispatched = [];

const saga = runSaga({
  dispatch: (action) => dispatched.push(action),
  getState: () => ({ value: 'test' }),
}, callApi, 'http://url');expect(myApi).toHaveBeenCalledTimes(1)
```

它就这样工作了，至少我是这么认为的。

当你引入第二个异步调用时，问题就来了，比如额外的 API 调用。

所以如果你的传奇看起来像这样:

```
function* callApi(url) {
  const someValue = yield select(somethingFromState);
  try {
    const result = yield call(myApi, url, someValue);
    const anotherResult = yield call(myApi, url, someValue);
    yield put(success(result.json()));
    yield put(success(anotherResult.json()));
    return result.status;
  } catch (e) {
    yield put(error(e));
    return -1;
  }
}
```

你的测试是这样的:

```
const dispatched = [];

const saga = runSaga({
  dispatch: (action) => dispatched.push(action),
  getState: () => ({ value: 'test' }),
}, callApi, 'http://url');expect(myApi).toHaveBeenCalledTimes(2)
```

您的测试将会失败，因为测试不会等待`runSaga`函数完成完整的运行。

乍一看，这些文档似乎没有任何关于如何在一个 saga 中运行多个异步调用的具体内容。

但是在文档中稍微滚动一下，你会看到这段代码:

```
const result = await runSaga({
  dispatch: (action) => dispatched.push(action),
  getState: () => ({ state: 'test' }),
}, callApi, url).toPromise();
```

这是在一个 saga 中运行多个异步调用的关键。

有了这些知识，去 redux-saga [API 参考](https://redux-saga.js.org/docs/api)搜索`toPromise`。您会发现`toPromise`可以与`runSaga`一起使用，并将为您正在测试的传奇返回一个承诺解决/拒绝值。

如果您现在像这样断言测试:

```
expect(myApi).toHaveBeenCalledTimes(2)
```

测试不会再失败了🎉