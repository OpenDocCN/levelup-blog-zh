# 5 GitHub。净回购，让你的技术技能更上一层楼

> 原文：<https://levelup.gitconnected.com/5-github-repositories-for-net-developers-to-take-tech-skills-to-the-next-level-84f244f6257b>

## 亲身体验 GitHub 资源库。

![](img/895cc9dc0986cece3653bfa8b609d1ff.png)

罗曼·辛克维奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

发布在 GitHub 存储库上的示例项目已经成为软件开发人员的优秀知识来源，书籍、在线课程和专家博客也是如此。

样本项目的代码分析允许开发人员巩固他们从阅读一本书或一篇文章中获得的理论知识，并了解某些开发概念如何在实践中应用。

好的示例项目不必太复杂，否则开发人员会半途而废。

另一方面，示例项目不一定要足够简单才能有用。

我整理了 5 个我认为符合上述要求的好回购！存储库包含基于。NET 技术堆栈，您可以使用它成为一名更有经验的软件开发人员。

# 1.带 DDD 的模块化整体结构

[**资源库**](https://github.com/kgrzybek/modular-monolith-with-ddd) 演示了如何以模块化的方式构建一个整体应用程序。

项目模块是松散耦合的，只使用内存中的事件总线相互通信。每个应用程序模块都有自己的组合根，用于设置与阿迪容器的依赖关系。

所使用的方法提供了一个清晰的关注点分离，使得迁移到微服务比传统的整体项目更容易。

该项目可以教你如何应用领域驱动的设计概念，CQRS 和事件采购方法，事件风暴，C4 模型和其他。

该项目是使用。NET Core 3.1，Autofac，IdentityServer4，MediatR，Swashbuckle，Entity Framework Core 3.1，Dapper，Quartz.NET，SSDT 数据库项目，DbUp，NetArchTest 等一些技术。

🔔 [**现在就订阅**](https://esashamathews.medium.com/subscribe) **，所以大家不要错过我接下来的文章。**

# 2.EventSourcingNetCore

[**存储库**](https://github.com/oskardudycz/EventSourcing.NetCore) 旨在描述事件源的概念及其在中的实现。NET 5 示例应用程序。

这是一个知识库，可以帮助你了解几乎所有关于事件采购的概念。

存储库的教程部分通过回答问题“*什么是事件”开始描述事件源。*"对事件源的所有剩余概念的描述，并附有代码示例。

关键技术是。NET 5，Kafka，Marten，Docker，MediatR 等。

# 3.样本。网络核心 CQRS API

[**存储库**](https://github.com/kgrzybek/sample-dotnet-core-cqrs-api) 包含了使用 CQRS 方法实现干净架构的样本项目。

读取模型使用 Dapper 框架从数据库中获取数据。写模型实现了域驱动的设计方法，并使用实体框架核心将数据持久化到数据库中。

项目实现的其余有趣的东西是缓存和发件箱模式。

主要技术有。NET Core 3.1，MediatR，FluentValidation，Quartz.NET，Swashbuckle 等。

# 4.清洁建筑漫画

[**存储库**](https://github.com/ivanpaulovich/clean-architecture-manga) 包含示例虚拟钱包单页面 web 应用程序，该应用程序是使用干净架构的原则实现的。

存储库的教程部分有结构良好的详细目录[表](https://medium.com/r?url=https%3A%2F%2Fgithub.com%2Fivanpaulovich%2Fclean-architecture-manga%23index-of-clean-architecture-manga)，可以帮助快速找到与实现特性标志、使用 EF 核心迁移、向项目添加认证和授权、实现域驱动模式等相关的各种实际问题的答案。

本项目采用的主要技术有。NET 5，React 和 Redux，Docker，Swagger，nginx。

# 5.白色 App，洋葱架构，带 ASP.NET 内核

最终的 [**存储库**](https://github.com/Amitpnk/Onion-architecture-ASP.NET-Core) 包含一个名为 WhiteApp 的示例项目，它实现了用。NET 5 技术栈。

详细描述了设置、构建和运行应用程序的步骤。

WhiteApp 项目比前面描述的类似项目简单。如果以前的项目对你来说太难了，学习这个项目对你来说是一个好的开始。

阅读 GitHub 资源库除了阅读基础编程书籍，查看高评分的在线课程，阅读专家博客可以让你的技术技能达到一个全新的水平，绝对值得一试。

## 我的其他文章:

[](/5-asp-net-core-open-source-projects-to-gain-practical-knowledge-24fbf9164230) [## 5 个 ASP.NET 核心开源项目，获取实用知识

### 在实践中学习。

levelup.gitconnected.com](/5-asp-net-core-open-source-projects-to-gain-practical-knowledge-24fbf9164230) [](/50-software-engineering-best-practices-for-technical-leaders-and-seniors-cfcdf6a17e44) [## 面向技术领导者和资深人士的 50 个软件工程最佳实践

### 最佳工程师的最佳实践。

levelup.gitconnected.com](/50-software-engineering-best-practices-for-technical-leaders-and-seniors-cfcdf6a17e44) [](/5-ways-to-create-an-object-in-c-and-when-to-choose-which-one-4aabea5c3e40) [## 用 C#创建对象的 5 种方法以及何时选择哪一种

### 当创建一个对象时，像建筑师一样思考。

levelup.gitconnected.com](/5-ways-to-create-an-object-in-c-and-when-to-choose-which-one-4aabea5c3e40)