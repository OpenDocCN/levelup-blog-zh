# 使用类型脚本—函数

> 原文：<https://levelup.gitconnected.com/using-typescript-functions-e847c1fec728>

![](img/1a3e138a0de586837dc75ad8a7a1be4b.png)

[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将研究如何在我们的 TypeScript 代码中使用函数。

# 定义函数

TypeScript 通过提高函数的可预测性来增强函数的 JavaScript 语法。

我们可以显式地添加假设，以便 TypeScript 编译器可以对其进行检查。

例如，下面是一个常规的 JavaScript 函数:

```
const getTax = price => {
  return price * 0.2;
};
```

它没有数据类型注释，我们可以把任何东西作为参数传入。

# 重新定义功能

TypeScript 为隐式推断类型和显式数据类型批注提供警告。

只要 TypeScript 编译器能够检查数据类型，我们就会得到任何数据类型不匹配的错误。

如果我们希望一个函数能够处理不同类型的数据，我们定义一个函数来做所有的事情。

JavaScript 不允许函数重载，所以我们只能用给定的名字定义一个函数。

# 功能参数

TypeScript 和 JavaScript 处理函数参数的方式不同。

TypeScript 要求实参的数量与形参的数量相同。

如果实参的数量与形参的数量不匹配，那么编译器就会出错。

如果一个函数定义了一个它不使用的参数，编译器有`noUnusedParameters`选项来警告我们。

# 可选参数

默认情况下，函数参数在 TypeScript 中是必需的。

然而，我们可以用`?`符号使它们可选。

例如，我们可以写:

```
const getTax = (price: number, discount?: number) => {
  return price * 0.2 - discount;
};
```

那么`discount`将是可选的。`price`是必填项，因为名称后面没有`?`。

为了保证我们在做计算的时候不会有意外的结果，我们要保证`discount`是数字。

我们可以使用`||`来做到这一点:

```
const getTax = (price: number, discount?: number) => {
  return (price * 0.2) - (discount || 0);
};
```

那我们就不用担心`discount`是`undefined`了。

# 具有默认值的函数参数

我们可以有一个带默认值的参数。语法继承自 JavaScript。

例如，我们可以写:

```
const getTax = (price: number, discount: number = 0) => {
  return price * 0.2 - discount;
};
```

我们将`discount`设置为默认值 0。

现在我们在调用`getTax`的时候不用检查`undefined`的值了。

# 休息参数

rest 参数是默认参数的替代。它允许函数接受可变数量的参数。它们被分组并作为一个数组呈现在一起。

一个函数只能有一个 rest 参数。而且，它必须是最后一个参数。

例如，我们可以写:

```
const getTax = (price: number, ...fees: number[]) => {
  return price * 0.2 - fees.reduce((total, fee) => total + fee, 0);
};
```

`fees`被设置为`number[]`类型，因此我们只能将数字作为参数传入。

在函数体中，我们可以安全地将所有值相加，而不用担心任何参数都不是数字。

# 函数参数的类型注释

可以为函数参数添加类型注释，正如我们从前面的例子中可以看到的，我们为参数添加了数据注释，包括 rest 参数。

![](img/68d603f3d1dc080a3e820bf4c15ca472.png)

照片由 [Edgar Castrejon](https://unsplash.com/@edgarraw?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 空参数值

`null`和`undefined`可以作为 TypeScript 中所有类型的值。

因此，我们可以将`null`或`undefined`作为函数值传入。

为了防止这种情况发生，我们可以使用`strictNullChecks`来禁止使用`null`和`undefined`作为所有类型的值。

# 函数结果

我们可以添加返回类型数据注释，这样我们就可以返回我们想要的类型。

例如，我们可以写:

```
const getTax = (price: number, ...fees: number[]): number => {
  return price * 0.2 - fees.reduce((total, fee) => total + fee, 0);
};
```

现在我们只能只能返回一个数字。

然而，它也可以隐式地返回`undefined`，但是对于参数的类型注释，这是不可能的。

# 结论

通过向参数添加数据类型注释，TypeScript 允许我们向 JavaScript 函数添加更多的数据类型安全性。

同样，我们可以设置默认值，这样当我们调用没有参数的函数时就不会有`undefined`。

除非我们指定了 rest 参数，否则 TypeScript 还将检查参数和实参的数量是否默认匹配。