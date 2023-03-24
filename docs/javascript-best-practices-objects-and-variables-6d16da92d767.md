# JavaScript 最佳实践—对象和变量

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-objects-and-variables-6d16da92d767>

![](img/5906edf9c86288f1351e4515d2852b1c.png)

安娜·沙利文在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将探讨处理对象和变量的最佳方式。

# 使用变量访问属性时，请使用括号表示法

当我们用变量访问属性时，我们应该使用括号符号。

例如，我们可以写:

```
const getVal = (prop) => {
  return foo[prop];
}const bar = getProp('jedi');
```

上面的代码有一个`getVal`函数将`prop`传递给对象`foo`。

# 计算指数时使用指数运算符

我们应该用`**`求幂，而不是用`Math.pow`。

例如，我们可以写:

```
const result = 2 ** 5;
```

而不是写:

```
const result = Math.pow(2, 5);
```

# 变量

当我们声明 JavaScript 变量时，需要考虑一些事情。

# 总是使用 let 或 const 来声明变量

我们应该总是使用`let`或`const`来声明变量。

否则，我们最终会得到全局变量。

例如，不写:

```
foo = 1;
```

或者:

```
var foo = 1;
```

我们写道:

```
let foo = 1;
```

或者:

```
const bar = 2;
```

`const`值不能被重新分配，但是被分配的值仍然是可变的。

# 每个变量或赋值使用一个 const 或 let 声明

我们应该在每一行声明变量。

这样，我们就可以准确地知道每个变量的类型。

例如，不写:

```
const foo = 1, bar = 2;
```

我们写道:

```
const foo = 1;
const bar = 2;
```

# 将所有 const 和 let 语句组合在一起

将它们组合在一起会使阅读更容易。

例如，不写:

```
let foo = 1;
const bar = 2;
let baz = 3;
const qux = 4;
```

我们写道:

```
let foo = 1;
let baz = 3;const bar = 2;
const qux = 4;
```

# 在我们需要的地方分配变量

我们应该在需要使用变量的地方分配变量。

这样，我们就不必跟随许多行代码来查看它们在哪里被使用。

例如，不写:

```
let x = 1;
// 50 lines of code that doesn't change x ...
x = 2;
```

我们写道:

```
let x = 1;
// 5 lines of code that doesn't reference x...
x = 2;
```

# 不要链接变量赋值

我们不应该链接变量赋值。

这是因为我们将从中创建全局变量。

例如，如果我们有:

```
let a = b = c = 2;
```

那么`b`和`c`就是全局变量。

所以我们应该写:

```
let a = 2;
let b = a;
let c = a;
```

相反。

# 避免使用递增或递减运算符

递增和递减运算符都更新值并同时返回它们。

根据运算符的位置，它可能返回更新的值或旧值。

如果我们将`++`或`--`放在操作数之前，那么新值将被返回。

否则，将返回旧值。

例如，如果我们写:

```
let x = 1;
const y = ++x;
```

那么`y`是 2，因为`x`的更新值被返回。

另一方面，如果我们写:

```
let x = 1;
const y = x++;
```

那么`y`为 1，因为`x`的旧值被返回。

这也适用于`--`

因此，我们应该使用`+=`和`-=`操作符，并使递增和递减操作符成为一个语句。

例如，我们可以写:

```
x += 1;
```

或者:

```
x -= 1
```

相反。

![](img/5e7a0cd1d8ecfbc5706b92a99e4e1b3f.png)

[杆长](https://unsplash.com/@rodlong?utm_source=medium&utm_medium=referral)在[防溅板](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍照

# 在赋值中避免在=之前或之后换行

我们可以用括号将一个长的 JavaScript 表达式括起来，这样我们就可以清楚地看到我们给变量赋了什么。

例如，不写:

```
const foo = 
  longExpression;
```

我们可以写:

```
const foo = (
  longExpression
);
```

# 结论

我们应该总是使用`let`或`const`来声明变量。

另外，我们应该使用`+=`或`-=`操作符，而不是`++`或`--`分别用于递增和递减。

如果我们有很长的表达式，我们应该在赋值前把它们用括号括起来。

我们应该尽可能多地使用点符号来访问属性。