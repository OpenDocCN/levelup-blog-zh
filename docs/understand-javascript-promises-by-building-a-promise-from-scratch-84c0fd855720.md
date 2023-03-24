# JavaScript 承诺——通过构建一个简单的承诺示例来理解 JavaScript 承诺

> 原文：<https://levelup.gitconnected.com/understand-javascript-promises-by-building-a-promise-from-scratch-84c0fd855720>

## 这是一个循序渐进的教程，通过从头开始构建承诺，确保您完全理解 JavaScript 承诺是如何工作的

![](img/7d9e1c5a44910914c1e6e9cc150a7426.png)

在本教程中，您将通过从头开始构建 JavaScript promise 来学习 JavaScript promise。这一课对于仍在学习异步 JavaScript 基础知识的初学者和中级开发人员来说非常有用。您将构建的 JavaScript promise 示例旨在帮助您理解承诺和异步思维的基础——它并不打算展示承诺的最佳版本。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev) 

你可能以前见过类似的东西:

```
fetch('/user/1')
  .then((user) => { 
    /* Do something with user after the API returns */ 
  })
```

在执行任何操作之前，`.then()`中的代码块会一直等待，直到收到来自服务器的响应。这叫做`Promise`。但是不要让花哨的名字或者异步代码[的事实吓倒你——`Promise`只是一个普通的老式 JavaScript 对象，它有特殊的方法允许你同步执行代码(即使有延迟，它也会按顺序执行)。](https://stackoverflow.com/questions/4559032/easy-to-understand-definition-of-asynchronous-event)

`typeof new Promise((resolve, reject) => {}) === 'object' // true`

让我重申一下(因为这是我第一次学习承诺时很难理解的)，一个`Promise`只是一个对象。为了能够在响应后等待服务器并执行`.then()`链中的代码，必须返回一个`Promise`对象。这不是函数开箱即用的东西。在幕后，fetch 函数正在做这样的事情。

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

一个承诺的教育范例—[https://gist . github . com/trey huffine/D2 e 63 bdee 6645 a7a 0619989 ee5a 4538 b](https://gist.github.com/treyhuffine/d2e63bdee6645a7a0619989ee5a4538b)

> 注意:这个版本的承诺仅用于教育目的。我省略了一些更高级的特性，将其提炼为核心功能。

我把它命名为`PromiseSimple`，这样它就不会和原生的`Promise`冲突，以防你想把它复制粘贴到你的 Chrome 主机上。我们的承诺实现有一个`constructor`，两个你可能熟悉的公共方法`then()`和`catch()`，两个内部方法`onResolve()`和`onReject()`。

当你创造一个承诺时，你这样做`new Promise((resolve, reject) => {/* ... */})`。你传递给它一个回调函数，我在构造函数中命名为`executionFunction`。执行函数采用映射到内部函数`onResolve()`和`onReject()`的`resolve`和`reject`。当 fetch 调用 resolve 或 reject 时，将调用这些函数。

构造函数还创建了一个`promiseChain`数组和`handleError`函数。当添加一系列`.then(() => {})`时，它会将每个功能推送到`promiseChain`上。当用户调用`catch(() => {})`时，它将函数分配给内部`handleError`。注意`then()`和`catch()`功能`return this;`。这允许你链接多个`then()`，因为你正在返回对象本身。

> 注意:在原生的`Promise`中，这些`then()`和`catch()`函数实际上返回了一个`new Promise`，但是对于这个简单的场景，我只返回了`this`。此外，可以有多个`.catch()`模块，它们也可以被链接，并且不需要出现在`.then()`链的末端。

当异步函数调用`resolve(apiResponse)`时，promise 对象开始执行`onResolve(apiResponse)`。它通过删除前面的函数来遍历整个`promiseChain`，并使用保存在`storedValue`中的最新值来执行它。然后它将`storedValue`更新为最近一次执行的结果。它将按顺序执行这些功能。这就创建了同步承诺链。

该循环被包裹在`try/catch`块中。这是查找错误的特殊 JavaScript 语法。如果你的异步函数调用了`reject(error)`或者你的`try/catch`发现了一个错误，那么它将被传递给`onReject()`方法，该方法调用你传递给`.catch()`的函数。

![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)[](https://gitconnected.com/portfolio-api) [## 组合 API —轻松发展您的编码事业| gitconnected

### 消除在每个单独位置手动更新您的详细信息的痛苦。只需在您的中更改一次数据…

gitconnected.com](https://gitconnected.com/portfolio-api) ![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

结合一个更实际的例子:

[https://gist . github . com/trey huffine/f 21525172 fece 828d 385 f 9 C5 db 8 f 87 a 0](https://gist.github.com/treyhuffine/f21525172fece828d385f9c5db8f87a0)

*如果您觉得本文有帮助，请点击*👏*。* [*关注我*](https://medium.com/@treyhuffine) *了解更多关于 React、Node.js、JavaScript 和开源软件的文章！你也可以在*[*Twitter*](https://twitter.com/treyhuffine)*或者*[*git connected*](https://gitconnected.com/treyhuffine)*上找到我。*