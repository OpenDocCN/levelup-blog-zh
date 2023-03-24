# JavaScript 最佳实践—案例和循环

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-cases-and-loops-9e9f5274d680>

![](img/acf33369b5aed84725919b0d7b29abf1.png)

照片由[艾米丽·巴鲁法尔迪](https://unsplash.com/@emilygreta?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将看看如何使用`case`语句以及编写循环的最佳实践。

# 按字母或数字顺序排列案例

我们可能希望按字母或数字顺序排列案例，以便于阅读。

例如，我们可以写:

```
switch (val) {
  case 1:
    //...
    break;
  case 2:
    //...
    break;
  default:
    //...
    break;
}
```

或者写:

```
switch (val) {
  case 'a':
    //...
    break;
  case 'b':
    //...
    break;
  default:
    //...
    break;
}
```

# 把正常情况放在首位

如果我们把正常情况放在第一位，那么我们就不必经常因为短路而去考虑其他情况。

因此，如果我们将`'success'`情况作为正常情况，我们可以写:

```
switch (val) {
  case 'success':
    //...
    break;
  case 'error':
    //...
    break;
  default:
    //...
    break;
}
```

# 保持每个案例的操作简单

案例应该简短以保持可读性。如果`case`语句或块中的动作很复杂，我们应该编写函数来包含它们。

例如，我们可以编写以下内容:

```
switch (val) {
  case 'success':
    handleSuccess();
    break;
  case 'error':
    handleError();
    break;
  default:
    //...
    break;
}
```

# 不要为了使用 case 语句而编造虚假的变量

case 语句应该用于容易分类的数据。

因此，我们应该这样写:

```
const action = command[0];
switch (action) {
  case 'a':
    //...
    break;
  case 'b':
    //...
    break;
  default:
    //...
    break;
}
```

我们传递给`switch`的变量应该是一个简单的变量。在检查案例之前，我们不应该做像从字符串中取出第一个字母这样的事情。

第一个字母可以由多个单词使用，所以我们希望在我们的案例中避免使用第一个字母。

# 仅使用 default 子句来检测合法的默认值

`default`条款应该只用于真正的违约。

如果我们没有一个真正的默认值，那么我们就不需要一个`default`子句。

# 使用 default 子句检测错误

如果有不应该发生的情况，我们可以使用`default`子句来捕捉这些情况。

例如，我们可以这样写:

```
switch (action) {
  case 'a':
    //...
    break;
  case 'b':
    //...
    break;
  default:
    handleError();
    break;
}
```

然后我们在默认情况下处理错误，以防遇到错误。

# 避免跳过 case 语句的结尾

JavaScript 不会在每个 case 语句或块的末尾自动中断。

因此，我们应该编写`break`关键字，在语句或块的末尾停止执行。

例如，不要写:

```
switch (action) {
  case 'a':
    //...    
  case 'b':
    //...    
}
```

我们写道:

```
switch (action) {
  case 'a':
    //...    
    break;
  case 'b':
    //...    
    break;
}
```

![](img/64b87fb426239144a37d145d736a9ba3.png)

照片由[缺口缺口](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)在[缺口](https://unsplash.com?utm_source=medium&utm_medium=referral)处拍摄

# 何时使用 while 循环

我们应该使用`while`来测试循环结束时的退出条件。

为此，我们写道:

```
while (!done) {
  if (condition) {
    done = true;
  }
  //...
}
```

这将检查循环体开始时的情况。

我们也可以通过将`if`块移动到末端来检查末端。

此外，在检查继续循环的条件之前，我们使用了`do...while`循环来首先运行循环体。

例如，我们可以写:

```
do {
  if (condition) {
    done = true;
  }
  //...
}
while (!done)
```

那么第一次迭代总是运行。

# 使用带出口的循环

我们也可以把一个循环条件放在循环体的中间。

然后，我们可以避免在退出检查之前产生的任何无效值所导致的错误。

例如，我们可以写:

```
while (!done) {
  //...
  if (length < 0 || width < 0) {
    done = true;
  }
  //...
}
```

然后，如果`length`或`width`小于 0，我们可以避免运行需要它们的其余代码。

# 结论

我们可以使用`while`循环来重复运行代码，并检查每次迭代中的条件，这样我们就可以结束它。

还有一个`do...while`循环，总是在检查我们的条件之前运行第一次迭代。

如果我们使用`switch`语句，我们可能希望将 case 语句按顺序排列，并且总是将`break`放在块的末尾。

或者，我们也可以把正常情况放在前面，把错误情况放在后面。