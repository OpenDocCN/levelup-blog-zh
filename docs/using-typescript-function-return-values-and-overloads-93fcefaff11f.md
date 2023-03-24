# 使用类型脚本—函数返回值和重载

> 原文：<https://levelup.gitconnected.com/using-typescript-function-return-values-and-overloads-93fcefaff11f>

![](img/e3a567119ad3dfa9802f030dc206689b.png)

照片由[亚里沙·安东](https://unsplash.com/@alisaanton?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将看看如何控制函数返回值和重载。

# 禁用隐式返回

JavaScript 的返回类型非常灵活。

如果函数没有隐式的`return`语句，JavaScript 函数将返回`undefined`。

这就是所谓的隐式返回特性。

为了防止隐式返回，我们可以将`compilerOptions`中的`noImplicitReturns`设置为`true`。

通过这种方式，如果函数中的路径没有使用`result`关键字显式地产生结果，就会抛出一个错误。

如果我们禁用隐式返回，那么我们必须明确返回什么。

例如，如果我们有一个参数可以是`null`:

```
const getTax = (price: number | null, ...fees: number[]): number => {
  return price * 0.2 - fees.reduce((total, fee) => total + fee, 0);
};
```

然后我们会从编译器那里得到一个关于`price`可能是`null`的警告。

要修复这个错误，我们必须使返回显式。

例如，我们可以写:

```
const getTax = (price: number | null, ...fees: number[]) => {
  if (price !== null) {
    return price * 0.2 - fees.reduce((total, fee) => total + fee, 0);
  }
  return undefined;
};
```

我们为`price`添加了一个`null`检查，如果是`null`则返回`undefined`。

# 无效函数

不返回任何东西的函数有一个`void`返回类型。

例如，我们可以这样定义一个`void`函数:

```
const greet = (): void => {
  console.log("hello");
};
```

因为我们的函数不返回任何东西，所以我们用`void`来表示。

# 重载函数类型

使用 TypeScript，我们可以重载函数。

这意味着我们可以为同名函数定义多个函数。

例如，我们可以写:

```
function getTax(price: number): number;
function getTax(price: null): number;
function getTax(price: number | null): number {
  if (price !== null) {
    return price * 0.2;
  }
  return 0;
}
```

现在我们调用`getTax`时可以带一个`number`参数或者`null`参数。

重载仅向 TypeScript 编译器提供有关各种签名的信息。

它将被合并到我们实际运行的内置 JavaScript 代码中的一个函数中。

![](img/b0d7f8641e1db32c4bc1b85cae650f74.png)

照片由[慈善机构贝丝龙](https://unsplash.com/@charitybethlong?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

使用 TypeScript，就像 JavaScript 一样，如果没有显式指定`return`语句，隐式返回`undefined`。

我们可以将`noImplicitReturns`选项设置为`true`，让 TypeScript 编译器避免隐式返回。

TypeScript 允许我们为同一个函数定义多个函数签名。

这让我们可以在一个函数中接受不同类型的数据，并以一种清晰的方式表达出来。