# 您可能错过的方便的 JavaScript 技巧

> 原文：<https://levelup.gitconnected.com/handy-javascript-tricks-you-may-have-missed-20185f9c1c48>

![](img/752e0bc845abbe4c149b3ddb76c9177d.png)

[Keagan Henman](https://unsplash.com/@henmankk?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

随着 JavaScript 的不断改进，它已经引入了许多特性，使得代码更加清晰易读。

在这篇文章中，我们将看看一些可以节省你的时间和打字的技巧。

# 从数组中删除重复项

我们可以将一个数组转换成一个`Set`，然后再转换回来删除重复的值。

例如，我们可以编写以下代码来实现这一点:

```
let arr = [1, 2, 2, 2, 2, 3, 4, 5]
arr = [...new Set(arr)]
```

在上面的代码中，我们有一个包含多个 2 的数组。一旦我们将数组放入`Set`构造函数中，并使用 spread 运算符将其转换回数组，我们会得到:

```
[1, 2, 3, 4, 5]
```

作为`arr`的新价值。

# 从数组中消除虚假值

我们可以通过对数组调用`filter`来消除数组中的虚假值，然后返回传递给`Boolean`函数的项。

例如，我们可以写:

```
let arr = [false, true, undefined, 'abc', 1]
arr = arr.filter(Boolean)
```

在上面的代码中，我们在`arr`数组中有类似`false`和`undefined`的 falsy 值。

我们通过使用传入的`Boolean`函数调用`filter`来从`arr`中消除它们。

这是因为`Boolean`函数接受一个参数，也就是我们想要转换成布尔值并返回它的任何东西。此外，我们在传入`filter`的回调中不需要索引和原始数组。

所以，我们可以把它作为我们的回调。

# 创建空对象

我们可以用`Object.create(null)`创建一个没有原型的对象，因为`Object.create`的参数是我们对象的原型。

`Object.create`返回新的对象，所以我们可以给一个变量赋值如下:

```
let foo = Object.create(null)
```

# 合并对象

我们可以使用`Object.assign`或扩展操作符来合并对象。

例如，我们可以写:

```
const foo = {
  a: 1,
  b: 2
}
const bar = {
  b: 3,
  c: 3
}
const obj = Object.assign({}, foo, bar);
```

然后`obj`具有值`{a: 1, b: 3, c: 3}`，因为`bar.b`的值覆盖了`foo.b`的值，因为`bar`后来被并入。

我们也可以使用 spread 运算符来做同样的事情:

```
const foo = {
  a: 1,
  b: 2
}
const bar = {
  b: 3,
  c: 3
}
const obj = {
  ...foo,
  ...bar
};
```

那么我们应该为`obj`得到相同的值。spread 操作符和`Object.assign`都返回一个新的 obj，其中包含了合并的条目。

# 需要函数参数

如果一个必需的参数没有传递给函数，我们会抛出一个错误。

例如，我们可以写:

```
const isRequired = () => {
  throw new Error('name is required');
};const greet = (name = isRequired()) => {
  console.log(`Hello ${name}`)
};
```

如果没有传入`name`，我们就调用`isRequired()`，因为它是默认参数。如果我们为`name`传递一些东西，那么`isRequired`将被忽略。

因此，如果我们传入如下内容:

```
greet('Jane')
```

我们将记录`Hello Jane`而不是错误。

![](img/b2a3f1dc745c7f7141a7d63c1dc0ed91.png)

迪伦·科莱特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 解构别名

我们可以使用析构赋值语法将对象项赋给变量。

例如，我们可以写:

```
const obj = { a: 1 };
const { a } = obj;
```

然后，JavaScript 解释器将获取`obj.a`的值，并将其分配给`a`，因为左右两边的位置和名称都匹配。

# 获取查询字符串参数

我们可以通过使用`URL`构造函数从 URL 获取查询字符串。

例如，我们可以写:

```
const search = new URL('http://example.com?a=1&b=2').search;
const query = new URLSearchParams(search);
const queryParams = [...query];
```

在上面的代码中，我们将 URL 放在了`URL`构造函数中。然后我们得到带有`search`属性的查询字符串。

然后，我们将查询字符串传递给`URLSearchParams`构造函数，它返回一个迭代器，查询键值对存储在一个数组中。

使用 spread 操作符，我们将迭代器转换成一个数组。

最后，我们得到:

```
[
  [
    "a",
    "1"
  ],
  [
    "b",
    "2"
  ]
]
```

作为`queryParams`的值。

# 结论

这张单子上有很多方便的窍门。所有这些都可以从 ES6 或更高版本中获得，所以确保我们在代码中使用最新版本的 JavaScript。