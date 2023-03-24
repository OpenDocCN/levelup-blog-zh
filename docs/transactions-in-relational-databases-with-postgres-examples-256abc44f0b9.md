# 关系数据库中的事务与 Postgres 示例

> 原文：<https://levelup.gitconnected.com/transactions-in-relational-databases-with-postgres-examples-256abc44f0b9>

## 什么是数据库中的事务以及如何使用它们！

![](img/c272491a594821f13d58dea46ba9d35f.png)

克里斯托夫·高尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是交易？

在数据库的上下文中，事务是一组操作，在事务最终作为一个整体提交之前，这些操作应用于一个或多个表。

理解这一点非常重要，如果一个事务的一个操作失败，整个事务都会失败，并且会发生回滚来恢复失败事务中应用的更改。

让我用一个基于 **Postgres 13** 的例子来解释这种行为。

# 创建表格

首先，我们需要在你想要的数据库中创建一个表，我们把这个表叫做: **bank_account** 。我们将**约束**添加到**平衡**列:

余额> = 0

这意味着列**余额**不能小于 0。在表 **bank_account** 被创建后，我们将插入 2 行:

埃隆余额 100，杰夫余额和我们第一排(埃隆)一样多。

在插入两行中创建一个表。

我们的 **bank_account** 表现在看起来像这样:

![](img/79d3ccd4546ce42560e1c0437a5d9982.png)

# 成功的交易

在我们的第一笔交易中，我们给 Elon 增加了 50 英镑的余额，同时我们从 Jeff 那里扣除了同样的金额。那笔交易成功了，因为杰夫的余额还在 50 以上，他的余额不会少于 0。

该事务将成功运行。

现在，我们的**银行账户**表将如下所示:

![](img/7058a9bbb8c065c019820cae9e861dd9.png)

# 失败的事务(带回滚)

我们记得，埃隆的余额是 150，而杰夫的余额是 50。现在我们做的是非常相似的交易。但是这一次，我们将 Jeff 的银行帐户余额减少 150，同时将 Elon 的银行帐户余额增加 150。

你能猜到现在会发生什么吗？我们先来看看交易片段。

该事务将失败并恢复其更改。

这项交易将**失败**，因为杰夫的余额将少于 0。

但是请记住，在交易失败之前，我们已经将 Elon 的余额增加了 150，正如您在上面的交易片段的第 2 行中所看到的。但是，这一更改不会在最后提交，因为事务在第 3 行失败了。Postgres 将自动执行回滚。所以你不需要自己为此烦恼。

感谢您阅读我关于关系数据库事务的文章。我希望，我可以刷新你对这个特定主题的知识。

干杯！

我希望你喜欢读这篇文章。如果你愿意支持我成为一名作家，考虑注册[成为一名媒体成员](https://medium.com/@hellokevinvogel/membership)。每月只需 5 美元，你就可以无限制地使用 Medium。

想支持我？[给我买杯咖啡。](https://www.buymeacoffee.com/hellokevinvogel)

# 接下来阅读

[](https://blog.devgenius.io/object-orientated-programming-oop-in-typescript-c1066941f5ee) [## TypeScript 中的面向对象编程(OOP)

### TypeScript 对 JavaScript 中面向对象编程的实现有很大的影响。今天，我想谈谈…

blog.devgenius.io](https://blog.devgenius.io/object-orientated-programming-oop-in-typescript-c1066941f5ee) [](https://blog.bitsrc.io/node-js-event-loop-and-multi-threading-e42e5fd16a77) [## Node.js 事件循环:不是单线程的

### Node.js、事件循环和多线程

blog.bitsrc.io](https://blog.bitsrc.io/node-js-event-loop-and-multi-threading-e42e5fd16a77) [](https://blog.bitsrc.io/how-to-build-a-simple-api-in-typescript-with-nest-js-876386b29753) [## 用 NestJS (2022)在 TypeScript 中构建一个简单的 API

### 如何在 TypeScript 中构建 NestJS API 的简要指南

blog.bitsrc.io](https://blog.bitsrc.io/how-to-build-a-simple-api-in-typescript-with-nest-js-876386b29753)