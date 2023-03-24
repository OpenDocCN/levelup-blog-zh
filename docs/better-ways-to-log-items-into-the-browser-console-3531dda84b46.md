# 在浏览器控制台中记录数据的更好方法

> 原文：<https://levelup.gitconnected.com/better-ways-to-log-items-into-the-browser-console-3531dda84b46>

![](img/4032cbc0d8acd913458c28766653079e.png)

[钻机](https://unsplash.com/@rigels?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

很多人都知道`console.log`是一种非常有用的查看变量和常量值的方法。然而，在`console`对象中有很多其他的方法我们已经错过了。

在本文中，我们将通过其他的`console`方法来查看将项目记录到控制台的其他方法，以使记录和调试更加容易。

# console.table()

`console.table`方法让我们将数组和对象记录到开发人员控制台的一个表中。

对象的每个数组条目或属性和值都将作为表中的一行记录到控制台中。

例如，我们可以将它与如下数组一起使用:

```
const array = [1, 2, 3];
console.table(array);
```

然后我们得到一个表格，左边是索引，右边是条目。

同样，我们可以如下记录一个对象:

```
const obj = {
  a: 1,
  b: 2,
  c: 3
};
console.table(obj);
```

左边是属性，右边是值。

它适用于嵌套一层的对象。如果我们有像这样嵌套的对象:

```
const obj = {
  a: 1,
  b: 2,
  c: {
    d: 4
  }
};
console.table(obj);
```

然后嵌套属性列在右侧。

# console.count()

`console.count()`方法记录一个方法被调用的次数。

我们可以提供一个字符串标识符作为标识符来开始和结束计数。

如果我们不传入自己的标识符，那么默认标识符就是' default '。

例如，我们可以如下使用它:

```
const foo = () => {};
for (let i = 0; i <= 5; i++) {
  console.count('foo');
  foo();
}
```

在上面的代码中，我们有一个`foo`函数，在循环中被调用了 6 次，因此我们得到:

```
foo: 1
foo: 2
foo: 3
foo: 4
foo: 5
foo: 6
```

从控制台日志输出。

# console.assert()

`console.assert()`方法仅在断言为假时写入控制台。它需要两个参数。第一个是带有要检查的条件的布尔表达式，第二个是当第一个参数中的条件返回`false`时显示的错误消息。

例如，我们可以如下使用它:

```
const add = (a, b) => {
  console.assert(a + b === 5, 'not 5');
};add(3, 2);
add(5, 5);
add(10, 0);
```

在上面的代码中，我们的`add`函数调用`console.assert`并检查两个参数相加等于 5，如果我们传递给第一个参数的条件返回`false`，我们记录‘not 5’。

因此，我们应该看到:

```
Assertion failed: not 5
Assertion failed: not 5
```

因为对`add`的最后两次调用的参数加起来不到 5。

# `Styling console.log`

我们可以通过用逗号分隔字符串来给我们的`console.log`添加标签和突出显示。

例如，我们可以写:

```
console.log('number', 5);
```

然后我们看到:

```
number 5
```

在控制台里。然后我们看到它们都是按照传入的顺序显示的。我们可以继续添加参数，它们都会显示出来。

![](img/edd2b2692944b7c1dd0ebdd1c5c5169d.png)

[法比安·米勒](https://unsplash.com/@fabiennedia?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 使用 CSS 设置样式

我们可以在字符串中使用`%c`标签，按照我们希望的方式设计`console.log`输出。

例如，我们可以编写以下代码:

```
console.log("%cfoo", "font-family:arial;color:blue;font-weight:bold;font-size:15px");
```

在上面的例子中，我们在文本`'foo'`前添加了`%c`标签作为第一个参数。然后我们有:

```
"font-family:arial;color:blue;font-weight:bold;font-size:15px"
```

这是一个字符串，用于将文本样式化为 Arial 字体，颜色为蓝色，字体粗细为粗体，字体大小为 15px。

然后，我们将看到所有这些样式都应用于控制台日志输出。

# 结论

正如我们从上面的方法用法中可以看到的,`console`对象有很多我们可能没想过要使用的有用方法。

`console.table`方法在一个表中列出数组条目和对象的属性和值。

`console.log`输出可以用 CSS 样式化，在我们想要记录的文本前加上`%c`字符串标签。

`console.assert`让我们检查条件是否为`true`，如果不是，我们在控制台中记录一条消息。

`console.count`统计函数被调用的次数。