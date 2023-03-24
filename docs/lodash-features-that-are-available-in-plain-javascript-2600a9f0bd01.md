# 普通 JavaScript 中可用的 Lodash 特性

> 原文：<https://levelup.gitconnected.com/lodash-features-that-are-available-in-plain-javascript-2600a9f0bd01>

![](img/95f73a4a8071a6efbd94a1c8be3623c7.png)

Joshua J. Cotten 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

近年来，JavaScript 中的新特性一直在快速发展。之前由其他库填补的不足，已经成为普通 JavaScript 的内置特性。

在本文中，我们将看看现在普通 JavaScript 中可用的 Lodash 方法。

# 地图

Lodash 有一个`map`方法，通过我们指定的函数将每个数组条目映射到另一个。它接受一个数组和一个函数来将条目转换为参数。

例如，我们可以这样调用 Lodash 的`map`方法:

```
const arr = _.map([1, 2, 3], n => n ** 2);
```

然后我们回来了:

```
[1, 4, 9]
```

JavaScript 的数组现在有了`map`方法。我们可以如下使用它:

```
const arr = [1, 2, 3].map(n => n ** 2);
```

它也返回一个新的数组，但是少了一个参数，因为它是数组中的一个方法。

# 过滤器

Lodash 中的`filter`方法返回一个新数组，该数组包含原始数组中经过过滤的条目。它采用一个数组和一个函数作为参数返回过滤器的条件。

例如，我们可以写:

```
const arr = _.filter([1, 2, 3], n => n % 2 === 0);
```

返回一个只有偶数的数组。

使用 JavaScript 数组的`filter`方法，我们可以稍微简化代码，因为它是在数组本身上调用的。我们可以把它改写成:

```
const arr = [1, 2, 3].filter(n => n % 2 === 0);
```

在这两种情况下，我们都返回了`[2]`。

# 减少

像其他两个数组方法一样，Lodash 的`reduce`方法现在有了一个同名的普通 JavaScript 等价方法。它们都允许我们将数组条目合并成一个东西。

例如，要获得数组中所有条目的总和，我们可以使用 Lodash `reduce`方法，如下所示:

```
const arr = _.reduce([1, 2, 3], (total, n) => total + n, 0);
```

Lodash 版本有 3 个参数，即要组合的数组、用于组合值的函数和组合值的初始值。

对于 JavaScript array 的`reduce`方法，除了我们直接在数组上调用它之外，它是类似的，如下所示:

```
const arr = [1, 2, 3].reduce((total, n) => total + n, 0);
```

在这两种情况下，结果都是 6。

# 头

Lodash 的`head`方法返回数组的第一个元素。例如:

```
const first = _.head([3, 4, 5]);
```

会得到 us 3 作为`first`的值。

我们可以通过编写以下代码用 JavaScript 实现这一点:

```
[3, 4, 5][0]
```

或者:

```
const [first] = [3, 4, 5];
```

![](img/31f91c922e939f00375400bc9be2ccd0.png)

塔玛拉·比利斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 尾巴

Lodash 的`tail`方法将获取除数组第一个条目之外的所有内容。

例如，我们可以如下使用它:

```
const rest = _.tail([3, 4, 5]);
```

然后我们得到`[4, 5]`作为`rest`的值。

我们可以编写以下代码，通过编写来获取数组中除第一个元素之外的所有元素:

```
const rest = [3, 4, 5].slice(1);
```

或者:

```
const [firs, ...rest] = [3, 4, 5];
```

spread 操作符将把存储在`rest`变量中的其余元素作为数组提供给我们，所以我们得到了相同的结果。

# 休息

Lodash 的`rest`方法获得传递给回调函数的额外参数，我们传递给它的参数超过了参数的数量，并返回一个函数，让我们用这些额外的参数做一些事情。

例如，如果我们有:

```
const restFn = _.rest((first, rest) => rest);
```

然后调用`restFn(“foo”, “bar”, “baz”);`将返回`[“bar”, “baz”]`。

我们让 rest 操作符在 JavaScript 的 rest 操作符中做同样的事情:

```
const restFn = (first, ...rest) => rest
```

# 传播

在 Lodash 中，`spread`方法将返回一个接受回调函数的函数，然后返回一个我们传入的函数，其中我们使用一个数组作为传入的回调函数的参数。

例如，如果我们有:

```
const spreadFn = _.spread((foo, bar) => `${foo} ${bar}`);
```

那么当我们调用`spreadFn`时如下:

```
spreadFn(["foo", "bar", "baz"])
```

我们回来了:

```
'foo bar'
```

它将获取传入`spreadFn`的数组的每个条目，并将其作为函数的参数传递给`spread`方法。

这与在 JavaScript 中调用函数是一样的:

```
const spreadFn = function(foo, bar) {
  return `${foo} ${bar}`;
}
```

然后我们可以在函数上使用`apply`方法来做同样的事情:

```
spreadFn.apply(null, ["foo", "bar", "baz"])
```

我们得到了同样的结果。

正如我们所看到的，Lodash 中的许多方法在普通 JavaScript 中都有对等物，尤其是数组方法。因此，为了减少对库的依赖，我们可以在这里使用更简单的 JavaScript 替换来替换 Lodash 方法的现有使用，当它们使代码更干净、更轻便时。