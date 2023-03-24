# 一个友好的承诺指南

> 原文：<https://levelup.gitconnected.com/a-friendly-guide-to-promise-all-68e7cd57b65d>

## 加速您的异步 JavaScript

![](img/674601a94b3c3c1c346469b88b2292e4.png)

对我来说，这似乎是异步 JS 之旅的最后一步。首先你学习[什么是承诺](https://medium.com/@mostlyfocusedmike/promises-promises-understanding-the-basics-of-js-promise-objects-dd5c656f2db4)，它让你[使用 fetch](https://itnext.io/that-data-looks-so-fetching-on-you-understanding-the-js-fetch-api-880eae0c8d25) ，然后你用 async/await 清理[。但是在你的旅程中还有最后一个小问题:同时处理多个承诺。](https://itnext.io/a-beginners-guide-to-async-await-in-javascript-97750bd09ffa)

# 我们能重复一下承诺吗？

假设你想抓住一群特定的用户。你知道数组方法不能和 `[await](https://dev.to/jhalvorson/how-do-i-use-async-await-with-array-map-1h3f)`一起工作，但是你想:“不管怎样，我会遍历它们。”

```
*const* **specificUserIds** = [...];
*for* (*let* **i** = 0; **i** < **specificUserIds**.length; **i**++) {
  *const* **user** = *await* **User**.getOne(**specificUsers**[**i**];
  ...
```

可惜的是，在短绒喊声中， [*没有*](https://eslint.org/docs/rules/no-await-in-loop) `[*await*](https://eslint.org/docs/rules/no-await-in-loop)` [*出现了一个 for 循环！*](https://eslint.org/docs/rules/no-await-in-loop) “然而，这是一个“性能”问题，有时人们过于关心性能，你能忽略这个警告吗？

# 号码

性能牺牲太大，真是*严重*打击。为了演示，让我们假设 api 需要 0.2 秒的响应时间，并且您想要 6 个用户。用`Promise.all`，大概需要 0.2 秒。使用`for`循环？现在需要一秒多的时间，你自己看:

```
*const* **getUser** = (**id**) => *new* **Promise**(
  (**resolve**) => **setTimeout**(() => **resolve**({**id****}**), 200)
);const **specificIds** = [1, 2, 3, 4, 5, 6];*const* **loop** = *async* () => {
  const **users** = [];
  *for* (*let* i = 0; **i** < **specificIds**.length; **i**++) {
    const **user** = *await* **getUser**(**i**);
    **users**.push(**user**);
  }
  **console**.log('*loop done*');
};const **all** = *async* () => {
  *const* **promises** = **specificIds**.map((**id**) => getUser(id));
  *const* **users** = *await* Promise.all(**promises**);
 **console**.log('*.all done*');
};**loop**();
**all**();
```

除非每个连续的承诺**需要**前一个解析值，否则您应该使用`Promise.all`。

# 为什么会快这么多？

`await`的伟大之处也在于它的失败:它让我们的代码*等待*:

```
*const* **user** = *await* **User**.getOne(**params**.userId);
*const* **username** = **user**.name;
```

在`user`对象解析之前，我们实际上*不能*分配用户名。在这种情况下，我们*需要*等待，这是逻辑的一部分。然而，当我们从数据库加载一群不相关的用户时，情况就不一样了。在加载用户 2 之前，没有任何实际理由等待用户 1 完全加载。这意味着我们可以解决*并行*中的承诺，整个事情只需要最慢的承诺，而不是所有承诺的总和。

# 承诺.一切都解释清楚了

我们在前面看到了它的作用，但是`Promise.all`背后的思想是，我们将所有被调用的，但是 ***未解析的*** 承诺捆绑到一个数组中，然后`.all`获取它们，并在*并行*中等待它们的所有解析，直到最后按照**原始数组的顺序解析一个数组的值。**

```
*1|* const **specificUserIds** = [1, 3, 4, 7];
*2|* 
*3| const* **promises** = **specificUserIds**.map(
**4|**   **id** => **User**.getOne(**id**),
*5|* );
*6|* 
*7|* const **specificUsers** = *await* **Promise**.all(**promises**);
// specificUsers is:
// [
//   { id: 1 },
//   { id: 3 },
//   { id: 4 },
//   { id: 7 },
// ]
```

记住，我们不是在`line 4`对承诺进行`await`或`.then`，我们只是将调用的承诺传递到我们的`promises`数组中。还有别忘了`await`这个`Promise.all`！

> promises[0]可能不会先 ***解析*** ，但其解析值 ***将始终为*** specificUsers[0]

# 处理拒绝

我们有 6 个承诺，但如果一个(或更多)拒绝呢？整个事情失败了:

```
const promises = [
  new Promise((resolve) => setTimeout(() => resolve('ok'), 100)),
  new Promise((r, reject) => setTimeout(() => reject('bad'), 300)),
]Promise.all(promises)
  .catch(console.log); 
// logs out the string: 'bad'
```

即使被拒绝的承诺在好的之后*解决，`Promise.all`也不在乎。我们只得到*单个*剔除值，需要用一个`.catch` 或者一个`await`和`try/catch`来处理。*

# 接受拒绝

如果我们想要数组，不管失败与否，那么我们需要`[Promise.allSettled](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/allSettled)`。该函数返回一个由*对象、*和`status`键组成的数组:

```
*const* **promises** = [
  *new* **Promise**((**resolve**) => **setTimeout**(() => **resolve**('*ok*'), 100)),
  *new* **Promise**((**r**, **reject**) => **setTimeout**(() => **reject**(new Error('*bad*')), 300)),
]const **values** = **Promise**
  .**allSettled**(promises) 
  .**then**(console.log)
//logs:
//[ 
//  {status: ”fulfilled”, value: ”ok},
//  {status: ”rejected”, reason: Error "bad"} 
//]
```

# 承诺数组到承诺数组

`Promise.all`看起来有点不靠谱，因为它打破了你大概*刚*学完的`await`风格。但是您所做的只是将一堆承诺放入一个数组，然后使用一个特殊的函数来跟踪它们的返回值。每当你发现自己有不连续的承诺时，这是一个完美的工具。

大家编码快乐，

麦克风