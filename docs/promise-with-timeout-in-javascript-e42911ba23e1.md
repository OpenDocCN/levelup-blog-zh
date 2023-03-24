# Javascript 中带有超时的承诺

> 原文：<https://levelup.gitconnected.com/promise-with-timeout-in-javascript-e42911ba23e1>

![](img/fbe0b60c2a4179774458206dcbd83a92.png)

照片由 [Marcelo Leal](https://unsplash.com/@marceloleal80?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有时我们发出的 API 请求没有得到服务器的任何响应。我们创建一个加载状态向用户展示，然后它永远不会消失。现在我们陷入了一种状态，表明应用程序正在等待什么...

在这种情况下，最好拒绝这个请求，并告诉用户有问题。解决这个问题的一个方法是创建一个带有超时的承诺。

那么，我所说的“超时承诺”是什么意思呢？为了继续下去，有必要知道什么是承诺以及它是如何工作的，我在这里写了一篇关于这个的文章。

如果发送了请求，我们就等待响应。如果回应给了我们想要的东西，也许是一些数据，那就是成功的回应。我们还可以得到一个错误响应，这可能是一个类似于“你不允许这样做”的文本。这很好，因为我们仍然得到一个响应，有了它，我们实际上有了一个事件，可以触发一个动作。

如果我们发送了一个请求却没有得到响应怎么办？然后我们只是等待，等待不是一个事件，所以我们不能从中触发一个动作。因此，我们需要设置一个计时器来告诉我们，我们已经等待了足够长的时间，是时候做些别的事情了。这就是我所说的“有时限的承诺”。让我们看看如何做到这一点。

有几种方法可以解决这个问题，但我认为最简单的方法是使用 [Promise.race](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race) 和 setTimeout。

```
const promiseWithTimeout = ***promise*** => {
  let **timeoutId**; 
  const **timeoutPromise** = new Promise((*_*, *reject*) => {
    **timeoutId** = setTimeout(() => {
      reject(new Error('Request timed out'));
    }, 4000);
  }) return {
    promiseOrTimeout: Promise.race([***promise***, **timeoutPromise**]),
    timeoutId,
  };
};
```

我们声明函数，函数带一个名为`**promise**` 的参数，表示我们要传递的东西是一个 JavaScript 承诺。创建了一个名为`**timeoutId**`的变量，它将用于获取对超时的引用。

于是一个承诺产生了，它被称为`**timeoutPromise**`。不会使用 resolve 部分，所以我用了一个“`_`”来表示。

该部分`setTimeout(()=>{reject(new Error(‘Request timed out’))}, 4000)`使用 **setTimeout** 创建一个超时，该超时在 4000ms 后接受一个触发**拒绝**承诺的回调。

函数`**promiseWithTimeout**`返回一个具有两个属性的对象，`**promiseOrTimeout**`和`**timeoutId**`。`promiseOrTimeout`的值是`Promise.race`。

Race 函数接受一系列承诺。在这种情况下，它是两个承诺，并将返回第一个解决或拒绝的承诺的值。这意味着如果我们将我们的请求(包装在承诺中)作为这个函数的输入参数，我们将在请求和超时之间进行一场比赛。

如果响应返回，我们将使用响应，但是如果超时的回调被触发，`timeoutPromise`将执行它的`reject`。拒绝被设置为创建一个错误，稍后我们可以使用一个 try-catch 并以一种简单的方式处理拒绝。

下面的例子说明了如何解决请求没有得到响应的问题。

```
let isLoading = false;
let data = [];const **fetchData** = async url => {
  const { **promiseOrTimeout**, **timeoutId** } =          
  **promiseWithTimeout**(fetch(url)); try {
    isLoading = true
    const result = await **promiseOrTimeout**;
    data = await result.json();
  } catch (error) { 
    console.log(error);
    isLoading = false;
    data = [];
  } finally {
    clearTimeout(**timeoutId**);
  }
};**fetchData**('api/some/endpoint');
console**.**log(data, isLoading);
```

如果`catch`中的`console.log(error)`记录“错误:请求超时”，那么是我们被拒绝的承诺触发了错误。这里，超时的处理方式与所有其他可能发生的错误相同。

## 概括起来

我已经讨论了如何使用`Promise.race`解决陷入挂起状态的请求。

在撰写本文时，有一个名为 [AbortController](https://developer.mozilla.org/en-US/docs/Web/API/AbortController/abort) 的实验特性，它有一个名为`abort`的方法，可以用来解决这样的问题。