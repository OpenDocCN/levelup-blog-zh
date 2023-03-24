# 通过实现 Lodash 方法学习 JavaScript 对象和集合

> 原文：<https://levelup.gitconnected.com/learning-javascript-by-implementing-lodash-methods-objects-and-collections-1974cd9afdee>

![](img/b6edf0958f30271d410c987d0478ed9b.png)

亚历山大·席默克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Lodash 是一个非常有用的工具库，让我们可以轻松地处理对象和数组。

然而，现在 JavaScript 标准库正在赶上诸如 Lodash 之类的库，我们可以用简单的方法实现许多功能。

在本文中，我们将研究如何实现方法来检查集合中是否存在某些东西以及如何处理对象。

# `some`

Lodash `some`方法返回一个布尔值，以指示满足谓词中条件的项是否存在于集合中。

对于数组，我们可以使用普通 JavaScript 数组的`some`方法来搜索一个条目，如果找到给定条件的条目，则返回`true`。

我们可以用普通 JavaScript 的`some`方法实现`some`,如下所示:

```
const some = (collection, predicate) => {
  return collection.some(predicate);
}
```

在上面的代码中，我们只是使用了数组实例的`some`方法并传入了`predicate`。

那么当我们如下运行`some`时:

```
const users = [{
    'user': 'foo',
    'active': true
  },
  {
    'user': 'bar',
    'active': false
  }
];
const result = some(users, u => u.active);
```

`result`是`true`，因为有一个条目`users`的`active`设置为`true`。

# `castArray`

Lodash `castArray`方法将任何对象转换成一个数组。它接受一个参数列表，并把它们放在一个数组中。

有了 rest 操作符，我们无需做太多工作就可以将项目放入数组中。我们可以如下实现`castArray`方法:

```
const castArray = (...args) => args;
```

在上面的代码中，我们只是将 rest 操作符与`args`一起使用，它将参数列表转换为一个数组，然后直接返回它。

那么当我们如下调用我们的`castArray`函数时:

```
const result = castArray(1, 2, 3);
```

那么我们得到`result`是:

```
[
  1,
  2,
  3
]
```

# `clone`

Lodash `clone`方法返回一个对象的浅表副本。它支持克隆所有类型的对象，包括数组、缓冲区、布尔值和日期对象。

因为 JavaScript 有对象的 spread 操作符，我们可以使用它来进行克隆，如下所示:

```
const clone = obj => ({
  ...obj
});
```

那么我们可以如下调用`clone`函数:

```
const result = clone({
  a: 1
});
```

然后我们看到`result`是:

```
{
  "a": 1
}
```

![](img/6ef12d171308ada5a1487591bfbbf028.png)

克里斯蒂安·帕尔默在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# `cloneDeep`

Lodash `cloneDeep`方法递归克隆一个对象。使用 spread 操作符，我们可以自己递归地克隆一个对象的每一层。

例如，我们可以这样实现:

```
const cloneDeep = (obj, cloned = {}) => {
  if (Array.isArray(obj)) {
    cloned = [
      ...obj
    ];
    for (let i = 0; i < obj.length; i++) {
      cloneDeep(obj[i], cloned[i]);
    }
  } else if (typeof obj === 'object') {
    cloned = {
      ...obj
    };
    for (const p of Object.keys(obj)) {
      cloneDeep(obj[p], cloned[p])
    }
  }
  return cloned;
}
```

在上面的代码中，我们有自己的`cloneDeep`函数，它将`obj`作为原始对象，将`cloned`对象作为克隆对象。

我们遍历对象，然后对它们调用`cloneDeep`。

如果`obj`是一个数组，那么我们用扩展操作符扩展这个数组。

否则，我们可以使用 spread 运算符将`obj`克隆到顶层的`cloned`。

然后我们检查`obj`是否是一个对象。如果是，那么我们可以循环访问这些键，并在对象的底层调用`cloneDeep`。

最后，我们返回带有克隆对象的`cloned`。

然后我们可以如下调用我们的`cloneDeep`函数:

```
const obj = {
  a: 1,
  b: {
    c: 2
  }
}
const result = cloneDeep(obj);
```

那么我们得到`result`是:

```
{
  "a": 1,
  "b": {
    "c": 2
  }
}
```

并且:

```
result === obj
```

是`false`。我们还可以如下克隆阵列:

```
const obj = [{
  'a': 1
}, {
  'b': 2
}]
const result = cloneDeep(obj);
```

然后我们得到`result`是下面的数组:

```
[
  {
    "a": 1
  },
  {
    "b": 2
  }
]
```

并且:

```
result === obj
```

依然是`false`。

# 结论

我们可以使用普通 JavaScript 数组的`some`方法来检查集合中是否存在满足谓词条件的对象。

为了实现`castArray`方法，我们可以在参数上使用 rest 操作符。

最后，可以用扩展操作符实现`clone`和`cloneDeep`方法。`cloneDeep`在克隆每个关卡之前需要进行数组或对象检查。