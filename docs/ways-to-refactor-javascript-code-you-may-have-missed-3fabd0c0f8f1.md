# 重构您可能错过的 JavaScript 代码的方法

> 原文：<https://levelup.gitconnected.com/ways-to-refactor-javascript-code-you-may-have-missed-3fabd0c0f8f1>

![](img/730b89f7fe13b2af11ec83711f879e78.png)

克里斯·克里斯滕森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难写出一段干净的 JavaScript 代码。

在本文中，我们将看看一些你可能错过的重构代码的方法。

# 使用对象参数

对象参数是作为对象的参数。对于对象参数，我们不关心在属性中传递的顺序。

此外，我们不必担心传入太多参数，因为这只是一个具有多个属性的参数。

此外，我们不必担心添加和删除参数会破坏我们的代码，因为它仍然是一个对象。

析构赋值语法可以将对象分解成变量，所以我们可以像使用变量一样使用属性。

为了用这种模式重构我们的 JavaScript 代码，我们通过将参数组合成一个对象参数来转换它们。

使用对象参数的示例如下:

```
const greet = ({
  greeting,
  firstName,
  lastName
}) => `${greeting}, ${firstName} ${lastName}`;
```

在上面的代码中，我们有一个对象参数，它有 3 个属性，`greeting`、`firstName`和`lastName`，然后我们提取它们，并用析构语法将它们作为变量使用。

那么我们可以这样称呼它:

```
const greeting = greet({
  greeting: 'Hello',
  firstName: 'jane',
  lastName: 'smith'
})
```

然后我们看到`greeting`就是`'Hello, jane smith’`。

# 用命名回调替换匿名回调

匿名函数没有名字，所以不太清楚，因为它们没有名字来区分它们在做什么。

为了使它们更容易阅读和理解，我们可以在它们的方法之外提取它们，然后将它们赋给一个常数，并将该常数传入。

例如，我们可以编写以下代码来提取对常量的回调:

```
const double = a => a * 2;
const arr = [1, 2, 3].map(double);
```

在上面的代码中，我们有`double`常量，它被赋予了函数:

```
a => a * 2;
```

它被它下面的`map`方法用来将数组中每一项的值加倍。

现在我们知道回调用于将每个条目的值加倍，而不必读取回调的代码。

# 用对象替换图元

如果我们有很多原语，我们想分配给一个变量，就像我们改变状态一样，我们可以把它们都放到一个对象中，然后使用它们。

这样，它们都在一个地方，如果我们必须更改它们，我们不必到处更改它们。

由于 JavaScript 没有枚举，我们可以使用`Set` s 将我们的值放入其中。

例如，我们可以将所有值添加到一个集合中，如下所示:

```
const statuses = new Set(['loading', 'success', 'error', 'pending'])const setStatus = (status) => {
   if (statuses.has(status)) {
     return status;
   }
 }
```

在上面的代码中，我们有具有多种状态的`statuses`集合。然后在`setStatus`函数中，我们在返回状态之前检查状态是否存在于`statuses`集合中。

然后，我们可以确保在代码中设置了有效的状态。这类似于没有定义枚举的枚举，因为我们只能设置集合中列出的值。

![](img/1aaff2a69872075fc61b2ea9ca4ad5f5.png)

照片由[乔纳森派](https://unsplash.com/@r3dmax?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 将条件分解成变量或函数

条件表达式，尤其是长表达式，很难读懂。这是因为它们只是列出了一堆用括号连接在一起的条件，括号中有 and、or 或括号。

为了使它们更容易阅读，我们可以将布尔表达式赋给一个变量，或者在函数中返回表达式，这样我们就可以理解条件在检查什么，而不用查看条件表达式本身。

例如，我们可以编写以下代码，将条件表达式赋给变量:

```
const hasWinner = score === 100 || remainingPlayer === 0;
```

在上面的代码中，我们知道代码正在检查游戏中的赢家，因为我们看到我们将布尔表达式赋给了`hasWinner`常量。

没有布尔值，很难知道我们在检查一个游戏是否有赢家。我们只知道我们正在检查`score`是 100，而`remainingPlayer`是 0。当它们连在一起时，很难知道它们是什么意思。

我们也可以在函数中返回布尔表达式，如下所示:

```
const hasWinner = () => score === 100 || remainingPlayer === 0;
```

然后只在调用`hasWinner`的时候求值，而不是一直求值。

# 结论

对象参数让我们通过添加更多的参数来传递更多的参数，因为我们可以将它们作为属性来传递，并在函数中析构它们。

如果我们有许多重复的原始值，我们可以把它们都放在一个对象中。然后我们可以在设置值之前使用对象来检查这些项，以确保它们是有效的。

最后，我们可以将布尔表达式设置到函数或变量中，我们可以对回调做同样的事情，使它们变得清晰。