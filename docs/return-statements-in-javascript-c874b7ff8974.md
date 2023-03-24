# JavaScript 中的 Return 语句和自动分号插入

> 原文：<https://levelup.gitconnected.com/return-statements-in-javascript-c874b7ff8974>

*所有编写 JavaScript 的开发人员都应该理解自动分号插入，因为它与返回语句有关。*

![](img/e43e27cf0f8c87af72f5a616776f8119.png)

约书亚·阿拉贡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JS 使用自动分号插入。这意味着 JS 引擎将执行代码并在它认为合适的地方插入分号。

其中一个位置在 return 语句之后。

```
function createPerson(firstName, lastName) {
  return // ; <-- semicolon inserted by the JS engine
    { firstName, lastName };
}createPerson("Chris", "Jeffery") // returns undefined
```

要解决这个问题，我们必须在语句开始的同一行上扩展 return 语句。这可以用几种不同的方法来完成，但最明显的是用不同的括号。当然，`{`用于对象文字，`[`用于数组。左括号`(`仅仅代表一个通用表达式的开始。

```
return (...
return {...
return [...
```

下面的代码显示了修复函数的三个选项，以便它返回一个对象而不是`undefined`。它们在功能上都是等效的。

```
function createPerson(firstName, lastName) {
  return { firstName, lastName }
}function createPerson(firstName, lastName) {
  return { 
    firstName, 
    lastName,
  }
}function createPerson(firstName, lastName) {
  return (
    { firstName, lastName }
  )
}
```

大多数人幸福地没有意识到他们正在制造一个大错误。通常，前端开发人员使用 linters 来格式化代码，因此这大大降低了产生这个 bug 的风险。

无论如何，知识就是力量。