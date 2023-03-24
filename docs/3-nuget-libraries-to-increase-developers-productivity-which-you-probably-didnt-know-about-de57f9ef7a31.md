# 3 使用库来提高开发人员的生产力(这一点你可能不知道)

> 原文：<https://levelup.gitconnected.com/3-nuget-libraries-to-increase-developers-productivity-which-you-probably-didnt-know-about-de57f9ef7a31>

## 用于架构单元测试的库，查找并发错误等等。

![](img/018718508bdde583ea50e39babcbb7cb.png)

卢克·皮特斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

软件开发人员需要什么来快速交付高质量的工作？除了计算机科学的基础知识、丰富的经验、批判性思维之外，开发人员应该使用市场上提供的库，这样就不会重新发明轮子，更快地完成任务或解决问题。

NuGet 画廊提供了数以千计的图书馆，其中一些是很难找到的真正的钻石。

# NodaTime

[NodaTime](https://nodatime.org/) 库是由 John Skeet 创建的，目的是解决与日期和时间、持续时间、时区、日历系统相关的各种问题，而不仅仅是内置的。NET 类型(DateTime、DateTimeOffset 和其他类型)具有。

NodaTime 不仅仅是 C#开发人员处理日期和时间的另一套精炼 API。NodaTime 细化了日期和时间的整个概念，使其比内置的更“领域驱动”。网络类型。

# 网络测试

NetArchTest 是一个库，它允许你通过编写单元测试来实施架构和设计应用程序特定的约定。

该库实现了预定义的规则，能够实现用于编写单元测试的自定义规则，这些单元测试可以检查应用程序代码的不变性、封装、DDD 规则、正确的项目分层、命名约定等。

你可以查看我的另一个帖子，了解更多关于如何开始的信息:

[](/netarchtest-enforce-architecture-and-design-rules-in-your-application-e6c6c9f5c97e) [## NetArchTest —在您的。网络应用

### 通过编写单元测试。

levelup.gitconnected.com](/netarchtest-enforce-architecture-and-design-rules-in-your-application-e6c6c9f5c97e) 

# 丛林狼

微软的 [Coyote](https://github.com/microsoft/coyote/) 库帮助您对多线程应用充满信心，因为它可以发现并发问题，如死锁、与线程并行执行相关的不确定性行为等。

Coyote 不仅允许您发现并发问题，更重要的是，允许您不断地重现这些问题，这大大简化了调试过程。

# 结论

软件开发人员面临的每一个新的编程挑战都值得花 15-30 分钟在市场上找到合适的库，因为从长远来看，这对开发人员的好处是巨大的。

## 我的其他文章

[](/why-is-list-struct-is-15-times-faster-to-allocate-than-list-class-17f5f79889ae) [## 为什么 C#中 List <struct>的分配速度比 List <class>快 15 倍</class></struct>

### 在上一篇文章《免费提高 C#代码性能的 5 种方法》中，在其中一个例子中，我测量了…

levelup.gitconnected.com](/why-is-list-struct-is-15-times-faster-to-allocate-than-list-class-17f5f79889ae) [](/5-asp-net-core-open-source-projects-to-gain-practical-knowledge-24fbf9164230) [## 5 个 ASP.NET 核心开源项目，获取实用知识

### 在实践中学习。

levelup.gitconnected.com](/5-asp-net-core-open-source-projects-to-gain-practical-knowledge-24fbf9164230) [](/3-ways-to-implement-strategy-design-pattern-in-c-a58548d8a4ad) [## 在 C#中实现策略设计模式的 3 种方法

### 战略模式的实际应用。

levelup.gitconnected.com](/3-ways-to-implement-strategy-design-pattern-in-c-a58548d8a4ad)