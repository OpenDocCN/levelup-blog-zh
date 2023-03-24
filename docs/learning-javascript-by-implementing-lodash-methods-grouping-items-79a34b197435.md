# 通过实现 Lodash 方法学习 JavaScript 对项目进行分组

> 原文：<https://levelup.gitconnected.com/learning-javascript-by-implementing-lodash-methods-grouping-items-79a34b197435>

![](img/801c187adb01b527dadab8b780805cd6.png)

布鲁克·卡吉尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Lodash 是一个非常有用的工具库，让我们可以轻松地处理对象和数组。

然而，现在 JavaScript 标准库正在赶上诸如 Lodash 之类的库，我们可以用简单的方法实现许多功能。

在本文中，我们将研究如何实现 Lodash 方法来对集合中的项目进行分组。

# `keyBy`

他们`keyBy`方法将集合条目分组到由函数返回的键中，该函数将条目作为参数，并根据函数代码中的内容返回键。

我们可以创建自己的`keyBy`函数如下:

```
const keyBy = (arr, fn) => {
  let obj = {};
  for (const a of arr) {
    obj[fn(a)] = a;
  }
  return obj;
}
```

在上面的代码中，我们只是创建了一个空对象，然后用来自`for...of`循环的数组条目调用`fn`来填充它，并将它用作键。

然后将相应的数组条目设置为值，并返回对象。

当我们这样称呼它时:

```
const arr = [{
    'dir': 'left',
    'code': 99
  },
  {
    'dir': 'right',
    'code': 100
  }
];
const result = keyBy(arr, o => String.fromCharCode(o.code))
```

那么`result`就是:

```
{
  "c": {
    "dir": "left",
    "code": 99
  },
  "d": {
    "dir": "right",
    "code": 100
  }
}{
  "c": {
    "dir": "left",
    "code": 99
  },
  "d": {
    "dir": "right",
    "code": 100
  }
}
```

# `map`

`map`方法返回一个从原始数组映射而来的数组，方法是在每个条目上调用一个函数，并将它放入新数组中。

由于普通 JavaScript 有一个用于数组实例的`map`方法，我们可以用它来实现 Lodash `map`方法。

Lodash 的`map`方法也将一个对象作为参数，该对象获取键，然后用函数映射值，并将值放入返回的数组中。

我们可以这样实现:

```
const map = (collection, iteratee) => {
  if (Array.isArray(collection)){
    return collection.map(iteratee);
  }
  else {
    return Object.values(collection).map(iteratee)
  }
}
```

在上面的代码中，我们使用`Array.isArray`方法来检查`collection`是否是一个数组。如果是，那么我们就使用数组的`map`方法，并使用`iteratee`作为回调。

否则，我们使用`Object.values`返回一个对象值的数组，并使用`iteratee`作为回调函数调用`map`。

那么我们可以这样称呼它:

```
const result = map([4, 8], x => x**2);
```

将`result`设置为:

```
[
  16,
  64
]
```

此外，当我们跑步时:

```
const result = map({ 'a': 4, 'b': 8 }, x => x**2);
```

我们得到同样的结果。

![](img/b36b2709daef32ded988d939be6ffe74.png)

图为 [Duy Pham](https://unsplash.com/@miinyuii?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# `partition`

`partition`方法返回一个数组，该数组由传入的函数返回的键分组。

它根据函数返回的键对数组进行分组。

我们可以如下实现它:

```
const partition = (collection, predicate) => {
  let obj = {};
  for (const a of collection) {
    if (!Array.isArray(obj[predicate(a)])) {
      obj[predicate(a)] = [];
    }
    obj[predicate(a)].push(a);
  }
  return Object.keys(obj).map(k => obj[k])
}
```

在上面的代码中，我们创建了一个`obj`对象，然后用一个`for...of`循环遍历`collection`。

然后我们用键`obj`创建一个数组，这个数组是通过调用条目上的`predicate`创建的。

如果它不是一个数组，那么我们把它设置为一个空数组。然后我们根据按键推送项目。

最后，我们通过将键映射到相应的值来返回数组中的值。

那么当我们这样称呼它的时候:

```
const users = [{
    'user': 'foo',
    'active': false
  },
  {
    'user': 'bar',
    'active': true
  },
  {
    'user': 'baz',
    'active': false
  }
];const result = partition(users, o => o.active);
```

我们得到的`result`是:

```
[
  [
    {
      "user": "foo",
      "active": false
    },
    {
      "user": "baz",
      "active": false
    }
  ],
  [
    {
      "user": "bar",
      "active": true
    }
  ]
]
```

# 结论

通过使用`for...of`循环将集合分组到一个对象中，并通过调用`iteratee`函数填充键，可以实现`keyBy`方法。然后，这些值由相应的键填充。

`map`方法可以由数组实例的`map`方法实现。

`partition`也可以通过遍历所有对象，然后用数组条目调用`predicate`来填充键，然后我们用相应的键将项目推入数组来实现。