# 增强 Javascript 代码的 15 个特性

> 原文：<https://levelup.gitconnected.com/15-features-to-supercharge-your-javascript-code-8971c65b5af6>

如何使用它们，以及为什么你会喜欢它们！

![](img/13ca60d8f3608387ca296d78b9f5b518.png)

由[克里斯蒂娜·布兰科](https://unsplash.com/@starvingartistfoodphotography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Javascript 一直在发展和改进，不断地向其底层语言规范 [ECMAScript](https://www.ecma-international.org/publications-and-standards/standards/ecma-262/) 添加额外的特性。

很容易错过这些可以让你的代码更干净、更简洁的新特性。下面我收集了一些我认为你应该利用的关键特性，以及一些有用的提示。

# 1.箭头功能

箭头函数已经存在一段时间了。如果你还没有使用它们，它们是让你的功能更加简洁的好方法。

箭头函数只是编写传统函数的一种更简洁的方式。

使用它们时，有几条规则必须遵守:

*   圆括号`()`对于只有一个参数的箭头函数是可选的。对于 0 个或 1 个以上的参数，它们是必需的。
*   花括号`{}`只有在你想让箭头函数多行的时候才需要，对于单行函数可以省略。
*   如果你的 arrow 函数使用了花括号，那么你**必须**在末尾包含一个 return 语句。否则，它是隐式的，不是必需的。

箭头函数示例

[*箭头功能文档*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) *。*

# 2.Array.map(f)

接受一个具有单个输入的函数，将其应用于数组中的每个元素，并输出新的变异值。

Array.map(f)示例

[*Array.map(f)文档*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 。

# 3.数组.过滤器(f)

接受一个只有一个输入的函数作为谓词。过滤器只收集与谓词匹配的元素。

数组.过滤器(f)示例

[*Array.filter(f)文档*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 。

# 4.解构

析构允许我们将对象和数组中的值直接解包到变量中。

解构范例

[*析构文档*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 。

# 5.传播算子

spread 操作符`...`允许一个 iterable 对象在需要多个元素的地方被解包。

扩展运算符示例

[*传播操作员文档*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) *。*

# 6.模板字符串

模板字符串便于简单的插值并支持多行字符串。

模板字符串通过封装在反斜线```中来定义。

要插入变量，将它们放在字符串内的`${}`中。

模板字符串示例

[*模板字符串文档*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) *。*

# 7.异步/等待

ES8 最终给了我们用`async`和`await`关键词回调 hell 的解决方案。我们不再需要编写又长又丑的承诺链。

`async`关键字将方法返回类型改为 promise，并使其与`await`关键字兼容。

`await`关键字是`myPromise.then(...)`的简写。它允许您等待一个承诺的成功完成，并直接将其输出赋给一个变量。

异步/等待示例

[*异步/等待文档*](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await) *。*

# 8.对象文字

当构造一个对象文字时，如果变量名相同，就不需要显式定义键。

对象文字示例

[*对象初始值设定项文档*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer) *。*

# 9.零合并

这是一个逻辑运算符，如果左操作数为空，则返回右操作数的值。该运算符`??`可与 or 运算符`||`互换使用。

零合并示例

[*空聚文档*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator) *。*

# 10.缩短条件句

这是一种简化一些短条件函数的方便方法，尽管它并不总是更易读，所以要明智地使用它。

简短条件句示例

# 11.最后

一旦尝试/捕获或承诺完成，执行一些处理或清理的简单方法，无论结果如何。可以与承诺和异步/等待代码一起使用。

最后一个例子

[*最终文件*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/finally) 。

# 12.对象条目

可以提供一种简单的方法来迭代对象的键和值。

对象.条目示例

[*参赛文件*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries) 。

# 13.忽略尝试/捕获异常标识符

如果您不关心 try-catch 块中抛出的异常，那么它可以从 catch 子句中省略。

省略 try/catch 异常示例

[*试捕文书*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch) 。

# 14.Array.reduce(f)

取一个 reducer 函数，应用于数组/可迭代表中的每个元素，**将其简化为**单值**。**

减压器函数采用两个参数，一个累加器和一个电流值。

累加器是“累加”总结果的值。对数组中的每个元素依次调用 reducer 函数。每个减速器功能的输出作为累加器变量传递给下一个。

当前值只是数组的当前值，位于缩减器正在查看的索引处。

Array.reduce(f)示例

[*Array.reduce(f)文档*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 。

# 15.Array.some(f)

接受一个谓词，并根据数组对其进行评估，以确定是否至少有一个**元素与该谓词匹配。请注意，这不会扫描整个阵列，它会在找到匹配项后立即终止。**

**Array.some(f)示例**

**[*阵法*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some) 。**

**如果你喜欢这篇文章，并且愿意支持我成为一名作家，考虑注册[成为一名媒体成员](https://adam-galtrey.medium.com/membership)。每月只需 5 美元，你就可以无限制地阅读 Medium 上的所有文章。如果你[用我的链接](https://adam-galtrey.medium.com/membership)注册，我也会赚一小笔佣金。**