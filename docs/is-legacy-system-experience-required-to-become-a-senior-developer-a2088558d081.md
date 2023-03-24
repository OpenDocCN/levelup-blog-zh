# 成为高级开发人员需要遗留系统经验吗？

> 原文：<https://levelup.gitconnected.com/is-legacy-system-experience-required-to-become-a-senior-developer-a2088558d081>

## 看看遗留系统能教会你什么。

![](img/a0d1ef9e9eb9b8bd45de33ae7500696d.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 拍摄的照片

通常，软件开发人员试图避免从事遗留项目，原因包括低代码质量、纯文档、缺乏单元测试、大量技术债务等。

然而，在我看来，每个软件开发人员都应该在一生中至少尝试一次真正的遗留项目，以便获得其他类型的项目可能永远不会给你的经验，从而成为一名高级开发人员。

# 你知道事情不应该怎么做

软件开发人员在他们的职业生涯中学习最佳实践、编码技术、设计模式，以知道如何正确地做事。然而，这通常不足以成为成熟的专家，因为开发人员不仅需要知道如何正确地做一些事情，还需要知道如何不做这些事情。换句话说，开发人员不仅需要知道模式，还需要知道反模式。

遗留项目，由于其长的生命周期、团队轮换、缺乏全面的文档以及其他原因，可能包括大量的反模式、错误的设计和架构选择、不适当的工具使用等。

[](https://medium.com/geekculture/dont-let-your-enterprise-project-become-a-legacy-d282acfdf212) [## 每个企业项目都是遗产吗？

### 如何不让项目变成遗产？

medium.com](https://medium.com/geekculture/dont-let-your-enterprise-project-become-a-legacy-d282acfdf212) 

如果开发人员在处理遗留项目时每天都看到所有这些错误，他们就不太可能在未来的新项目中重复同样的错误。

此外，当开发人员面临一些实现不佳并给业务带来问题的东西时，他们应该考虑如何正确地返工，并起草一份详细的计划，包括估计、影响范围、测试策略等。这是开发人员快速学习并为客户增加价值的方法。

# 您将学习如何最小化回归问题的风险

遗留代码通常很脆弱，这意味着一些代码更改可能会破坏最初看起来与更改无关的功能。

处理遗留代码的脆弱性不仅教会开发人员更多地祈祷以避免退化，还教会他们以更科学的方式处理问题，例如:

*   分析要更改的代码的传入依赖项，以查看哪里可能出现回归问题。
*   检查要更改的代码的可用测试，以更好地理解该代码。
*   为要更改的代码编写新的单元、回归或特征测试，以最小化回归问题的风险。
*   将影响区域列表传递给质量保证团队。

这些步骤在我的另一篇文章中有更详细的描述:

[](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) [## 如何在不破坏应用程序的情况下修复 Bug

### 更改源代码时更自信的步骤。

levelup.gitconnected.com](/how-to-fix-bugs-and-not-introduce-new-ones-9f35e625673a) 

# 您显著提高了您的重构技能

在引入新特性或修复缺陷之前，遗留代码通常需要重构。这可能是一个小的重构，比如拆分临时变量、合并重复的条件片段、用静态工厂方法替换重载的构造函数等等。或者这可能是一个使用抽象技术、[扼杀者模式](https://docs.microsoft.com/en-us/azure/architecture/patterns/strangler-fig)等[分支的大重构。](https://martinfowler.com/bliki/BranchByAbstraction.html)

真正的遗留项目几乎每天都需要开发人员重构代码。这是大量的练习！结合阅读像《T4 重构:改进现有代码的设计》或《T6》这样的好书，有效地处理遗留代码可以让开发者成为重构大师。

# 你学习如何管理技术债务

当然，遗留项目有很多技术债务。技术债务积压规模取决于许多因素，如项目复杂性、项目年龄、团队之间的沟通等。

如果开发人员不按照技术债务管理最佳实践定期减少技术债务，技术债务往往会增加。

[](https://medium.com/codex/technical-debt-management-best-practices-for-software-engineers-871a315ac812) [## 软件工程师的技术债务管理最佳实践

### 技术债务率=应该返工的代码行数/现有源代码行数* 100%

medium.com](https://medium.com/codex/technical-debt-management-best-practices-for-software-engineers-871a315ac812) 

从事遗留项目，开发人员可以掌握使用静态代码分析工具检测技术债务、设计更好的解决方案、重构过程、规划回归测试等方面的技能。

此外，开发人员可以通过尝试向客户传达为与技术债务减少相关的工作分配预算的重要性来提高他们的软技能。

如果你正在做的遗留项目似乎不能教会你新的东西，不要气馁。当然可以，试一试吧。

## 我的其他文章

[](/how-many-repositories-do-you-need-for-a-microservices-project-b5c991aa440) [## 一个微服务项目需要多少个存储库？

### 在多存储库和单存储库之间找到平衡点。

levelup.gitconnected.com](/how-many-repositories-do-you-need-for-a-microservices-project-b5c991aa440) [](/3-ways-to-implement-strategy-design-pattern-in-c-a58548d8a4ad) [## 在 C#中实现策略设计模式的 3 种方法

### 战略模式的实际应用。

levelup.gitconnected.com](/3-ways-to-implement-strategy-design-pattern-in-c-a58548d8a4ad) [](/fast-database-fast-application-useful-db-performance-optimization-techniques-34b6926d1196) [## 快速数据库—快速应用程序(有用的数据库性能优化技术)

### 了解加速关系数据库的最佳实践。

levelup.gitconnected.com](/fast-database-fast-application-useful-db-performance-optimization-techniques-34b6926d1196)