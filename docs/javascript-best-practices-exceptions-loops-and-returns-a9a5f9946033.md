# JavaScript 最佳实践—异常、循环和返回

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-exceptions-loops-and-returns-a9a5f9946033>

![](img/c8aa81476fdcd41cb93d4dc74cf42dc1.png)

由 [Kouji 鹤](https://unsplash.com/@pafuxu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，JavaScript 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

在本文中，我们将研究一些在编写 JavaScript 代码时应该遵循的最佳实践。

# 正则表达式中没有多个空格

正则表达式中不应该有多个空格。

例如，我们不应该写:

```
const regex = /foo  bar/;
```

相反，我们写道:

```
const regexp = /foo bar/;
```

# return 语句中的赋值必须用括号括起来

我们应该用括号将`return`语句括起来，这样我们就可以确定赋值是在返回值之前完成的。

例如，不写:

```
function add (a, b) {
  return result = a + b;
}
```

我们写道:

```
function add (a, b) {
  return (result = a + b);
}
```

# 不要给变量本身赋值

给变量赋值是一个无用的语句，所以我们不应该写它。

例如，我们不应该这样写:

```
num = num;
```

# 不要拿一个变量和它本身做比较

既然一个变量和它自己比永远是`true`，那么让一个变量和它自己比是没有用的。

例如，我们不应该写:

```
if (score === score) {}
```

# 不要使用逗号运算符

逗号操作符总是返回序列中的最后一个表达式，所以它是非常无用的。

因此，我们不应该有这样的代码:

```
const foo = (doSomething(), !!test);
```

# 不要将事物分配给受限的名称

我们不应该用受限的名字创建变量并给它们赋值。

它不起作用，只会引起混乱。

例如，我们不应该有这样的代码:

```
let undefined = 'value';
```

# 没有稀疏数组

我们可以拥有在偶数槽中没有条目的数组。

要做到这一点，我们只需写逗号，中间不加任何东西来创建稀疏数组。

然而，这很容易被误认为是一个错别字，或者说这是一个错别字。

因此，我们不应该有那样的表达。

例如，我们不应该这样写:

```
let fruits = ['banana',, 'grape'];
```

# 没有标签

制表符会导致不同文本编辑器出现问题。

标签之间的间距不一致，

因此，我们不应该使用它们。

相反，我们使用 2 个空格或将制表符自动转换为 2 个空格进行缩进。

# 常规字符串应该包含模板文字占位符

模板字符串和常规字符串很容易被混淆，因为它们使用相似的字符作为分隔符。

我们不能在常规字符串中使用模板文字占位符。

因此，我们不应该有这样的代码:

```
const greeting = 'hi  ${name}';
```

相反，我们写道:

```
const greeting = `hi  ${name}`;
```

模板字符串用反斜杠而不是单引号分隔。

# 在此之前必须调用 super

在类构造函数中，我们必须在`this`之前调用`super`。

否则，我们会得到一个错误。

因此，与其写:

```
class Dog extends Animal {
  constructor () {
    this.breed = 'chow chow';
    super();
  }
}
```

我们写道:

```
class Dog extends Animal {
  constructor () {
    super();
    this.breed = 'chow chow';
  }
}
```

# 总是抛出错误实例

一个`Error`实例包含有用的信息，比如堆栈跟踪和错误所在的行号。

因此，我们应该总是抛出`Error`实例，而不是其他值。

所以我们不应该写:

```
throw 'error';
```

但是我们应该写:

```
throw new Error('error');
```

# 空白不应该出现在行尾

空白不应该出现在行尾。

他们做的不多。所以我们应该移除它们。

# 不要初始化为未定义

我们不应该将变量初始化为`undefined`。

当我们在没有值的情况下声明它们时，变量已经是`undefined`了。

所以我们不需要显式地将它设置为`undefined`。

例如，不写:

```
let name = undefined;
```

我们只是写:

```
let name;
```

# 没有未修改的循环条件

如果我们忘记更新条件，那么我们可能会得到一个无限循环。

因此，我们应该记住在每次迭代中修改条件。

例如，不写:

```
for (let i = 0; i < arr.length; j++) {...}
```

我们写道:

```
for (let i = 0; i < arr.length; i++) {...}
```

![](img/23cc9597b4a24dab8b7134a4f9e2fd4b.png)

照片由[迈克·本纳](https://unsplash.com/@mbenna?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们不应该有条件不更新的循环。

此外，我们不应该在代码中有无用的空格。

抛出`Error`实例比抛出其他值更好，因为它提供了更多的信息。