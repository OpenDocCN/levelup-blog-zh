# JavaScript 最佳实践—箭头、常量和重复成员

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-arrows-const-and-duplicate-members-7825c857b99c>

![](img/6ccdc462f29308eb3af55e822e533dfe.png)

照片由 [Jungwoo Hong](https://unsplash.com/@oowgnuj?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将看看令人困惑的箭头，分配给`const`常量，以及重复成员。

# 可能与比较混淆的箭头函数

箭头函数有一个`=>`粗箭头，对于不完全熟悉 JavaScript 的人来说，这个箭头可能会与不等式比较运算符`<=`或`=>`相混淆。

因此，我们可能希望通过不使用看起来像比较表达式的箭头函数来使我们的代码对他们来说更容易理解。

例如，以下函数可能会让一些人感到困惑:

```
const foo = a => 1;
```

我们有一个`foo`函数，它有一个参数`a`并返回 1。

然而，有些人可能会将此与:

```
const foo = a >= 1;
```

或者:

```
const foo = a <= 1;
```

其分别比较`a`是否大于或等于 1 或者`a`是否小于或等于 1。

因此，我们可能希望用花括号将函数体括起来，或者用括号将函数签名括起来，从而使我们的箭头函数不那么容易混淆。

例如，我们可以用下面的方式重写`foo`函数:

```
const foo = a => {
  return 1
};
```

上面的代码通过表明我们想要返回值 1，使我们的函数变得清晰。

我们也可以改写如下:

```
const foo = (a) => 1;
```

括号让我们的代码读者清楚地知道`a`是一个参数，而不是一个我们想与 1 比较的变量。

# 没有使用`const`声明的修改变量

在 JavaScript 中，用`const`声明的常量不能被重新赋值。

如果我们编写类似下面的代码，那么我们会得到一个错误:

```
const a = 1;
a = 2;
```

当我们运行上面的代码时，我们会得到错误“未捕获的类型错误:常量变量赋值”并且代码将停止运行。

因此，我们应该注意不要这样做。如果我们希望`a`能够被重新赋值给一个不同的值，那么我们应该用`let`来声明它。

例如，我们改为编写以下内容:

```
let a = 1;
a = 2;
```

这样，`a`被声明为一个变量而不是常量，因此它可以被重新赋值。

其他做赋值运算的运算符如`+=`、`-=`、`*=`、`/=`、`%=`也不能用`const`常量。

例如，如果我们编写以下代码，我们会得到相同的错误:

```
const a = 1;
a += 2;
```

用`const`声明的循环变量也不能被重新赋值。例如，我们会得到一个错误，如果我们写:

```
for (const a in [1, 2, 3]) {
  a = 1;
}
```

在上面的代码中，我们试图将`a`重新赋值为 1，但同样无效。

![](img/0fc78baee9dc90bda9b578e51ad57456.png)

[乔·格林](https://unsplash.com/@jg?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 类中有重复的成员名

我们不希望类中有重复的成员名。这是因为很难确定哪一个才是真正被保留下来的。

例如，我们不应该写这样的代码:

```
class Foo {
  bar() {
    console.log("foo");
  } bar() {
    console.log("bar");
  }
}
```

在上面的代码中，我们有 2 个`bar`实例方法。第二个会被保留，所以第一个是无用的。

因此，当我们调用`bar`方法时如下:

```
const foo = new Foo();
foo.bar();
```

我们将看到控制台日志输出中记录了`'bar'`。

因此，我们应该只保留我们想要保留的一个，或者在两个都需要时重命名其中一个。

我们可以这样写:

```
class Foo {
  foo() {
    console.log("foo");
  } bar() {
    console.log("bar");
  }
}
```

然后，我们可以调用两个实例方法，并在控制台中查看两者的记录值。

# 结论

我们可能想要重写可能与比较表达式混淆的箭头函数。

为此，我们可以将函数签名放在括号中，或者在函数体中添加花括号。

我们不应该将`const`常量重新赋值给另一个值。所以它是一个常数。

此外，我们不应该在一个类中有多个同名的成员。这是无用和混乱的，因为后面定义的那个会覆盖上面的那个。