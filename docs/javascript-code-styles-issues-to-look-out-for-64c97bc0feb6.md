# 需要注意的 JavaScript 代码风格问题

> 原文：<https://levelup.gitconnected.com/javascript-code-styles-issues-to-look-out-for-64c97bc0feb6>

![](img/7d04bc4d1e7093d8e9414fdcd73234f9.png)

由[迪潘卡尔·戈戈伊](https://unsplash.com/@dipankargogoi55?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将关注一些 JavaScript 代码风格的问题，以便代码易于阅读和理解。

# 花括号

有时候，我们可以在 JavaScript 中定义一个没有花括号的块。例如，只有一行的`if`块可以不用花括号来写。例如，如果我们有:

```
let foo = true;
let bar = 1;
if (foo) 
  bar = 2;
```

那么`bar`将被赋值为 2，因为`foo`是`true`并且`bar = 2`被认为是`if`块的一部分。

然而，如果我们有:

```
let foo = false;
let bar = 1;
let baz;
if (foo)
  bar = 2;
  baz = 3;
```

然后我们得到`bar`是 1，但是`baz`是 3。这是因为`baz = 3`不是`if`块的一部分，但它看起来像是。

为了避免这种歧义，我们应该总是用花括号将`if`块括起来，以消除任何可能的歧义。

因此，我们应该写:

```
let foo = true;
let bar = 1;
if (foo) {
  bar = 2;
}
```

并且:

```
let foo = false;
let bar = 1;
let baz;
if (foo){
  bar = 2;
  baz = 3;
}
```

# Switch 语句中的默认情况

JavaScript `switch`语句不需要默认的大小写。然而，我们有时希望包含 ut，以便不管设置了什么值，它都返回一些东西。

例如，我们可能想给`switch`语句添加一个`default`块，以避免返回`undefined`，如下所示:

```
const foo = (a) => {
  switch (a) {
    case 1:
      return 'one';
    default:
      return 'bar';
  }
}
```

这样，我们就不必检查`foo`是否返回了`undefined`。我们知道它总是会返回一些东西。

# 默认参数应该是最后一个

默认参数最好放在最后，这样函数调用可以跳过尾部参数。可选参数只能在没有默认值的所有参数之后省略。

因此，与其写:

```
const foo = (b = 1, a) => {}
foo(undefined, 0);
```

我们应该写:

```
const foo = (a, b = 1) => {}
foo(0);
```

因此，除了必需的参数之外，我们不必为带有默认值的参数传递参数。

# 放置点和换行符的一致性

JavaScript 允许我们在任何地方放置点和换行符。保持一致性使我们的代码更具可读性。例如，如果我们想写:

```
const a = foo.
  bar;
```

然后我们总是用行尾的`.`来写代码。

我们也可以选择将`.`写在该行之前:

```
const a = foo
  .bar;
```

此外，我们可以将代码与其他内容放在一行:

```
const a = foo.bar;
```

位置的一致性使代码更具可读性。

![](img/0b959258809442784811df97d644d067.png)

约翰·邓肯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 尽可能使用点符号

点符号是访问对象属性最常用的语法。如果属性名是有效的标识符，则可以使用它。每当我们试图访问静态属性时，都可以使用点语法。

如果我们的 JavaScript 代码需要动态访问对象属性，如果对象属性不是一个有效的标识符，或者如果标识符是一个符号，那么我们必须使用括号符号。

理想情况下，我们用点符号而不是括号符号来访问大多数属性，这样 JavaScript bundlers 和 minify 化代码会更积极。它也更容易阅读，更少罗嗦。

例如，这是我们大多数时候想要的:

```
let x = foo.bar;
```

比:

```
let x = foo['bar'];
```

但是，如果我们的对象属性名被赋给一个变量，那么我们别无选择，只能使用括号符号:

```
let bar = 'bar';
let x = foo[bar];
```

或者:

```
let foo = {};
let bar = Symbol('bar');
let x = foo[bar];
```

# 结论

花括号对于消除代码中的任何歧义非常重要。例如，单行的`if`块应该用花括号括起来以避免歧义。有了它们，我们就不用猜周围的线是不是`if`块的一部分了。

理想情况下，默认情况应该在`switch`语句中，这样我们就可以假设它总是返回一些东西。

将默认参数放在最后更好，因为如果它们在最后，我们可以在调用带有默认参数的函数时跳过它们。

点的位置应该是一致的，这样代码更容易阅读。此外，应该尽可能多地使用点符号，以便更积极地缩小代码，使代码更短，更容易阅读。