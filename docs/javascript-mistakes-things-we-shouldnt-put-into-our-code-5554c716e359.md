# JavaScript 错误——我们不应该在代码中出现的错误

> 原文：<https://levelup.gitconnected.com/javascript-mistakes-things-we-shouldnt-put-into-our-code-5554c716e359>

![](img/bd7aaf4638203acb79b6b0eafe0c4d1f.png)

照片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在这篇文章中，我们将看看我们不应该放在代码中的东西。

# 我们不应该在条件语句中使用赋值操作符

我们不应该在条件赋值中使用赋值操作符。例如，以下可能是一个错误:

```
if (title = "manager") {

}
```

上面的代码将`title`设置为`'manager'`，而不是检查`title`是否有值`'manager'`。这可能是一个错误，因为大多数人想用`===`操作符检查值，而不是在`if`语句中进行赋值操作。

我们真正想要的是:

```
if (title === "manager") {

}
```

# 我们不应该把控制台留在生产代码中

`console.log`和其他`console`方法对于调试我们的代码非常有用。然而，在我们将它们投入生产之前，我们应该将它们删除，这样我们就不会无意中向用户暴露任何内容。

因此，以下代码不应出现在生产代码中:

```
console.log("hello.");
console.warn("warning.");
console.error("error.");
```

其他的`console`方法调用也不应该被提交。唯一的例外是我们在 Node.js 应用程序中使用`console`向用户输出数据。

那么我们可能想忽略这个规则。

# 我们不应该把常量表达式放在条件中

常量表达式很可能是一个错误，除非我们是故意这样做的。例如:

```
if (false) {
    foo();
}
```

上面的代码永远不会运行，因为里面的条件总是`false`。

同样，当在循环中使用常量条件时，我们可能会得到无限循环或从不运行的循环。例如，下面是一个无限循环:

```
while (true) {
    foo();
}
```

在大多数情况下，我们可能不希望这样。如果三进制表达式中的条件是常数，那么它就没什么用，因为它总是返回一个值。

例如:

```
let result = true ? 'foo' : 'bar';
```

那么`result`总是`'foo'`，因为条件是`true`，所以总是返回第一个值。

更有意义的代码示例包括:

```
while (true) {
  foo();
  if (conditionFalse()){
    break;
  }
}
```

# 在 JavaScript 正则表达式中放置控制字符

控制字符在 ASCII 到 31 的范围内。他们是特殊的隐形人物。JavaScript 字符串中很少使用这些字符，所以这可能是一个错误。

例如，下面的内容可能不是我们想要的:

```
const pattern1 = /\x00/;
const pattern2 = new RegExp("\x00");
```

我们很少想要匹配空字符，也就是 ASCII 中的字符 0。

# 我们不应该将调试器语句放在生产代码中

顾名思义，`debugger`用于调试。它会暂停该行中的程序，以便我们可以检查代码中当前点的项目。

此外，使用`debugger`调试代码还有更好的方法。生产代码中绝对不应该有`debugger`，因为它会暂停用户正在使用的程序，如果它存在的话。

例如，下面是一个错误:

```
const isTrue = (x) => {
  debugger;
  return x === true;
}
```

在生产代码中，我们应该写:

```
const isTrue = (x) => {
  return x === true;
}
```

![](img/f801db7925d27bb1a7104663b4246d85.png)

由[塞巴斯蒂安·赫尔曼](https://unsplash.com/@officestock?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 我们不应该在函数中有重复的参数

具有相同的多个参数意味着最后出现的参数采用具有相同名称的最后一个参数的值。

例如，如果我们有:

```
const foo = (a, b, a) => {
  console.log(a);
}
```

那么当我们如下调用`foo`时:

```
foo(1, 2, 3);
```

我们看到记录的值为 3。这会引起混乱，很可能是个错误。两个参数同名没有太大意义。

相反，我们应该重命名第三个参数:

```
const foo = (a, b, c) => {
  console.log(a);
}
```

或者去掉第二个`a`:

```
const foo = (a, b) => {
  console.log(a);
}
```

# 结论

由于其宽容的本质，在编写 JavaScript 代码时可能会犯许多种错误。我们永远不应该留下类似`console`或`debugger`的调试代码。唯一的例外是当我们在节点应用程序中使用`console`向用户输出值时。

条件中的常量表达式最容易出错。在条件语句中，我们编写总是运行或从不运行的代码。

如果它们在循环中，那么它们要么创建一个无限循环，要么创建一个永不运行的循环。

在三元表达式中，我们创建一个总是赋值相同的表达式。因此，它很可能不是很有用，是一个错误。

最后，函数中不应该有重复的参数名。这会造成混乱，因为传入的最后一个值会覆盖赋给相同参数名的第一个值。