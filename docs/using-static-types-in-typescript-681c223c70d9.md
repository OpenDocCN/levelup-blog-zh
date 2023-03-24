# 在 TypeScript 中使用静态类型

> 原文：<https://levelup.gitconnected.com/using-static-types-in-typescript-681c223c70d9>

![](img/6986ea82d980dcf2461d6ce108138787.png)

卢西恩·阿利克夏在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将研究静态类型在 TypeScript 项目中的使用。

# 静态类型

TypeScript 类型不同于 JbaScript 类型。我们可以指定比 JavaScript 允许的更多的数据类型。

JavaScript 是动态类型的。这对于熟悉其他语言的编程来说是一个障碍。

在 JavaScript 中，值有类型而不是变量。

例如，如果我们有:

```
let x = 1;
```

然后我们可以用`typeof`操作符得到`x`的值的类型，即 1。

例如，我们可以写:

```
typeof x
```

我们会得到`'number'`。

如果我们不给某物赋值，那么它的类型就是`undefined`。

类型为`undefined`的值只能是`undefined`。

JavaScript 只有以下内置类型。

*   `number` —表示数值的数据类型
*   `string` —表示文本数据的数据类型
*   `boolean` — `true`或`false`
*   `symbol`—表示唯一常数值的数据类型
*   `null` —值`null`，用于表示不存在或无效的引用
*   `undefined` —已定义变量但尚未赋值时使用的数据类型
*   `object` —表示复合值。

变量可以被赋予这些类型中的任何一种。

函数参数类型也是动态的。这意味着参数可以接收任何类型的数据。

因此，为了让我们的生活更轻松，我们可以使用 TypeScript 来帮助我们定义一些类型。

# 使用类型批注创建静态类型

我们可以用 TypeScript 创建带有类型注释的静态类型。

TypeScript 中最基本的数据类型是静态类型。

我们可以用数据类型注释来注释变量和参数，以使我们的假设明确。

同样，我们可以对参数做同样的事情。

例如，我们可以写:

```
const add = (a: number, b: number): number => {
  return a + b;
};
```

我们有`add`函数，它有参数`a`和`b`，两者都是数字。

我们还会在将它们相加后返回一个数字。

这是最基本的类型注释，有助于防止出错。

如果我们传入任何不是数字的东西，我们将从 TypeScript 编译器得到错误。

同样，我们可以给变量添加类型注释。

例如，我们可以写:

```
let price: number = 100;
```

限制`price`只能被分配号码。

# 隐式定义的静态类型

如果类型从赋值中显而易见，那么我们就不必显式地编写数据类型注释。

例如，如果我们有上面的例子:

```
let price: number = 100;
```

我们可以去掉类型注释，写成:

```
let price = 100;
```

TypeScript 编译器将自动推断类型。

同样，我们可以对返回类型做同样的事情。

例如，我们可以写:

```
const getTax = (price: number) => {
  return (price * 0.2).toFixed(2);
};const halfTax = getTax(100) / 2;
console.log(tax);
```

我们将在倒数第二行得到错误“算术运算的左侧必须是' any '、' number '、' bigint '或 enum type.ts(2362)'。

这是因为 TypeScript 编译器知道我们只能将一个数除以 2。

如果我们的代码没有编译器错误，那么编译器会在我们运行代码时编译它。

![](img/f1fa6e37a1e6d873367ad3e123edfa2b.png)

米哈伊尔·瓦西里耶夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 任何类型

TypeScript 并没有阻止我们灵活使用数据类型。

它为我们提供了`any`时间来绕过编译器所做的数据类型检查。

然而，它确实阻止了我们不小心使用它。

我们可以把`getTax`改成:

```
const getTax = (price: any): any => {
  return (price * 0.2).toFixed(2);
};
```

这样我们就可以返回任何我们想要的东西，并接受任何我们想要的参数。

然而，我们不应该过多地使用`any`，这样我们就可以利用 TypeScript 的优势。

# 结论

我们可以将静态数据类型用于基本数据类型注释。

TypeScript 还会针对明显的情况进行类型推断，因此我们不必总是添加数据类型注释。

另外，它有`any`类型，可以让我们给变量、参数赋值，或者返回任何东西，但是我们不应该过多地使用它。