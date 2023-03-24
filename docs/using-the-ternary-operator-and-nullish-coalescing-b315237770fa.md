# 使用三元运算符和零化合并

> 原文：<https://levelup.gitconnected.com/using-the-ternary-operator-and-nullish-coalescing-b315237770fa>

![](img/e97c6ded5cdc1a0256d2f9799c4b4522.png)

我们每天都在代码中使用条件语句——它是所有编程语言的基石。然而，如果一个值被立即赋值或返回，那么写出整个 **IF ELSE** 语句可能会有点麻烦。有没有更简单的方法让我们写出这些条件句？让我们来看看[三进制算子](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)！

三元运算符是写出条件的简便方法，主要用于“一行程序”。让我们看看这如何改善我们的生活，并使我们的代码更加简洁。

```
const person = {
  name: "Becky",
  age: 23,
  isStudent: null
}// Cumbersome conditional for immediately assigning a value
if (person.age >= 18) {
  person.isStudent = 'yes'
} else {
  person.isStudent = 'no'
}// The ternary operator is a lot more concise and returns the value
person.isStudent = person.age >= 18 ? 'yes' : 'no'
```

上面是一个非常简单的例子，说明了三元运算符如何将 5 行的常规 **IF ELSE** 语句的开销最小化到一行代码中。

三元运算符的工作方式相当优雅。首先，我们有一个条件，后面跟一个`?`，然后我们有第一个表达式，如果条件评估为真，则返回第一个表达式，然后是一个`:`，如果评估为假，则返回第二个表达式。

这里需要注意的一点是，IF 是一个语句，而三元运算符是一个表达式，它返回一个值，这对于压缩代码非常有用。

```
conditional ? truthyExpression : falsyExpression
```

值得注意的是，在计算条件时，如果值还不是布尔值，JavaScript 将使用类型转换将它强制转换为布尔值。这意味着一些值将被评估为*真值*或*假值*。

*注意:JS 中的以下值求值为 falsy → false，null，0，0n，“”，“”，`，undefined，NaN。* [*在这里阅读更多关于 falsy 值的内容*](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) *。*

这实质上意味着，与典型的条件一样，您可以使用三元运算符来确定某个值是否存在。

```
person.name ? 'Name exists' : 'Unknown person'
```

# TypeScript 带来了什么？

然而，在 TypeScript 中，我们有一个额外的功能，其功能与三元运算符非常相似，称为 [nullish 合并](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#nullish-coalescing)，它是在 TypeScript 的 3.7 版本中引入的。

```
conditional ?? fallbackExpression
```

那么，nullish 同余与普通的三元运算符有什么不同呢？最根本的区别是，它会检查一个值是否存在。如果该值存在，即不是`null`或`undefined`，则将返回该值。如果该值不存在，将返回“fallback”。让我们仔细看看这个。

```
const person = {
  name: 'Becky',
  gender: null
}// Value is present
const name = person.name ?? 'Unknown person'
console.log(name)
> Becky// Value is not present
const gender = person.gender ?? 'Unknown gender'
console.log(gender)
> Unknown gender
```

在上面的例子中，当`name`属性存在时，它将被返回，在本例中，它被注销到控制台。然而，由于`gender`不存在，因此返回“回退”并记录到控制台。这真是太棒了！

[这个你可以自己实验！](http://www.typescriptlang.org/play/#code/MYewdgzgLgBADgUwE4XDAvDA3gKBvmMAQwFsEAuGAcgCEFgBrATyoBo8CBzBMAE2UpgArgBsROAL44cAehkwAakRFCE8JAgg8oOUJBAiEAOhEhOACkQpwR4mRgB+B9QCqYBmBAB3MIVIIqAEppOUVlVUIQWDgNLTAdPVRDEzNLZFQwI24+ZEdnKjcPb19s-iQgnCA)

![](img/2e64ff03b946be1cf6a8d32f84288ecc.png)

使用三元运算符时，我们可以像在条件运算符中一样调用函数。

```
const name = person.name ? foo() : bar()
```

在上面的例子中，如果`name`属性存在于`person`对象上并且评估为真，那么`foo()`函数将被调用，其结果将被存储在`name`变量中。同样，如果`name`属性的计算结果为 falsy，那么`bar()`函数将被调用并返回。

nullish 合并也会发生这种情况，如果正在计算的属性没有定义，我们可以使用函数调用作为“后备”。

```
const name = person.name ?? foo()
```

这非常有用，肯定会使代码更容易阅读和维护。

现在我们可以考虑一个稍微复杂一点的例子。考虑下面的场景，一个有两个需求需要满足的条件。我们如何用三进制写这个？

```
const foo = person.isStudent && person.age <= 24 ? 'yes' : 'no'
```

上面将检查这个人是 24 岁或更小的学生。如果我们愿意的话，我们还可以潜在地加入一些战术括号，使事情更容易区分。

```
const foo = (person.isStudent) && (person.age <= 24) ? 'yes' : 'no'
```

这两个例子之间没有功能上的变化。唯一的变化是语法上的，为了增强未来开发人员的可读性，他们可能会在维护代码库的同时遇到这个代码片段。

可以进行的进一步优化是将条件提取到它自己的变量中。这样可以使检查的目的更明确，例子更简洁。

```
// Define the condition
const isJuniorStudent = person.isStudent && person.age <= 24// Evaluate the condition
const foo = isJuniorStudent ? 'yes' : 'no'
```

对于下一个需要维护该功能的开发人员来说，这可能更容易理解。

如果我们有一些嵌套的条件句呢？这会改变写出三元运算符的方式吗？有点，但它的核心基本保持不变。

```
const foo = person.isStudent ? (person.isFullTime ? 'yes' : 'no') : 'maybe'
```

在基本示例中，我们看到，如果这个人是学生并且在全日制学习，那么表达式的计算结果为`yes`，而如果这个人是学生但是没有全日制学习，那么`no`就是返回的响应。如果不知道这个人是不是学生，那么返回`maybe`。

这相当于一个常规条件，如下所示。

```
let fooif (person.isStudent) {
  if (person.isFullTime) {
    foo = 'yes'
  } else {
    foo = 'no'
  }
} else {
  foo = 'maybe'
}
```

然而，在某种程度上，速记函数可能变得比传统方法更加繁琐和复杂，难以维护和阅读。在编写一些更复杂的简写条件时，应该仔细考虑可维护性和可读性。

就我个人而言，我会将速记条件分解成命名变量，然后构建一个结果。这将使用三元运算符，但也有助于保持代码的可读性和可维护性。

```
// Determines if full time student or not
const isFullTime = person.isFullTime ? 'yes' : 'no'const foo = person.isStudent ? isFullTime : 'maybe'
```

![](img/22862130af058f47a3a47472254d88b4.png)

# 最后，有什么有用的工具吗？

我很高兴我们问了这个问题，因为碰巧的是，确实有。首先，有一些令人惊奇的资源，它们可能涵盖了你仍然可能有的关于三元运算符或零化合并函数的任何问题。您可以在下面找到这些资源。

[打字稿游乐场](http://www.typescriptlang.org/play/)
[打字稿— v3.7](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#nullish-coalescing)

[js dild](https://jsfiddle.net/)
[MDN—三元运算符](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)

感谢你阅读这篇文章，我希望你学到了一些新的东西。如果你有任何意见，请随意写在下面的评论区！

再见了。