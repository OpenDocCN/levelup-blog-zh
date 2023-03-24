# JavaScript 最佳实践—数组和箭头函数

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-arrays-and-arrow-functions-1893a49d12d8>

![](img/b1311496b3d176e039829e8d72d22091.png)

照片由[达伦·努尼斯](https://unsplash.com/@dnunis?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，JavaScript 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

在本文中，我们将研究一些使用数组和箭头函数的最佳实践。

# 使用映射来映射项目

`map`方法非常适合将数组条目从原始的映射到其他东西。

这样，我们就不需要使用循环来完成。

所以与其写:

```
for (const a of arr) {
  cubed.push(a ** 3);
}
```

我们写道:

```
const cubed = arr.map(a => a ** 3);
```

这个更短更干净。

# 这个论点没有用

不需要做就不要引用`thsis`。例如，当它被用作回调函数时，我们不应该在箭头函数中引用它。

我们应该这样写:

```
const cubed = arr.map(a => a ** 3);
```

而不是:

```
const cubed = arr.map(a => a ** 3, this);
```

或者:

```
const containsE = array.some((char) => char === 'e', this);
```

# 避免倒车

如果我们不使用`reverse`来反转数组，我们应该避免使用它。

如果我们只是试图从数组的末尾向左组合结果，我们可以使用`reduceRight`来代替。

例如，不要写:

```
const sum = array.reverse().reduce((total, c) => total + c, 0);
```

我们应该写:

```
const reverseSum = array.reduceRight((total, c) => total + c, 0);
```

我们跳过了反向操作，做了同样的事情。

这意味着我们编写的代码更少，代码更快，因为我们跳过了一个操作。

# 使用平面地图

最新版本的 JavaScript 有一个`flatMap`方法来映射数组项，并将数组的嵌套减少 1 级。这比一起使用`map`和`flat`要好。

例如，不要写:

```
const flattenedAndMapped = array.map((a) => a).flat();
```

我们可以写:

```
const oneAction = array.flatMap((b) => b);
```

如果不需要贴图，也可以用`flat`:

```
const flattened = array.flat();
```

# 使用平板

要展平数组，我们不再需要使用`reduce`或`concat`。这是因为数组实例现在有了`flat`方法来减少嵌套。

例如，不写:

```
const concatFlattened = [].concat(...array);
```

或者:

```
const reduceFlattened = array.reduce((p, n) => p.concat(n), []);
```

我们写道:

```
const flattened = array.flat();
```

# 没有未使用的参数

在我们的箭头函数中不应该有未使用的参数。

因为它们没有被使用，我们应该把它们移除。

例如，我们写道:

```
const fn = (data, user) => request(user.id, data);
```

而不是写:

```
const fn = (data, user) => request(user.id);
```

`data`在上面的例子中没有用到，所以我们应该把它去掉。

# 参数数量

我们不应该有太多的参数。我们拥有的参数越多，就越难跟踪它们。

我们可能在错误的地方传递参数，也很容易传递错误类型的数据。

理想情况下，我们的函数中有 3 个或更少的参数。

例如，好的函数是:

```
const fn0 = () => "";
```

或者:

```
const fn3 = (one, two, three) => one * two * three;
```

但是下面的就不好了:

```
const fn3 = (a, b, c, d) => a * b * c * d;
```

如果我们需要更多，那么我们可以传入一个对象或者使用 rest 操作符。

例如，我们可以写:

```
const fn = ({ a, b, c, d }) => a * b * c * d;
```

我们使用析构语法来析构对象中的属性。

然后我们可以在一个对象中传递任意多的属性。

此外，我们可以使用 rest 运算符，如下所示:

```
const fn = (a, b, c, ...d) => a * b * c * d[0];
```

`d`是一个参数，它的第四个位置的参数存储在数组中。

# 命名功能

如果我们需要在多个地方使用箭头函数，那么我们应该通过将它们赋给一个变量来命名它们。

例如，我们可以写:

```
cosnt fn = (a, b) => a ** b;
```

现在我们可以通过写`fn(1, 2)`来调用它。

我们也可以把它们变成一个物体:

```
const obj = {
  fn: (a, b) => a ** b;
}
```

那么我们可以写成`obj.fn(1, 2)`来称呼它；

![](img/a757b2dbf269d64f1251b0ada1ec7bb3.png)

[叶尔林·马图](https://unsplash.com/@yerlinmatu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

数组实例中有许多方法可以用来操作它们。

此外，还有更好的方法来定义和使用箭头函数。