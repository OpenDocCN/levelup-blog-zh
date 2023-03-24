# JavaScript 错误——循环、承诺等等

> 原文：<https://levelup.gitconnected.com/javascript-mistake-loops-promises-and-more-e866e3ccc404>

![](img/d5e2d934146b74b1c8311c18b53c29c3.png)

由 [Travis Yewell](https://unsplash.com/@shutters_guild?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将看看一些 JavaScript 错误，包括循环和承诺。

# 循环方向错误

带有永远不会到达的结束条件的`for`循环可能是错误代码。如果我们想做一个无限循环，我们应该使用一个`while`循环作为约定。

例如，如果我们有:

```
for (let i = 0; i < 20; i--) {
}
```

这可能是一个错误，因为我们指定了结束条件，但从未达到它。

我们的意思可能是:

```
for (let i = 0; i < 20; i++) {
}
```

# 不返回任何东西的 Getters

如果我们做了一个 getter，但它没有返回任何东西，这很可能是一个错误。没有理由制造一个返回`undefined`的 getter。

例如，以下内容可能不正确:

```
let person = {
  get name() {}
};Object.defineProperty(person, "gender", {
  get() {}
});class Person {
  get name() {}
}
```

在上面的所有代码中，我们都有无用的 getters。如果我们有一个 getter，那么我们应该在里面返回一些东西:

```
let person = {
  get name() {
    return 'Jane';
  }
};Object.defineProperty(person, "gender", {
  get() {
    return 'female';
  }
});class Person {
  get name() {
    return 'James';
  }
}
```

# 作为承诺执行器回调的异步函数

当我们从头定义一个承诺时，我们必须传入一个带有`resolve`和`reject`函数作为参数的执行器回调函数。

我们不希望`async`函数作为执行者，因为当抛出错误时，它们会丢失，不会导致新构建的承诺被拒绝。这使得调试和处理一些错误变得困难。

如果一个 promise executor 函数正在使用`await`，那么这通常意味着创建一个新的`Promise`实例是无用的，或者可以使用`new` Promise 构造函数的范围。

正在使用的`await`已经是一个承诺，并且`async`函数也返回一个承诺，所以我们不需要承诺里面的承诺。

例如，如果我们有以下代码:

```
const fs = require('fs');const foo = new Promise(async (resolve, reject) => {
  fs.readFile('foo.txt', (err, result)=> {
    if (err) {
      reject(err);
    } else {
      resolve(result);
    }
  });
});const result = new Promise(async (resolve, reject) => {
  resolve(await foo);
});
```

那么这可能是一个错误，因为我们在承诺中嵌套了一个承诺。

我们实际上想做的是:

```
const fs = require('fs');const foo = new Promise(async (resolve, reject) => {
  fs.readFile('foo.txt', (err, result)=> {
    if (err) {
      reject(err);
    } else {
      resolve(result);
    }
  });
});const result = Promise.resolve(foo);
```

![](img/fa603076176c77e8e5ef8c6892e85f5d.png)

照片由[马太·亨利](https://unsplash.com/@matthewhenry?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 没有等待内部循环

`async`和`await`允许并行化。通常，我们希望运行`Promise.all`来并行运行不相关的承诺。循环运行`await`将依次运行每个承诺。对于互不依赖的承诺来说，这是不必要的。

例如，我们应该写:

```
const bar = (results) => console.log(results);const foo = async (arr) => {
  const promises = [];
  for (const a of arr) {
    promises.push(Promise.resolve(a));
  }
  const results = await Promise.all(promises);
  bar(results);
}
```

而不是:

```
const bar = (results) => console.log(results);const foo = async (arr) => {
  const results = [];
  for (const a of arr) {
    results.push(await  Promise.resolve(a));
  }  
  bar(results);
}
```

第一个例子比第二个快得多，因为我们并行运行它们，而不是顺序运行。

如果承诺是相互依赖的，那么我们应该使用第二个例子。

# 不要拿任何东西和负零做比较

再次比较负 0 将返回+0 和-0 的`true`。我们可能实际上想要使用`Object.is(x, -0)`来检查某个值是否等于-0。

例如，在以下代码中:

```
const x = +0;
const y = -0;
console.log(x === -0)
console.log(y === -0)
```

两个表达式都将记录`true`。另一方面，如果我们如下使用`Object.is`:

```
const x = +0;
const y = -0;
console.log(Object.is(x, -0))
console.log(Object.is(y, -0))
```

然后第一个日志是`false`，第二个是`true`，大概就是我们想要的。

# 结论

有很多方法可以编写与 JavaScript 不相关的代码。为了防止错误发生，我们应该使用`Object.is`再次比较+0 和-0，在 promise executor 回调中运行 promise，添加不返回任何内容的 getters，或者无意中创建无限循环。

如果承诺可以并行运行，那么我们应该通过使用`Promise.all`来利用它。