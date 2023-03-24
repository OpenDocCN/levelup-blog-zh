# JavaScript 最佳实践—复合语句

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-compound-statements-fb0326f6ea70>

![](img/f551538aa96227cb3f67c121ff6b9046.png)

图片由 [Aral Tasher](https://unsplash.com/@araltasher?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将研究用 JavaScript 编写复合语句的最佳实践。

# 将一对括号写在一起

当我们写积木的时候，很容易忘记大括号。

因此，我们可能想先写大括号，然后再填充语句。

例如，我们首先写:

```
if (i < maxLines)
```

然后我们写道:

```
if (i < maxLines){}
```

然后我们填写里面的语句:

```
if (i < maxLines){
  // do something
}
```

# 用括号来阐明条件句

我们应该在条件块中添加大括号来阐明它们。

即使 JavaScript 允许我们编写不带括号的单行条件语句，我们也可以加上括号:

```
if (denominator !== 0) {
  result = a / denominator;
}
```

# 危险的深层筑巢

嵌套是不好的，因为它们很难阅读。并且修改难以阅读的代码增加了产生错误的机会。

因此，我们应该尽可能地减少或消除嵌套。

如果我们有:

```
if (foo) {
  //...
  if (bar) {
    //...
    if (qux) {
      //...
      if (baz) {
        //...
      }
    }
  }
}
```

那么我们应该重写它以消除嵌套。

# 使用中断块简化嵌套的 if

如果我们在循环中嵌套了，我们可以用`break`语句来消除它们。

例如，我们可以编写以下内容:

```
while (condition) {
  if (shouldEnd) {
    break;
  }
  //...
}
```

当`shouldEnd`为`true`时，我们结束循环。

我们可以为任何应该结束循环的条件添加中断块。

# 将嵌套的 if 转换为一组 if-else 块

如果我们有嵌套的`if`语句，我们可以通过变成`if...else`块来消除它们。

例如，如果我们有:

```
if (10 < subtotal) {
  if (100 < subtotal) {
    if (1000 < subtotal) {
      total *= 0.9;
    } else {
      total *= 0.8;
    }
  } else {
    total *= 0.7;
  }
}
```

然后，我们应该通过编写以下代码来移除嵌套，从而以一种更简洁的方式重新构造它:

```
if (10 < subtotal) {
  total *= 0.7;
} else if (100 < subtotal) {
  total *= 0.8;
} else if (1000 < subtotal) {
  total *= 0.9;
}
```

没有巢状结构会更容易阅读。

# 将嵌套的 if 转换为 case 语句

我们也可以将嵌套的`if`语句转换成`switch`语句。

例如，我们可以写:

```
switch (val) {
  case 1: {
    //...
    break;
  }
  case 2: {
    //...
    break;
  }
  //...
}
```

没有嵌套，所以很容易阅读，我们可以看到所有的条件。

![](img/caf0cdc05aee8839bbec64d2edc0c888.png)

由 [Sergio Pedemonte](https://unsplash.com/@yourhousefitness?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 将深度嵌套的代码分解到它自己的函数中

将深度嵌套的代码移到它自己的函数中会减少嵌套，所以我们应该这样做。

它还使每个部分更短，这样就更好了。

# 使用更加面向对象的方法

我们可以使用类和对象将它们封装到一个包中并使用它们，而不是写出具有大量相似代码和嵌套的冗长过程。

这样，我们可以创建做类似事情的类似对象，而不是一行一行地写出来。

# 重新设计深度嵌套的代码

深度嵌套的代码应该重新设计以减少嵌套。

太复杂了，读起来伤脑子。更改它们将容易出错。

# 降低复杂性

降低复杂性会让我们的生活更轻松。

没有人喜欢被复杂的事情淹没。

我们可以通过计算直线路径的数量来衡量一段代码的复杂度。

然后我们加上`if`、`while`、`for`、`&&`、`||`的数字。

然后我们将总数加上`case`语句或块的数量。

总数越高，代码就越复杂。

# 结论

降低复杂性会让我们的生活更轻松。

我们可以通过计算一组代码的分支和直线代码来做到这一点。

此外，嵌套对我们的大脑来说总是很难，所以我们可以将它们转移到函数或类中。

我们可以先写大括号，这样在定义函数的时候就不会忘记了。