# 更多技巧将增强您的 JavaScript 技能

> 原文：<https://levelup.gitconnected.com/more-tricks-that-will-supercharge-your-javascript-skills-8c71e0f7da31>

![](img/6fa855850efb57577f73eb14ecd81b91.png)

路易斯·维拉斯米尔在 Unsplash[拍摄的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 你可以马上开始应用的小技巧

JavaScript 是目前非常流行的编程语言。就像任何其他编程语言一样，JavaScript 提供了许多不同的方法来解决代码中的某些问题。有数不清的小技巧的例子，你可以从今天开始使用，这将帮助你更有效地解决这些问题。

我们将在本文中介绍的技巧可以用于大多数开发人员日常需要完成的任务。不管你是一名多么有经验的开发人员，扩展你的技能总是好的。这个列表中最棒的部分是你可以马上开始应用它们。

让我们直接进入技巧列表，这些技巧将大大提高您的 JavaScript 技能。

# 1.串联数组

如果你有一些 JavaScript 的经验，你可能听说过 spread 操作符。您可能已经知道 spread 操作符有多强大。

使用 spread 操作符可以做的事情之一是将多个数组连接在一起。

这是它在代码中的样子:

```
let start = [1, 2]
let end = [5, 6, 7]
let numbers = [...start, 3, 4, ...end] // [1, 2, 3, 4, 5, 6, 7]
```

请注意，您不仅可以在数组的开头或结尾展开。在构建新阵列时，您完全可以灵活应对。在下面的例子中，我在新数组的中间使用了 spread 运算符。

```
const start = [1, 2]
const end = [5, 6, 7]
const numbers = [9, ...start, ...end, 8] // [9, 1, 2, 5, 6, 7, 8]
```

# 2.独特的价值观

有时候你需要从数组中删除重复的值。一种方法是循环遍历这些值，并跟踪已经遍历过的项。这听起来已经很复杂了，对于如此简单的事情来说，代码似乎太多了。

你完全正确。有一个简单的技巧可以用来从数组中获取唯一值。

```
const numbers = [1, 2, 3, 4, 5, 6, 2, 7, 4]
const uniqueNumbers = Array.from(new Set(numbers))console.log(uniqueNumbers) // [1, 2, 3, 4, 5, 6, 7]
```

这个技巧同样适用于包含字符串值的数组。甚至是混合的价值观。

```
const items = [1, "John", 3, 4, 5, "Anna", 2, 7, 4, "Anna"]
const uniqueItems = Array.from(new Set(items))console.log(uniqueItems) // [1, "John", 3, 4, 5, "Anna", 2, 7]
```

这样做的原因是 Set-object 中的值只能出现一次。这意味着一个集合只适用于唯一的值。我们用这个小技巧所做的就是将我们的初始数组转换成一个集合，然后将它转换回一个数组。这样，重复的值将被过滤掉。

如果你不喜欢`Array.from`功能，你也可以使用扩展操作符。这是同一段代码的样子，但这次使用了扩展运算符:

```
const uniqueItems = [… new Set(items)]
```

如果您对 JavaScript 或 spread 操作符没有太多经验，那么第一个例子更有意义，因为它更冗长。更有经验的开发人员可能会选择最后一种方法，因为它非常简洁。

# 3.计数值

假设我们有一个包含订单的数组，如下所示:

```
const orders = [
  {
    number: 78,
    vat: 12,
    total: 101,
  },
  {
    number: 83,
    vat: 5,
    total: 40,
  },
  {
    number: 84,
    vat: 18,
    total: 67,
  }
]
```

我们想知道所有订单的总数是多少，这样我们就知道我们有多少收入。一种方法是使用 for 循环。

```
let total = 0for(let i = 0; i < orders.length; i++) {
    total += orders[i].total
}
```

虽然这很有效，但是 for 循环的问题是它们很难看。有一种更方便的方法，使用更少的代码行。是时候认识一下*减少*的方法了。

*reduce* 方法将一个数组缩减为一个值。第一个参数是回调，它有两个参数:累加器和当前值。

要获得所有订单的总价值，我们可以使用 *reduce* 方法。我们可以通过将每个订单的总和添加到累加器中来实现这一点。

```
orders.reduce((acc, cur) => acc + cur.total, 0) // 208
```

最后一个参数是累加器的初始值，在本例中为 0。如果不指定初始值，数组中的第一个元素将被用作初始值。

# 4.模板文字

本文中我们要讲的最后一个技巧是关于模板文字的。模板文字允许您以优雅的方式连接字符串。这样你就再也不用用加号来连接字符串了。

您可能已经使用加号连接了字符串，如下所示:

```
const first = "John"
const last = "Doe"const full = first + " " + last
```

这不是连接这些字符串的最佳方式。幸运的是，还有一个选择。

```
const full = `${first} ${last}`
```

请注意，这要求您用反斜线```将字符串括起来，而不是用传统方式连接字符串时使用的双引号或单引号。

使用模板文字还可以做更多的事情。一旦用反勾号打开了模板文字，您只需按 enter 键创建一个新行。不需要特殊字符，字符串将按原样呈现:

```
const output = `Hey
these templateliterals are awesome!`
```