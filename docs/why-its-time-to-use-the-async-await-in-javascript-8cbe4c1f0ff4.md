# 为什么是时候在 JavaScript 中使用异步等待了

> 原文：<https://levelup.gitconnected.com/why-its-time-to-use-the-async-await-in-javascript-8cbe4c1f0ff4>

![](img/f6b9b1547d8f7cd12ecf1a85f4f870aa.png)

照片由[约翰·普莱斯](https://unsplash.com/@johnprice?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

异步代码是 JavaScript 的常规部分。随着 web 应用变得越来越复杂，对异步代码的需求也会越来越多，因为 JavaScript 是单线程的，异步代码可以防止阻塞主线程。

在本文中，我们将看看为什么`async`和`await`是编写异步代码的方法。

# 编写 Promise 代码的老方法

在我们看`async`和`await`之前，我们必须看看它在过去是什么样子的。

在过去，我们通过写下类似下面的东西来锁住承诺:

```
Promise.resolve(1)
  .then(val => Promise.resolve(2))
  .then(val => Promise.resolve(3))
  .then(val => Promise.resolve(4))
  .then(val => Promise.resolve(5))
  .then(val => Promise.resolve(6))
```

正如我们所看到的，我们必须调用`then`很多次，在每个`then`调用中，我们必须传递一个回调函数来返回下一个承诺。这是一个痛苦，因为它非常冗长。如果我们想从每个承诺中收集解析的值，那么代码会变得更长。

例如，要将解析的值收集到一个数组中，我们必须编写如下代码:

```
let vals = [];
Promise.resolve(1)
  .then(val => {
    vals.push(val);
    return Promise.resolve(2)
  })
  .then(val => {
    vals.push(val);
    return Promise.resolve(3)
  })
  .then(val => {
    vals.push(val);
    return Promise.resolve(4)
  })
  .then(val => {
    vals.push(val);
    return Promise.resolve(5)
  })
  .then(val => {
    vals.push(val);
    return Promise.resolve(6)
  })
  .then(val => {
    vals.push(val);
    console.log(vals)
  });
```

然后我们得到`[1, 2, 3, 4, 5, 6]`作为`vals`的值。

正如我们所看到的，与第一个例子相比，代码行数激增。

文件中有很多重复的内容，会占用很多空间。

为了解决这个问题，是时候用`async-await`语法来链接承诺了。

![](img/778b285c9cf1e8719e4ab89a4d9d79fb.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Berkay Gumustekin](https://unsplash.com/@berkaygumustekin?utm_source=medium&utm_medium=referral) 拍摄的照片

# 异步和等待

为了清理上面例子中的代码，我们可以使用`async`和`await`来完成。

我们用`async`开始一个函数声明，并在里面使用`await`关键字。`await`与`then`回调做同样的事情。它接受承诺的价值，并让我们用它做事情。

通过`async`和`await`，我们可以转动:

```
let vals = [];
Promise.resolve(1)
  .then(val => {
    vals.push(val);
    return Promise.resolve(2)
  })
  .then(val => {
    vals.push(val);
    return Promise.resolve(3)
  })
  .then(val => {
    vals.push(val);
    return Promise.resolve(4)
  })
  .then(val => {
    vals.push(val);
    return Promise.resolve(5)
  })
  .then(val => {
    vals.push(val);
    return Promise.resolve(6)
  })
  .then(val => {
    vals.push(val);
    console.log(vals)
  });
```

变成:

```
(async () => {
  const val = await Promise.resolve(1);
  const val2 = await Promise.resolve(2);
  const val3 = await Promise.resolve(3);
  const val4 = await Promise.resolve(4);
  const val5 = await Promise.resolve(5);
  const val6 = await Promise.resolve(6);
  const vals = [val, val2, val3, val4, val5, val6];
  console.log(vals);
})();
```

上面代码中的`await`表示它右边的代码返回一个承诺，它将等待这个承诺被实现，直到它移动到下一个。同样，解析的值可以用`=`和它左边的变量或常量来赋值。

这些值可以聚集到函数末尾的一个数组中，我们可以记录下来。

尽管这看起来像同步代码，`async`函数只能返回承诺，所以如果我们返回`vals`，我们将得到一个解析为`vals`值的承诺。

这意味着:

```
const getVals = () => {
  let vals = [];
  Promise.resolve(1)
    .then(val => {
      vals.push(val);
      return Promise.resolve(2)
    })
    .then(val => {
      vals.push(val);
      return Promise.resolve(3)
    })
    .then(val => {
      vals.push(val);
      return Promise.resolve(4)
    })
    .then(val => {
      vals.push(val);
      return Promise.resolve(5)
    })
    .then(val => {
      vals.push(val);
      return Promise.resolve(6)
    })
    .then(val => {
      vals.push(val);
      return Promise.resolve(vals);
    });
}
```

与以下内容相同:

```
const getValsAsyncAwait = async () => {
  const val = await Promise.resolve(1);
  const val2 = await Promise.resolve(2);
  const val3 = await Promise.resolve(3);
  const val4 = await Promise.resolve(4);
  const val5 = await Promise.resolve(5);
  const val6 = await Promise.resolve(6);
  const vals = [val, val2, val3, val4, val5, val6];
  return vals;
};
```

我们可以在两者上使用`await`,看看我们会得到什么。

此区块:

```
const getVals = () => {
  let vals = [];
  return Promise.resolve(1)
    .then(val => {
      vals.push(val);
      return Promise.resolve(2)
    })
    .then(val => {
      vals.push(val);
      return Promise.resolve(3)
    })
    .then(val => {
      vals.push(val);
      return Promise.resolve(4)
    })
    .then(val => {
      vals.push(val);
      return Promise.resolve(5)
    })
    .then(val => {
      vals.push(val);
      return Promise.resolve(6)
    })
    .then(val => {
      vals.push(val);
      return Promise.resolve(vals);
    });
}(async () => {
  const values = await getVals();
  console.log(values);
})()
```

从`console.log`中获取`[1, 2, 3, 4, 5, 6]`。

并且:

```
const getValsAsyncAwait = async () => {
  const val = await Promise.resolve(1);
  const val2 = await Promise.resolve(2);
  const val3 = await Promise.resolve(3);
  const val4 = await Promise.resolve(4);
  const val5 = await Promise.resolve(5);
  const val6 = await Promise.resolve(6);
  const vals = [val, val2, val3, val4, val5, val6];
  return vals;
};(async () => {
  const values = await getValsAsyncAwait();
  console.log(values);
})()
```

也让我们从`console.log`中得到`[1, 2, 3, 4, 5, 6]`。所以它们做完全相同的事情，只是代码行少得多。

# 捕捉错误

在过去，我们用`catch`捕捉错误，回调传递给它。

例如，我们写下这样的内容:

```
Promise.resolve(1)
  .then(val => console.log(val))
  .catch(err => console.log(err));
```

现在我们可以将`try...catch`与`async`和`await`一起使用:

```
(async () => {
  try {
    const val = await Promise.resolve(1);
  } catch (err) {
    console.log(err);
  }
})();
```

这并没有节省多少空间，但在出现错误的情况下，这仍然可以派上用场。

正如我们所见，`async`和`await`使得链接承诺的代码变得更短。从 2017 年就有了，所以大部分现代浏览器都支持。现在绝对是使用它来清理代码的时候了。