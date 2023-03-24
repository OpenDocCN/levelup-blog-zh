# JavaScript 重构技巧——降低函数复杂性

> 原文：<https://levelup.gitconnected.com/javascript-refactoring-tips-reducing-function-complexity-22f4ad3f86cc>

![](img/8aad70b4135b63d6d6b3c13bc6ea11d3.png)

照片由[加博尔·szűts](https://unsplash.com/@szutsi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种简单易学的编程语言。编写运行的程序很容易。然而，很难写出一段干净的 JavaScript 代码。

在本文中，我们将研究降低 JavaScript 函数复杂性的方法。

# 将重复的代码移动到一个位置

我们应该将重复的代码移动到一个位置，这样我们就可以在需要时轻松地更改它们，因为我们只需要更改一段代码，而不是多段代码。

例如，不写:

```
const button = document.querySelector('button');
let toggled = true;
button.addEventListener('click', () => {
  toggled = !toggled;
  if (toggled) {
    document.querySelector("p").style.color = 'red';
  } else {
    document.querySelector("p").style.color = 'green';
  }
})
```

如果我们有 2 个`document.querySelector(“p”)`实例，我们应该写:

```
const button = document.querySelector('button');
const p = document.querySelector("p");
let toggled = true;
button.addEventListener('click', () => {
  toggled = !toggled;
  if (toggled) {
    p.style.color = 'red';
  } else {
    p.style.color = 'green';
  }
})
```

正如我们所看到的，它更短，如果我们决定不再选择 p 元素，我们只需要改变选择器一次。

代码的另一个常见实例是幻数，我们用数字编写代码，但我们不知道代码本身的含义。

例如，我们有这样的代码:

```
let x = 1;
let y = 1;
let z = 1;
```

我们有 3 1 个不知道是什么意思的数字。我们可以删除重复内容，并通过编写以下内容使这些数字自文档化:

```
const numPeople = 1;
let x = numPeople;
let y = numPeople;
let z = numPeople;
```

那么我们知道 1 是`numPeople`的值。现在我们知道 1 代表人数。

# 简化功能

功能应该尽可能简单。因此，他们应该只做一件事，不要有太多行代码。他们不应该有超过 30 行的代码。

我们使用 JavaScript 函数的旧情况不应该被使用。例如，我们不应该对模块或块使用 IIFEs。同样，我们不应该再定义构造函数了。

相反，我们应该使用类语法，其中我们可以在类中包含该类的多个实例方法。

这大大减少了对长函数的需求。

此外，函数应该是纯的，只要我们可以定义它们。这意味着他们不应该产生副作用。

例如，最好的简单函数是这样的函数:

```
const add = (a, b) => a + b;
```

上面的函数没有任何副作用，因为它不修改函数外部的任何变量。此外，它是纯的，因为它总是为相同的输入返回相同的输出。

它也只做一件事，那就是增加数字。因此，代码读者的认知负荷不会太重，因为它只做一件事，别的什么都不做。

![](img/72ee8701619dc774ef11a1908c9e1297.png)

照片由[鱼水族馆](https://unsplash.com/@cofishco?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 使用 Guard 子句而不是嵌套的 If 语句

当函数遇到某种情况时，我们在保护子句中结束函数。

它取代了又长又难读的嵌套的`if`块。

例如，不用编写下面的函数:

```
const greet = (firstName, lastName, greeting) => {
  if (typeof firstName === 'string') {
    if (typeof lastName === 'string') {
      if (typeof greeting === 'string') {
        return `${greeting}, ${firstName}${lastName}`;
      }
    }
  }
}
```

我们写道:

```
const greet = (firstName, lastName, greeting) => {
  if (typeof firstName !== 'string') {
    throw new Error('first name is required');
  }
  if (typeof lastName !== 'string') {
    throw new Error('last name is required');
  }
  if (typeof greeting !== 'string') {
    throw new Error('greeting is required');
  }
  return `${greeting}, ${firstName}${lastName}`;
}
```

在第二个例子中，如果每个参数不是字符串，我们通过抛出错误来消除嵌套的`if`语句。

在做同样的事情时，这将嵌套的`if`语句减少到没有嵌套的`if`语句。

嵌套很难阅读和理解，我们应该在任何地方消除它们。

# 结论

重复的代码总是不好的。我们应该永远记住不重复自己(干)的原则。

此外，许多以前使用传统功能的东西不应该再使用了。许多函数可以用模块、块、类和箭头函数来代替。

最后，嵌套的`if`语句应该用 guard 子句替换，因为它们可以做与嵌套的`if`语句相同的检查，而不必费力阅读嵌套的`if`语句。