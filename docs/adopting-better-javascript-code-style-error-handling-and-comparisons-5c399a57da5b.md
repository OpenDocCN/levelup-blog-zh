# 采用更好的 JavaScript 代码风格——错误处理和比较

> 原文：<https://levelup.gitconnected.com/adopting-better-javascript-code-style-error-handling-and-comparisons-5c399a57da5b>

![](img/19fc069495268bac0d5a87391f7230d2.png)

梅森·哈森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们编写 JavaScript 的方式总是可以改进的。

在本文中，我们将看看如何通过改进代码中使用的基本语法来改进我们的代码风格。

# 错误处理

错误处理应该尽可能简单。如果我们在运行一个函数时遇到一个错误，我们应该抛出一个错误。

例如，如果遇到无效值，我们可以抛出一个错误，如下所示:

```
const greet = (name) => {
  if (typeof name !== 'string') {
    throw new Error('name not provided');
  }
  return `hi ${name}`;
}
```

在上面的代码中，我们检查`name`是否是一个字符串。如果不是，那么我们抛出一个错误。

否则，我们返回带有问候语的字符串。

这比返回错误值的替代方法要好:

```
const greet = (name) => {
  if (typeof name !== 'string') {
    return '';
  }
  return `hi ${name}`;
}
```

在上面的代码中，我们返回了一个空字符串，而不是抛出一个错误，所以我们使用函数和用户对开发人员隐藏了错误。

很容易忽略错误值，因为它们并不明显。与错误不同，错误会立即显示在控制台中，我们在调试之前看不到错误值。

这使得调试更加困难，因为错误值不会显示在控制台中。

如果我们需要处理错误，我们可以如下使用`try...catch`块:

```
const greet = (name) => {
  if (typeof name !== 'string') {
    throw new Error('name not provided');
  }
  return `hi ${name}`;
}try {
  greet();
} catch (ex) {
  console.log(ex);
}
```

在上面的代码中，我们有下面的`try...catch`块:

```
try {
  greet();
} catch (ex) {
  console.log(ex);
}
```

然后，由于我们在调用`greet`函数时没有传入任何参数，我们在控制台中从`catch`块得到一个错误。

为了更好地处理它，我们可以选择显示一个错误消息或类似的东西。

对于异步代码，我们应该尽可能使用承诺。如果遇到错误，承诺会被拒绝，就像在同步代码中抛出错误一样，它们也会出现在控制台中。

例如，如果我们有一个被拒绝的承诺，如下所示:

```
Promise.reject('error');
```

我们可以对它调用`catch`来捕捉错误并按如下方式处理它:

```
Promise.reject('error')
  .catch(err => console.log(err));
```

在上面的代码中，我们调用了`catch`方法，在`catch`方法调用中，我们传递了一个回调来记录错误。

然后，我们可以看到错误，并在异步代码中轻松处理它们。

使用`async`和`await`语法，我们可以从拒绝的承诺中捕获错误，如下所示:

```
(async () => {
  try {
    await Promise.reject('error');
  } catch (err) {
    console.log(err);
  }
})();
```

正如我们所见，如果我们使用`async`和`await`语法，我们就像使用同步代码一样使用`try...catch`。

![](img/43f3e149776326b85d96c7f3aff13328.png)

照片由[思想目录](https://unsplash.com/@thoughtcatalog?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 比较

`===`和`!==`永远比`==`和`!=`强。

这是因为`===`和`!==`在进行比较之前不会强制其操作数的数据类型。

另一方面，`==`和`!=`在进行任何比较之前对它们的操作数进行数据类型强制。

通过跳过键入一个字符，我们为自己引入了许多令人头痛的问题，因为自动类型强制。

因此，我们应该总是只键入额外的`=`符号，从而大大减少出错的机会。

为了保持条件表达式的简单，我们应该尽可能地使用快捷方式。

例如，以下是不好的:

```
if (condition === true) {
  // ...
}
```

这很糟糕，因为我们不需要与`true`进行比较来检查某个东西是否是`true`。

相反，我们应该写:

```
if (condition) {
  // ...
}
```

然后我们检查`condition`是否为真，其中包括值`true`。

如果我们使用三元表达式，我们不应该嵌套它们。例如，以下是好的:

```
const foo = cond ? 'foo' : 'bar';
```

这是因为我们只有一个条件`cond`，返回`'foo'`为`cond`为`true`否则为`'bar'`。

但是，我们不应该将多个三元表达式嵌套在一起。所以下面是不好的:

```
const foo = cond1 ? cond2 ? 'foo' : 'bar' : 'baz';
```

嵌套使得表达式难以理解。我们必须在大脑中放入括号来计算大脑中的值，这样我们才能理解它在做什么。

# 结论

错误处理是通过抛出错误，然后通过捕获错误来处理它们。

对于异步代码，我们可以拒绝承诺，然后调用`catch`或使用`catch`块来处理错误。

对于条件句，我们应该尽可能使用最短的方式。我们也不应该嵌套三元表达式。

为了进行比较，我们应该使用`===`和`!==`进行比较，这样我们就不必担心类型强制。