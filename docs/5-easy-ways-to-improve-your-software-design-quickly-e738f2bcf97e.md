# 改进软件设计的 5 种简单方法

> 原文：<https://levelup.gitconnected.com/5-easy-ways-to-improve-your-software-design-quickly-e738f2bcf97e>

## 简单的解决方案在 80%的情况下都有帮助。

![](img/65e73d419ed2c50b49e75297d3c40a7e.png)

[Alex Chumak](https://unsplash.com/@ralexnder?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

以面向对象、函数式或混合风格设计代码是一门艺术，需要软件开发人员通过阅读基础书籍、实践、犯错误并从中学习来不断磨练他们的知识和技能。

代码设计包括各种复杂程度的无数技术、模式和原则。然而，不仅仅是应用难学的知识可以显著提高你申请的质量。通常，简单的方法会让软件开发人员受益匪浅。

# 1.避免部分初始化的对象

域类中缺少构造函数会导致应用程序中的对象部分初始化，这反过来又会导致令人讨厌的错误。

## 严重的

## 好的

糟糕的实现允许开发人员创建没有姓名和/或电子邮件的客户——开发人员很容易忘记初始化属性，但代码会编译。

坏的例子从编程语言的角度来看是完全正确的，但是从领域的角度来看是无效的。每个新创建的客户都必须有一个名字和电子邮件——这是领域语言规定的，但是一个坏例子的代码没有强制执行这个领域规则。

一个好的例子需要构造函数中的参数。构造器是`Customer`类和每个客户端之间的契约，客户端将通过`new`关键字实例化它。现在客户必须提供用户名和电子邮件，否则他们将无法获得`Customer`实例。

注意，设置器也被移除了，这提供了更好的封装——实例`Customer`在创建后不会因为意外清除`Email`或`Username`属性而被破坏。

# 2.将您的代码编写为管道

当需要执行一系列对象突变时，编写代码的流水线方式可以保护应用程序免受错误的影响，并且更易于阅读。

## 严重的

## 好的

好的和坏的例子在行为上是完全等价的。

然而，一个坏的例子是坏的，因为方法是顺序敏感的。在试图过滤掉不活跃的客户之前，必须加载客户。有人可能会不小心交换了`RemoveNonActiveCustomers`和`LoadCustomers`方法。代码可以编译，但是在运行时会失败。

在这个好例子中，上面的缺点被消除了，在这个例子中，方法不仅修改集合，还将结果返回给调用者。

偶然交换行，比如 1 和 2，将导致编译时错误。此外，基于纯函数的代码更容易阅读，因为在一个很好的例子中，代码看起来像管道。在每一个阶段，我们收到什么和我们传递给下一个阶段的东西都很清楚。

# 3.使用 IReadOnlyList <t>而不是 IEnumerable <t>作为返回类型</t></t>

`IEnumerable<T>`接口提供了一种迭代来自内存、数据库、网络、文件等的数据的方法。

返回类型`IEnumerable<T>`是存储库方法的典型选择。然而，使用`IEnumerable<T>`可能会导致性能问题。

## 严重的

## 好的

*注意:对于第三方库，当方法将被其他外部代码库使用时，最好返回更通用的类型 IEnumerable < T >。这允许您轻松地在不同的集合类型之间切换，例如 HashSet < T >，List < T >，Dictionary < TKey，TValue >等，并且不会中断您的客户端。*

当使用`IEnumerable<T>`作为返回类型时，开发人员应该记住在将结果返回给调用者之前，通过调用`ToList, ToArray or ToWhatever`方法在方法内部具体化结果。如果不这样做，每次调用方遍历集合时，结果都将被具体化，这可能会导致严重的性能下降。

[](/5-ways-to-improve-the-performance-of-c-code-for-free-c89188eba5da) [## 5 种免费提高 C#代码性能的方法

### 慢速代码是可选的。

levelup.gitconnected.com](/5-ways-to-improve-the-performance-of-c-code-for-free-c89188eba5da) 

当使用`IEnumerable<T>.`时，很容易忘记在方法中具体化集合，然而，使用`IReadOnlyList<T>`接口将要求开发人员在将结果发送给调用者之前调用`ToList()`方法。否则，代码将无法编译。

# 4.使用 dto 代替部分初始化的对象

有时有必要从数据库中只获取一个对象的一些属性，而不是整个对象。这通常是获得最佳性能所必需的。

## 严重的

## 好的

糟糕的例子使用相同的`Customer`模型，即使存储库方法只需要返回客户的一个或几个属性。这导致在应用程序中有部分初始化的对象。

当开发人员通过调用抽象顶层的`GetCustomerContacts`方法来获得客户类型的实例时，他们不确定客户的哪些属性被初始化了，哪些没有。要回答这个问题，开发人员必须深入研究存储库实现细节。

一个很好的例子是使用非常特殊的类型`Guid`和`CustomerContactsDto`，它们总是被完全初始化，所以更容易使用。

# 5.使用只读和只写类

存储库、服务、控制器和其他类可以混合读写数据的职责。将这样的类分成只读类和只写类，是一种简单的技术，可以带来更好的软件设计。

## 严重的

## 好的

阶级的这种划分导致几个很大的优势:

*   有两个小班，而不是一个大班。类越小，依赖就越少，分析、调试、维护和单元测试就越容易。
*   写入和读取数据通常需要不同的方法和工具。比如把对象的复杂图形保存到数据库，用实体框架核心比较好，从数据库快速检索数据，用 Dapper 比较好。从可维护性的角度来看，在同一个类中混合 EF 核心和 Dapper 逻辑不是一个好主意。

# 结论

简单的解决方案可以带来巨大的进步。它总是值得考虑，他们主要是一个有效的开发人员谁得到快速完成工作。

## 我的其他文章

[](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [## 如何在不破坏应用程序的情况下修复 Bug

### 更改源代码时更自信的步骤。

levelup.gitconnected.com](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [](/treat-if-else-as-a-code-smell-until-proven-otherwise-3bd2c4c577bf) [## 将 If-Else 视为代码气味，直到被证明并非如此

### C#中 If-Else 重构技术

levelup.gitconnected.com](/treat-if-else-as-a-code-smell-until-proven-otherwise-3bd2c4c577bf) [](https://medium.datadriveninvestor.com/estimation-hell-or-what-can-developers-do-better-with-their-estimates-614f9e71f43d) [## 评估地狱，或者开发人员如何利用他们的评估做得更好？

### 估计过程很难，但没那么难。

medium.datadriveninvestor.com](https://medium.datadriveninvestor.com/estimation-hell-or-what-can-developers-do-better-with-their-estimates-614f9e71f43d)