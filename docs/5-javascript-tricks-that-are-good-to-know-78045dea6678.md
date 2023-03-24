# 5 个值得了解的 JavaScript 技巧

> 原文：<https://levelup.gitconnected.com/5-javascript-tricks-that-are-good-to-know-78045dea6678>

![](img/ed917affaae98f251b06294128164111.png)

Artem Sapegin 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 用下面的技巧列表增强你的 JavaScript 技能

JavaScript 是目前最流行的编程语言之一。就像任何其他编程语言一样，它有许多你今天就可以开始使用的巧妙的小技巧。

这些技巧中的每一个都完成了大多数开发人员每天需要完成的任务。根据你的经验，你可能已经知道其中的一些技巧，而其他的会让你大吃一惊。

在本文中，我们将回顾一系列技巧，这些技巧将使你成为更好的开发人员，并增强你的 JavaScript 技能。

# 1.每个和一些

*every* 和 *some* 函数是并非所有开发者都熟悉的函数。然而，它们在某些情况下非常有用。让我们从每个函数的*开始。如果想知道数组的所有元素是否都通过了某个测试，可以使用这个函数。本质上，这是在遍历数组的每个元素，并检查它们是否都为真。*

这对你来说可能听起来有点抽象，所以让我们看看下面的例子。这并不像听起来那么复杂。

```
const random_numbers = [ 13, 2, 37, 18, 5 ]
const more_random_numbers = [ 0, -1, 30, 22 ]const isPositive = (number) => {
  return number > 0
}random_numbers.every(isPositive); // returns true
more_random_numbers.every(isPositive); // returns false
```

每个函数都会返回一个布尔值。如果数组中的所有元素都通过测试，将返回 *true* 。如果数组中的一个元素测试失败，将返回 *false* 。

如果您愿意，您也可以使用匿名函数作为测试函数:

```
random_numbers.every((number) => {
    return number > 0
})
```

*的某些*功能几乎与*的所有*功能完全相同。只有一个主要的区别: *some* 函数测试数组中是否至少有一个元素通过了测试。

如果我们看一下前面的例子，使用 *some* 函数而不是 *every* 函数，两个数组都将返回 *true* ，因为两个数组都包含一个正数。

```
const random_numbers = [ 13, 2, 37, 18, 5 ]
const more_random_numbers = [ 0, -1, 30, 22 ]const isPositive = (number) => {
  return number > 0
}random_numbers.some(isPositive); // returns true
more_random_numbers.some(isPositive); // returns true
```

# 2.有条件地设置变量

有条件地设置变量既简单又让你的代码看起来更优雅。应用这个技巧时不需要写 if 语句——这是我个人最喜欢的 JavaScript 技巧之一。

那么如何有条件地设置一个变量呢？

```
const timezone = user.preferred_timezone || 'America/New_York'
```

在上面的例子中，我们检查用户是否有首选时区。如果用户有首选时区，我们就使用那个时区。如果用户没有首选时区，我们使用默认时区，即“美国/纽约”。

这段代码看起来比使用 if 语句时干净多了。

```
let timezone = 'America/New_York'if (user.preferred_timezone) {
    timezone = user.preferred_timezone
}
```

看起来干净多了，不是吗？

# 3.在数组中强制转换值

有时你想把所有的值放在一个数组里。例如，当您使用三重等于运算符来检查数组中是否存在某个数字时，就会出现这种情况。

我最近遇到了一个问题，我有一个多选。select 选项的 HTML 值是字符串而不是整数，这是有意义的。选定值的数组如下所示:

```
let selected_values = [ '1', '5', '8' ]
```

问题是我在检查某个整数是否存在于所选值的数组中。没有成功。我使用了一个使用三重等于运算符的交集函数。因为我必须找到一个解决方案。

在我看来，最好的解决方案是将数组中的所有值转换成一个整数。在尝试这样做的时候，我偶然发现了一个简单却优雅的解决方案。

```
selected_values = selected_values.map(Number) // [ 1, 5, 8 ]
```

除了将所有值转换为整数，还可以通过简单地更改 map 函数的参数将数组中的所有值转换为布尔值。

```
selected_values = selected_values.map(Boolean)
```

# 4.对象析构

一旦你知道了对象析构，你可能每天都会用到它。

但是到底什么是解构？

析构是一个 JavaScript 表达式，允许你从数组、对象、映射和集合中提取数据到它们自己的变量中。它允许您从一个对象或数组中提取属性，一次提取多个。

让我们看看下面的例子，我们有一个用户对象。如果你想把用户名存储在一个变量中，你必须在新的一行把它赋给一个变量。如果你想在一个变量中存储性别，你必须再次做同样的事情。

```
const user = {
    name: 'Frank',
    age: 23,
    gender: 'M',
    member: false
}const name = user.name
const gender = user.gender
```

通过析构，您可以使用以下语法直接获取对象属性的变量:

```
const { name, age, gender, member } = user;console.log(name)   // Frank
console.log(age)    // [2](https://medium.com/@kunaltandon.kt)3
console.log(gender) // M
console.log(member) // false
```

# 5.更好的调试使用性能

作为开发人员，你经常要做的事情之一就是调试。然而，调试不仅仅是使用 *console.log* 在控制台中打印出一堆日志消息。

您知道控制台对象有一个很好的方法来分析代码片段的性能吗？然而，大多数开发人员只知道调试代码的标准 *console.log* 方式。

控制台对象有更多有用的功能。它有一个*时间*和*时间结束*功能，可以帮助您分析性能。它的工作非常简单。

在您想要测试的代码前面，您调用 *console.time* 函数。这个函数有一个参数，它接受一个描述您想要分析的内容的字符串。在想要测试的代码的末尾，调用 *console.timeEnd* 函数。您给这个函数的字符串与第一个参数相同。然后，您将看到在控制台中运行代码所花费的时间。

```
console.time('loop') for (let i = 0; i < 10000; i++) {   
    // Do stuff here 
} console.timeEnd('loop')
```