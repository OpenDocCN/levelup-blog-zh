# 编程中的类型脚本类型

> 原文：<https://levelup.gitconnected.com/typescript-types-in-programming-ec6cfdb6e02d>

## 有角的

## 开发人员在使用 TypeScript 时需要了解的基本 TypeScript 类型。

![](img/1a133fb0ad3498abbb975aa8d8ca0ab1.png)

在 [Unsplash](https://unsplash.com/s/photos/cafe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上[达安·埃弗斯](https://unsplash.com/@daanelise?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)的照片

这个故事是关于 TypeScript 中的类型，TypeScript 是 JavaScript 的一个类型化超集，可以编译成普通的 JavaScript。下面我将更详细地解释一些术语。

*   原始类型
*   列举
*   工会类型
*   文字类型
*   交叉点类型
*   数组
*   元组类型
*   字典类型
*   映射类型
*   受歧视的工会

你可以在 TypeScript 主页的这里阅读原始文档[或者](https://www.typescriptlang.org/index.html)[这个关于 JavaScript 类型的故事](/summary-of-data-types-in-javascript-a04d02715a9a)。

*更多类似内容，请查看*[*https://betterfullstack.com*](https://betterfullstack.com/)

# 原始类型

TypeScript 中的基本类型类似于 JavaScript

*   线
*   布尔型
*   数字
*   标志

**记住**:

*   `undefined`类型是尚未赋值的变量值。
*   `null`类型可以用来表示对象值的有意缺失。
*   `void`类型用于表示没有值的情况。说明一个函数不返回任何东西。
*   `never`类型代表一段不可到达的代码。

# 列举

*枚举允许开发者定义一组命名常量*。

使用枚举可以更容易地记录意图，或者创建一组不同的事例。TypeScript 同时提供数字和基于字符串的枚举。

下面的代码是一个枚举的例子，是编译成 JavaScript 后的样子。

TypeScript 中的枚举车辆类型

有趣的是，我们可以在 Typescript 中使用 Enum 来定义**位标志**。

> **位标志**是 c 语言中的一种编程技术，用于最小化相关真/假**标志所需的空间。**

以下是示例:

TypeScript 中的位标志示例

分配给枚举中每一项的值可以是常数，也可以是计算值。

*   ***常量值*** *是类型系统可以解释的任何表达式，如文字值、计算、二元运算符等。*
*   ***计算值*** *是编译器无法有效解释的表达式，比如分配字符串长度，或者调用方法。*

# 工会类型

*联合类型描述可以是几种类型之一的值。*

简单地说，您会遇到这样一种情况，希望参数要么是数字，要么是字符串。

typescript 中的联合类型

上面的例子包括 3 种主要情况:

*   可变的
*   功能
*   公共字段

# 文字类型

文字类型可用于将允许值的范围缩小到该类型的一个子集，例如将一个字符串缩减为一组特定的值。

这意味着`"Hello World"`是一个`string`，但是在类型系统中`string`不是`"Hello World"`。

typescript 中的文字类型

注意:目前在 TypeScript 中有两组文字类型，字符串和数字。

# 交叉点类型

交集类型与并集类型密切相关，但是它们的用法非常不同。

> 交集类型将几个不同的类型组合成一个超类型，该超类型包括所有参与类型的成员。

typescript 中的交集类型

注意:交集类型在处理混合时很有用。

# 数组

与 JavaScript 一样，TypeScript 允许您使用值数组。

数组类型可以用两种方式之一编写。

*   使用括号`[]`
*   使用`Array<elemType>`

typescript 中的数组类型

# 字典类型

您可以使用**索引类型**在 TypeScript 中表示字典。

typescript 中的字典类型

# 映射类型

为了减少创建仅在可选性或可读性方面不同的相似类型所需的工作量，映射类型允许您在单个表达式中创建现有类型的变体。

typescript 中的映射类型

# 受歧视的工会

使用联合的一种常见技术是拥有一个使用文字类型的单个字段，您可以使用该字段让 TypeScript 缩小可能的当前类型。

构成区别联合的组件如下:

*   几种类型共享一个共同的字符串文字属性，称为判别式。
*   这些类型的联合的类型别名，称为联合。
*   检查判别式的一种类型保护装置。

我希望这篇文章对你有用！可以跟着我上[媒](https://medium.com/@transonhoang?source=post_page---------------------------)。我也在推特上。欢迎在下面的评论中留下任何问题。我很乐意帮忙！

这个故事中的一些例子来自[https://www . typescriptlang . org/docs/handbook/basic-types . html](https://www.typescriptlang.org/docs/handbook/basic-types.html)

[](https://betterfullstack.com/stories/) [## 故事-更好的全栈

### 关于 JavaScript、Python 和 Wordpress 的有用文章，有助于开发人员减少开发时间并提高…

betterfullstack.com](https://betterfullstack.com/stories/)