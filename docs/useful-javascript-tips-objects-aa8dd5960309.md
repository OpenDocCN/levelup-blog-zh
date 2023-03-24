# 有用的 JavaScript 技巧—对象

> 原文：<https://levelup.gitconnected.com/useful-javascript-tips-objects-aa8dd5960309>

![](img/dedc775c730967d156fd74cdbd76033e.png)

照片由 [Ignacio Brosa](https://unsplash.com/@ignaciobrosa?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

像任何类型的应用程序一样，JavaScript 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

在本文中，我们将了解一些应该遵循的技巧，以便更快更好地编写 JavaScript 代码。

# 挑选和拒绝对象属性

我们可以使用`Object.keys`方法来获取密钥并选择我们想要的密钥。

例如，我们可以写:

```
const reject = (obj, keys) => {
  return Object.keys(obj)
    .filter(k => !keys.includes(k))
    .map(k => Object.assign({}, {[k]: obj[k]}))
    .reduce((res, o) => Object.assign(res, o), {});
}
```

在上面的代码中，我们调用了`Object.keys`来获取密钥。

然后我们调用`map`将对象键映射到他们自己的对象，以键的属性值作为值。

然后我们使用`reduce`将来自`map`的对象数组合并成一个带有`reduce`的对象。

我们可以这样称呼它:

```
const obj = {
  foo: 1,
  bar: 2,
  bar: 3
}
const newOb = reject(obj, ['foo', 'bar']);
```

# 使用 Object.is 进行对象比较

我们可以使用`Object.is`方法进行比较。`===`和`Object.is`的区别在于`NaN`等于自身。

例如，我们可以写:

```
Object.is(NaN, NaN);
```

它会返回`true`。

# 创建记录值的函数

我们可以创建一个记录值并返回它的函数，这样我们就可以在调试时同时完成这两项工作。

例如，我们可以写:

```
const tap = (x) => {
  console.log(x);
  return x;
}
```

然后我们记录`x`并用函数返回。

这对于以下情况很方便:

```
.filter(c => tap(c.numItems > 100))
```

然后我们可以记录快递的值，同时返回它。

# 数组技巧

我们可以用数组做一些我们可能还没有想到的事情。

# 遍历空数组

我们可以用数组构造函数创建一个空槽数组。

为此，我们可以写:

```
const arr = new Array(10);
```

将一个数字作为唯一的参数传入，使构造函数返回具有给定数量的空槽的数组。

因为我们传入了 10 个，所以我们得到了一个有 10 个空槽的数组。

# 填充空数组

我们可以使用`fill`来填充空数组。

例如，我们可以写:

```
const arr = new Array(10);
const nums = arr.fill().map((_, i) => i)
```

创建一个从 0 到 9 的数组。

我们调用`fill`来用`undefined`填充空槽。然后我们可以用回调来调用`map`返回数组索引，用数字填充它。

# 将空参数传递给方法

我们可以传入`null`或`undefined`来跳过参数。

例如，我们可以写:

```
foo('a', null, 'b')
```

或者:

```
foo('a', undefined, 'b')
```

但是我们也可以使用 spread 运算符，并写出:

```
foo(...['a', , 'b']);
```

然后我们可以跳过数组中的一个条目。

当我们将条目作为参数传播时，它会变成`undefined`,所以我们像前面的例子一样做同样的事情。

# 唯一数组值

我们可以通过将一个数组转换成一个集合，然后用 spread 操作符将它转换回一个数组，来删除数组中的重复项。

例如，我们可以写:

```
const arr = [...new Set([1, 2, 3, 3])];
```

然后我们得到:

```
[1, 2, 3]
```

作为`arr`的值。

# 将对象绑定到函数

我们可以使用`bind`返回一个我们想要的值为`this`的函数。

例如，如果我们有:

```
const getName = function () {
 console.log(this.name);
};
```

那么如果我们写:

```
const getJoe = getName.bind({ name: 'joe' });
```

那么`this.name`将是`getJoe`中的`'joe'`，因为`bind`返回一个函数，其值为第一个参数中给出的`this`。

同样，我们可以写:

```
const getJane = getName.bind({ name: 'jane' });
```

那么`this.name`就是`'jane'`。

![](img/3fde074bf93cdc46ad3bfbf8947e0685.png)

照片由[德韦恩·希尔斯](https://unsplash.com/@dhillssr?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 结论

我们可以用`Array`构造函数创建带有空槽的数组。

然后，我们可以使用`fill`方法来填充条目。

我们可以使用`Object.keys`和`Object.assign`返回一个没有某些属性的新对象。

同样，我们可以使用`Object.is`来比较包括`NaN`在内的值。所以比`===`稍微有用一点。