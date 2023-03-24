# 奎因:用法和最佳实践

> 原文：<https://levelup.gitconnected.com/quines-usage-and-best-practices-41e988ae6894>

## 编程；编排

## 使用 ES6 JavaScript 探索 Quines！

![](img/acf2bcfd2c4bd64425f1bb8ef6b68d90.png)

[粘土银行](https://unsplash.com/fr/@claybanks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

quine 是一种计算机程序，它不接受任何输入，只生成自己源代码的副本作为唯一输出。“蒯因”一词是数学家兼哲学家威拉德·范·奥曼·奎因根据“查询”一词创造的。

蒯因的概念可以追溯到 20 世纪 50 年代，当时数学家道格拉斯·霍夫施塔特(Douglas Hofstadter)写了一本名为《哥德尔、埃舍尔、巴赫:一条永恒的金色辫子》的书，他在书中引入了自指句的思想。这个想法后来被奎因扩展，他用它来描述一个产生自己的源代码作为输出的程序。

第一个已知的奎因是由程序员和逻辑学家 J. H. Conway 在 20 世纪 60 年代早期写的。它是一个简短的自引用程序，用 LISP 编程语言编写，简单地输出自己源代码的副本。从那以后，quines 被写成了许多不同的编程语言，它们已经成为计算机科学家和程序员之间的热门研究课题。

奎因经常被认为是对程序员的挑战，因为他们需要一定程度的创造力和独创性来创造。一些最复杂的查询是那些能够生成自己的源代码的多个副本的查询，或者那些使用编程语言的高级功能来产生其输出的查询。

## 用 ES6 JavaScript 写的例子

1.  第一个示例使用 ES6 模板字符串语法输出自己的源代码:

```
const quine = () => {
  const src = `const quine = () => {
    const src = \`${src}\`;
    return src;
  }`;
  return src;
}

console.log(quine());
```

2.第二个查询使用 ES6 扩展操作符创建自己源代码的副本:

```
const quine = () => {
  const arr = [...arr, ...arr];
  return arr.join("\n");
}

console.log(quine());
```

3.第三个 quine 使用 ES6 映射函数创建其自己源代码的多个副本:

```
const quine = () => {
  const src = `const quine = () => {
    const src = \`${src}\`;
    return src;
  }`;
  return [...Array(5)].map(() => src).join("\n\n");
} 

console.log(quine());
```

4.第四个 quine 使用 ES6 reduce 函数连接自己源代码的多个副本:

```
const quine = () => {
  const src = `const quine = () => {
    const src = \`${src}\`;
    return src;
  }`;
  return [...Array(5)].reduce((acc, _) => acc + "\n\n" + src, "");
}

console.log(quine()); 
```

5.最后一个示例使用 ES6 *String.raw* 方法以最小的转义输出自己的源代码:

```
const quine = () => {
  const src = String.raw`const quine = () => {
    const src = \`${src}\`;
    return src;
  }`;
  return src;
}

console.log(quine()); 
```

这些例子展示了使用 ES6 JavaScript 的高级特性编写查询的创造性方法。无论是作为编程挑战还是作为代码生成工具，quines 都继续吸引和激励着计算机科学家和程序员。

## 在 ES6 JavaScript 中使用 quines 时要考虑的最佳实践:

1.  避免使用`eval`或新函数来评估查询的输出，因为这可能会引入安全漏洞。相反，使用内置的 JavaScript 方法来执行代码，比如沙盒环境中的`Function.prototype.call`或`eval`函数。
2.  请注意使用 quine 生成和执行大量代码的性能影响。Quines 可以快速生成大量源代码，这会影响程序的性能。
3.  在源代码中使用注释来清楚地解释 quine 是如何工作的，并为理解代码提供任何必要的上下文。这可以帮助其他正在阅读和使用 quine 的程序员。
4.  避免在 quine 中使用全局变量或改变现有变量，因为这可能导致不可预测的行为，并使代码难以理解。相反，使用函数式编程技术和局部变量来确保查询是结构良好且可预测的。
5.  彻底测试 quine，以确保它生成了预期的输出，并且生成的代码是正确且格式良好的。这有助于防止 quine 中的任何错误，并确保它是可靠和值得信赖的。
6.  下面是一个遵循这些最佳实践的结构良好的奎因的示例:

```
const quine = () => {
  // Use a local variable to store the source code of the quine
  const src = `const quine = () => {
    // Use a local variable to store the source code of the quine
    const src = \`${src}\`;
    return src;
  }`;

  // Use the Function constructor to create a new function from the source code
  // of the quine, and return the result of calling that function
  return new Function(src)();
}

console.log(quine());
```

这个查询使用一个局部变量来存储查询的源代码，并使用函数构造器以安全和可预测的方式执行生成的代码。它注释良好，易于理解，是在 ES6 JavaScript 中使用 quines 的最佳实践的好例子。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)