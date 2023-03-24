# 易于用普通 JavaScript 实现的 Lodash 数组方法

> 原文：<https://levelup.gitconnected.com/lodash-array-methods-that-can-easily-be-implemented-in-plain-javascript-9a701e5e25c1>

![](img/1f87d19a8fb1ad44a7833ffd67a7a3ef.png)

由 [Kasya Shahovskaya](https://unsplash.com/@kasya?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

Lodash 是一个非常有用的工具库，让我们可以轻松地处理对象和数组。

然而，现在 JavaScript 标准库正在赶上诸如 Lodash 之类的库，我们可以用简单的方法实现许多功能。

在本文中，我们将研究一些可以很容易地用普通 JavaScript 替换的数组访问方法。

# 头

`head`方法只获取数组的第一个元素。从 JavaScript 的第一个版本开始，我们就可以毫不费力地做到这一点。

例如，我们可以创建自己的`head`函数，如下所示:

```
const head = (arr) => arr[0]
```

那么我们可以这样说:

```
const result = head([1, 2, 3]);
```

而`result`得到 1。

# 索引 Of

Lodash `indexOf`方法从我们可以选择指定的第一个索引或者数组开始和数组结束之间的位置找到值。

普通 JavaScript `indexOf`做同样的事情。唯一的区别是它是数组实例的一部分，而不是像我们使用 Lodash 那样将数组传递给`indexOf`方法。

我们可以轻松地使用普通 JavaScript 的`indexOf`方法来创建我们自己的 Lodash 的`indexOf`方法，如下所示:

```
const indexOf = (arr, val, start = 0) => arr.indexOf(val, start);
```

在上面的代码中，我们在`arr`上调用了普通 JavaScript 的`indexOf`。然后我们只需传入`val`来搜索条目，传入`start`来搜索可选的起始索引。

两个`indexOf`方法都返回第一个匹配的索引。那么我们可以这样称呼它:

```
const result = indexOf([1, 2, 3], 2, 1);
```

因为 2 在索引 1 中，所以我们为`result`得到 1。如果在指定的索引范围内没有找到项目，两个`indexOf`方法都返回-1。

# `initial`

Lodash `initial`方法返回数组中除最后一个元素之外的所有元素。我们可以用`slice`方法轻松实现这一点。

例如，我们可以如下实现它:

```
const initial = (arr) => arr.slice(0, -1);
```

在上面的代码中，我们用 0 和-1 作为参数调用了 since，以包含第一个到倒数第二个元素。负索引从-1 开始，表示最后一个元素。那么倒数第二个就是-2，依此类推。

因此，当我们这样称呼它时:

```
const result = initial([1, 2, 3]);
```

我们得到`[1, 2]`作为`result`的值。

![](img/63484f7a07aec104e7e42a2e41a40a96.png)

照片由[维塔利·西尼克](https://unsplash.com/@vitalisit?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# `intersection`

Lodash `intersection`方法创建一个包含唯一值的数组，这些值包含在使用 SameValueZero 相等比较给出的所有数组中，这与`===`类似，只是`+0`和`-0`被认为是不同的，而`NaN`与自身相同。

Lodash 的`intersection`方法接受零个或多个数组参数。我们可以得到所有数组中的所有元素，如下所示:

```
const intersection = (...arrs) => {
  let vals = [];
  const longestArr = arrs.sort((a, b) => b.length - a.length)[0]
  for (const a of longestArr) {
    const inAllArrs = arrs.every((arr) => arr.includes(a))
    if (inAllArrs) {
      vals.push(a)
    }
  }
  return [...new Set(vals)];
}
```

在上面的代码中，我们首先将参数分布到一个数组中。

然后，我们遍历最长的数组，并使用普通 JavaScript 的`every`方法和回调`(arr) => arr.includes(a)`检查其他数组是否与第一个数组具有相同的条目，回调函数检查传入的所有数组是否都包含给定的条目`a`。

我们不需要检查所有数组中的所有条目，因为如果我们循环的数组中没有条目，那么它就不应该包含在内。

如果它包含在所有数组中，那么我们将其推送到`vals`。`includes`使用 SameValueZero 算法，因此我们不需要做任何其他的比较检查。

那么当我们这样称呼它的时候:

```
const result = intersection([1, 2, 3], [1, 2], [1, 3, 5]);
```

我们得到`[1]`作为`result`的值。

# 结论

自从 JavaScript 的第一个版本以来，Lodash `head`方法不需要太多的努力来实现我们自己。

用普通 JavaScript 的`indexOf`方法也可以很容易地实现`indexOf`方法。

我们可以使用`slice`方法来实现 Lodash 的`initial`方法。

通过使用 spread 操作符将参数扩展到一个数组中，然后使用`for...of`循环遍历最长的数组并检查其他数组是否有给定的条目，可以实现`intersection`方法。