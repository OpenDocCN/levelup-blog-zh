# JavaScript 代码中更容易重构的东西

> 原文：<https://levelup.gitconnected.com/more-easy-things-to-refactor-in-javascript-code-bc0f4c93407>

![](img/10ea0b1320df82c65bbbc47425fbb2c6.png)

[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难写出一段干净的 JavaScript 代码。

在这篇文章中，我们将会看到更多重构的快速胜利。它们大多是语法上的东西，很容易改变。

# 条件句应该各占一行

每个条件语句都应该在自己的行中。例如，我们不应该有如下所示的代码:

```
if (foo){} if (bar){}
```

上面的代码使 2 个`if`语句看起来相互关联。因此，应该用一条新的线将它们彼此分开。

第二个`if`语句前没有`else`，所以它们彼此没有关联。

因此，我们应该将其重写如下:

```
if (foo) {}if (bar) {}
```

# 正确注释可选成员和参数

如果我们使用 TypeScript，我们可以用`?`符号注释可选的参数和成员。

例如，如果我们使用 TypeScript，我们应该编写以下代码:

```
interface Person {
    firstName: string;
    lastName: string;
    age?: number;
}
```

在上面的代码中，我们有一个 TypeScript 接口，它将`age`作为接口的可选成员。

那么我们在声明一个类型为`Person`的对象时，就不必在对象中包含`age`。

这也适用于函数参数。例如，如果我们有:

```
const getPerson = (firstName: string, lastName: string, age?: number) => {
    return {
        firstName, lastName, age
    }
}
```

我们调用`getPerson`函数的时候，不一定要用`age`来调用。

# 移除废弃商店

死存储是已经被赋值的变量值，但在将它们重新赋值给不同的值之前从未被使用过。

例如，如果我们有以下代码:

```
const getTotal = (arr) => {
  let total = 0;
  total = arr.reduce((a, b) => a + b);
  return total;
}
```

那么`let total = 0;`是一个无用的赋值，因为 0 从未被使用过。

这是令人困惑的，因为它在那里，但它不用于任何事情。因此，我们应该从代码中删除任何死存储，因为它在任何地方都没有被使用。

我们可以删除`let total = 0;`，该函数将做同样的事情。例如，我们可以改为写:

```
const getTotal = (arr) => {
  let total = arr.reduce((a, b) => a + b);
  return total;
}
```

它做同样的事情，但是少了一行。现在我们看到这一行是无用的代码，不做任何事情。

删除它可以节省页面空间，在阅读时使用更少的大脑资源，并且少运行一行代码。

# 不要反转布尔值

当我们的比较表达式使用了像`===`、`!==`、`<`、`>`、`>=`、`<=`、`!=`或`==`这样的运算符时，我们不应该颠倒整个表达式。

例如，如果我们有这样的代码:

```
if (!(1 <= total)) {
  //...
}
```

我们应该改为写:

```
if (1 > total) {
  //...
}
```

第二个例子有较少的字符，我们也没有解释双重否定的认知负担。

双重否定对我们的大脑不好，所以我们不应该有。双重否定更长，所以我们绝对不应该在代码中使用它们。

![](img/e8c5014f93b1da39f18818439a8217a5.png)

照片由 [suzie maclean](https://unsplash.com/@redcherry?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 不再连接字符串

在 JavaScript 有模板字符串之前，我们只能连接字符串。这并不好，因为当我们必须构造长的动态字符串时，这变得非常混乱。

因此，模板字符串是 ES2015 中设想的 JavaScript 的最佳特性之一。

我们应该尽可能多地使用模板字符串。例如，不写:

```
const getDescription = (firstName, lastName, age) => 'Your name is ' + firstName + ' ' + lastName + 'Your age is ' + age.
```

我们应该改为写:

```
const getDescription = (firstName, lastName, age) => `Your name is ${firstName} ${lastName}. Your age is ${age}`
```

正如我们所看到的，我们将变量直接嵌入到模板字符串中。我们不必用`+`操作符将多个字符串连接在一起。

模板字符串也与模板标签一起工作，模板标签是只能用模板字符串调用的函数。

我们不能对普通字符串使用模板标签。这是我们不应该使用它们的另一个原因。

模板字符串要干净得多，我们可以用它们创建多行字符串。此外，我们可以在分隔符`${}`中嵌入任何表达式。

# 结论

不相关的`if`语句应该从自己的行开始，这样它们看起来就不像是相关的。

此外，我们不应该在代码中有死存储，在那里我们的代码中有从未使用过的赋值。

整个布尔表达式不应该倒置。

应该始终使用模板字符串，而不是串联字符串。