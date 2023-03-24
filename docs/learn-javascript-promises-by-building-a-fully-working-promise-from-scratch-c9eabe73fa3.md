# 从头开始构建 JavaScript 承诺

> 原文：<https://levelup.gitconnected.com/learn-javascript-promises-by-building-a-fully-working-promise-from-scratch-c9eabe73fa3>

## 学习 JavaScript promises 并构建自己的 Promise.then()。catch()实现来深入理解 promises 以及它们如何处理 JavaScript 中的异步代码

![](img/e5bfa7eb0b4c9b214ef1f6f016297fb4.png)

**原文** [**此处**](https://skilled.dev/course/build-a-javascript-promise) **:**

[](https://skilled.dev/course/build-a-javascript-promise) [## 构建一个 JavaScript Promise 编码面试问题| Skilled.dev

### 注意:如果您想看到我们的 promise 实现如何处理一个真实的 API 请求，请查看……

技术开发](https://skilled.dev/course/build-a-javascript-promise) 

异步编程是 JavaScript 中的一个核心概念，与其他脚本编程语言相比，这一特性大大提高了 JavaScript 的速度。JavaScript 是单线程的，这意味着它逐行执行程序。它也是异步的，这意味着如果我们的程序执行到了一个必须等待结果的代码块，它将继续通过这个正在等待的代码块，这样程序就不会冻结执行，一旦异步任务完成，我们的代码将通过使用一个承诺或回调来处理它正在等待的结果。

自 ES2015 以来，处理异步函数调用结果的最常见方式是通过`Promise`，但在此之前使用了回调。最近，`async` / `await`语法被添加到 JavaScript 中，但这只是对承诺的抽象，我们将在文章的最后看到它们的比较。

在本课中，您将通过从头构建 JavaScript promise 实现来学习 JavaScript promise。你的承诺将像在原生 JavaScript `new Promise(...)`中一样被声明，它将能够链接`.then()`和`.catch()`语句来处理异步代码的结果。

## 履行诺言

我们正在构建的 JavaScript promise 示例旨在帮助您理解承诺和异步思维的基础——它并不打算展示承诺的最佳版本。我将首先为初学者描述 promise，然后深入我们自己的 promise 实现，这样我们就可以从头开始理解它。

你可能以前见过类似的东西:

```
fetch('/user/1')
  .then((user) => { 
    /* Do something with user after the API returns */ 
  })
```

在执行任何操作之前，`.then()`中的代码块会一直等待，直到收到来自服务器的响应。这叫做`Promise`。但是不要让花哨的名字或存在异步代码的事实吓倒你——`Promise`只是一个普通的老式 JavaScript 对象，它具有特殊的方法，允许你同步执行代码(即使有延迟，它也会按顺序执行)。

```
typeof new Promise((resolve, reject) => {}) === 'object' // true
```

我重申一下(因为这是我刚学承诺时很难把握的东西)，a `Promise`只是一个对象。为了能够在响应后等待服务器并执行`.then()`链中的代码，您必须`return`一个`Promise`对象。这不是函数开箱即用的东西。在幕后，fetch 函数正在做这样的事情。

```
const fetch = function(url) {
  return new Promise((resolve, reject) => {
    request((error, apiResponse) => {
      if (error) {
        reject(error)
      }

      resolve(apiResponse)
    })
  })
}
```

`fetch()`函数向服务器发出 http 请求，但是客户端不知道服务器什么时候会发回结果。因此 JavaScript 开始执行其他不相关的代码，同时等待服务器返回响应。一旦客户端收到响应，它就会通过调用`resolve(apiResponse)`来启动`.then()`语句中代码的执行。

现在让我们仔细看看`Promise`实际上是如何让你做到这一点的。

一个承诺的教育例子—[https://gist . github . com/trey huffine/d 2e 63 bdee 6645 a7a 0619989 ee5a 4538 b](https://gist.github.com/treyhuffine/d2e63bdee6645a7a0619989ee5a4538b)

> 注意:这个版本的承诺仅用于教育目的。我省略了一些更高级的特性，将其提炼为核心功能。

我把它命名为`PromiseSimple`，这样它就不会和原生的`Promise`冲突，以防你想把它复制粘贴到你的 Chrome 控制台上。我们的承诺实现有一个`constructor`，2 个你可能熟悉的公共方法`then()`和`catch()`，2 个内部方法`onResolve()`和`onReject()`。

当你创造一个承诺时，你是这样做的。你传递给它一个回调函数，我在构造函数中命名为`executionFunction`。执行函数采用映射到内部`onResolve()`和`onReject()`函数的`resolve`和`reject`。当 fetch 调用 resolve 或 reject 时，将调用这些函数。

构造函数还创建了一个`promiseChain`数组和`handleError`函数。当添加一系列`.then(() => {})`时，它会将每个功能推送到`promiseChain`上。当用户调用`catch(() => {})`时，它将函数分配给内部`handleError`。注意`then()`和`catch()`功能`return this;`。这允许你链接多个`then()`，因为你正在返回对象本身。

> 注意:在原生的`*Promise*`中，这些`*then()*`和`*catch()*`函数实际上返回了一个`*new Promise*`，但是对于这个简单的场景，我只返回了`*this*`。此外，可以有多个`*.catch()*`模块，它们也可以被链接，并且不需要出现在`*.then()*`链的末端。

当异步函数调用`resolve(apiResponse)`时，promise 对象开始执行`onResolve(apiResponse)`。它通过删除前面的函数来遍历整个`promiseChain`，并使用保存在`storedValue`中的最新值来执行它。然后将`storedValue`更新为最近一次执行的结果。它将按顺序执行这些功能。这就创建了同步承诺链。

这个循环被包裹在一个`try/catch`块中。这是查找错误的特殊 JavaScript 语法。如果你的异步函数调用了`reject(error)`或者你的`try/catch`发现了一个错误，那么它将被传递给`onReject()`方法，该方法调用你传递给`.catch()`的函数。

结合一个更实际的例子:

[https://gist . github . com/trey huffine/f 21525172 fece 828d 385 f 9 C5 db 8 f 87 a 0](https://gist.github.com/treyhuffine/f21525172fece828d385f9c5db8f87a0)

## 异步/等待

`async` / `await`语法只是承诺的包装。

如果您将函数标记为 async，它只是将返回响应转换为承诺。

这相当于执行以下操作: