# 等待数组。原型。带 ES6 的地图

> 原文：<https://levelup.gitconnected.com/await-array-prototype-map-with-es6-ae5d6e4d1d66>

![](img/b19b88b5b9f0e6e885c7ee829fcc27c2.png)

来自 [Pexels](https://www.pexels.com/photo/computer-with-code-4218883/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Paras Katwal](https://www.pexels.com/@paras?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 摄影

Javascript 中的承诺很难理解。我们应该使用承诺还是组合异步/等待？这可能是一个艰难的选择。

但是当它开始做函数式编程时，它可能开始变得令人困惑。如果您的回调正在处理异步函数，而您想等待它们，该怎么办？那么如何处理地图回调的返回值呢？(这将是一个承诺，因为回调是异步的)

不幸的是，即使有意义，在地图上直接使用 await 也是不可能的。让我们来看看下面的场景:

```
const array = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12];async function test() {
  const results = await array.map(async(item) => { // The setTimeout with await simulate a "heavy async function"
   await setTimeout(() => {}, 1000);
    return item;
  })

  console.log(results);
}test()
```

console.log 将打印一组仍然需要解决的承诺。这是因为 Map 本身无法解析这些承诺，所以它只会返回这些承诺，并期望开发人员手动处理它们。

现在，如果我们想等待所有的承诺都被解析并打印出来，我们可以使用全局对象 Promise: `Promise.all`中的这个非常有用的函数，并在后面添加关键字 await 来等待所有的承诺都被解析。

现在让我们编辑我们的代码:

```
const array = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12];async function test() {
  // No more await here
  const promises = array.map(async(item) => {
   await setTimeout(() => {}, 1000);
    return item;
  })

  // Await + Promise.all to wait for all promises to resolve
  const results = await Promise.all(promises)

  console.log(results);
}test()
```

有了这个新版本的代码，我们正在等待所有的承诺解决，然后我们可以正确地打印它们的结果！