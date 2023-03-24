# 为什么应该使用 TypeScript + GraphQL

> 原文：<https://levelup.gitconnected.com/why-you-should-be-using-typescript-graphql-5d87633f5363>

## 使用这种强大组合的学习成果。

![](img/73f8e3d8d28fdc5d34fa28e58e5aeef7.png)

# **简介**

今天我要写的是关于[打字稿](https://www.typescriptlang.org/)和[图表。我将简要介绍这些技术，然后直接进入学习成果。](https://graphql.org/)

我已经使用这个堆栈将近两年了。我首先从 GraphQL 开始，这启发了我钻研 TypeScript。还有很多东西需要学习，技术在不断发展，人们的兴趣在迅速增长，尤其是在企业领域，每天都有新的应用程序发布。

![](img/241ea80db253718513ea7aab1ca64279.png)

GraphQL + TypeScript

# **什么是 GraphQL？**

有许多介绍 GraphQL 的文章。如果你问 10 个不同的人 GraphQL 是什么，你会得到 10 个不同的答案。在这篇文章里我不会花太多时间解释。我建议阅读一些不同的观点，然后尝试一些 GraphQL 代码。

Graph QL(Graph**Q**uery**L**language)是一种描述你拥有什么，你想要什么，并提供算法来解决这些请求的语言。

服务器提供整个可用数据的定义以及该数据的相关类型。数据以对象的形式呈现，我们知道所有键的名称和相关值的类型。这意味着您的后端是完整记录的，我们可以使用工具轻松地检查它。

只有一个端点可以向 GraphQL 服务器发出请求。客户机发送它需要的数据的键，服务器以 JSON 对象的形式响应产生的键-值对。简单地说，客户要求它想要的东西。

它被称为 REST(表述性状态转移)的替代品，但它在概念上超出了该规范。

在服务器端有很多好处。您可以使用 [SDL](https://graphql.org/learn/schema/) (模式定义语言)来定义您的 API 模式，然后您可以获得自动自省和自我文档。有些解析器是将数据映射到请求的函数，而字段解析器是递归的。如果字段产生一个标量值，如字符串或数字，解析器执行完成。但是，如果一个字段产生一个对象值，那么查询将包含应用于该对象的另一个字段选择。

# 什么是 TypeScript？

[TypeScript](https://www.typescriptlang.org/) 是一种编程语言，是一套应用开发的工具。它是由[微软](https://www.microsoft.com)创造的，7 年前发布，从那以后人们的兴趣一直在增长。

JavaScript 一直因缺乏变量的类型安全性而受到批评，但随着 TypeScript 的出现，这种情况有所改变，它提供了一组(严格的)ECMAScript 特性。它将文件转换为 JavaScript，所以它包含了所有的标准特性，并添加了新的东西，如类型注释和面向对象的概念，如类和接口。它还支持未来新的 JavaScript 特性，比如异步函数和装饰器。

TypeScript 提供编译时检查和强静态类型。这使得像 [VS Code](https://code.visualstudio.com/) 这样的 IDE 工具变得“更加智能”，提供了内嵌代码完成和错误检查。

> 我以为我懂 API 开发，但是 GraphQL 和 TypeScript 让我成为了更好的 API 开发者。

# 学习成果

下面是学习 TypeScript 和 GraphQL 的一些结果。我试图强调我认为超越 REST 和传统 JavaScript 的概念。

## **加深对 API 的理解**

从 REST 之前就开始开发 API(应用编程接口)。我见过一些不同的方法来来去去，还有其他规范，如 SOAP (XML)、CORBA 和 Hateoas。我研究过数百个 REST 端点。我以为我懂 API 开发，但是 GraphQL 和 TypeScript 让我成为了更好的 API 开发者。

GraphQL API 本质上是自省的。您可以获得所有可用数据的全类型模式，使其自文档化。这允许您发展对 API 的强烈直觉，并增强您对数据类型、实体和抽象的意识。

你没有重载端点，也没有过度提取或提取不足。你不用在不同的技术栈之间跳来跳去，订阅和查询的形状是一样的(基于 JSON)，变异也是一样的形状，除非你把它表示为“变异”,你明确地声明查询可能有副作用。它更容易被人类和机器阅读。

REST 缺乏适当的标准，所以没有行业标准受到青睐，有太多的味道，太多的签名和文档很快就过时了。使用 GraphQL，您可以从致力于采用标准方法并在整个行业推广的行业老手那里学习最佳实践，标准允许通用工具，因为系统共享一个通用接口。

像 REST 的 [Swagger](https://swagger.io/) 这样的工具也提供了类似的 API 内省，但是使用 GraphQL，这个过程是自动的。工具 [Swagger-to-GraphQL](https://github.com/yarax/swagger-to-graphql) 将您现有的 Swagger 模式转换为可执行的 GraphQL 模式。

这些工具的结合使您能够构建强大而实用的编程接口，供所有 GraphQL 消费者使用。

TypeScript 扩展了这一概念，从 GraphQL API 边界开始进行类型检查。当一个端点是完全类型化的时，误用它要困难得多。它鼓励您对数据进行更好的抽象，并适当地设计您的 UI 来容纳这些数据。

## **培养对类型安全的理解，以及这如何传达程序员的真实意图**

类型注释给你一种程序员意图的更强烈的感觉。对于任何考虑使用 TypeScript+GraphQL 的人来说，拥有端到端打字的机会应该是一个巨大的激励因素。

在现代的 web 开发栈中，可以在多个层次上体验到打字的力量。类型使您的 IDE 更加智能，在开发过程中建议正确的类型。它可以极大地增强您的开发人员体验。

如果你使用的是像 [VS Code](https://code.visualstudio.com/) 这样的现代 IDE，TypeScript 在你开发的时候有很强的指导方针和类型化的保护。类型错误是 JavaScript 中错误的一个巨大来源，正确地输入有助于提高代码质量。

在开发过程中识别 bug 比让客户在生产中发现它要好得多。

语言级别的 TypeScript 与 API 边界的 GraphQL 相结合，提供了高级别的代码自省和安全性。类型定义使你成为更好的开发者。

代码生成提供了蛋糕上的樱桃。有不同的包可用，但大多数都为您的客户端提供类型生成，这意味着您可以从您的 GraphQL API 为您的客户端代码生成类型定义。

**由于您正在创建类型化数据图，因此可以免费生成 TypeScript 类型。**

你甚至可以生成像 React 组件和 [GraphQL 客户端钩子](https://medium.com/the-guild/graphql-code-generator-introducing-hooks-support-for-react-apollo-2cdc8a7b526d)这样的东西！

> 打字稿提高生产力，减少疯狂，它的价值随着时间的推移而增加。

## **使用集成开发环境(代码完成、智能感知)分析您的代码库**

内在 API 自省非常重要，因为您可以有效地免费获得[一些]文档和运行时类型检查。犯错误要难得多。通常只需一个`ctrl-space`键序列就可以自动完成，像字段弃用这样的事情会实时呈现。大多数服务器都提供集成的 API 探索者，如 [GraphQL Playground](https://github.com/prisma-labs/graphql-playground) 或[graph QL](https://github.com/graphql/graphiql/blob/master/packages/graphiql/README.md)。

VS 代码的特点是智能感知，它是一个代码完成辅助工具。它帮助您了解更多关于您正在使用的代码的信息。它会在您键入时跟踪参数，并且只需几次击键就可以添加对属性和方法的调用。IntelliSense 随着更新变得更加智能，当您提供良好的类型定义时，它会更加智能。GraphQL 有工具可以自动生成类型定义。

## 自动生成代码

您应该使用代码生成器从 GraphQL API 自动生成您的类型，但是您也可以生成其他东西，比如 React 和 Apollo 的[钩子](https://medium.com/the-guild/graphql-code-generator-introducing-hooks-support-for-react-apollo-2cdc8a7b526d)。您甚至可以编写自己的插件来生成自己的代码，不要忘记您可以访问静态类型定义和模式自省。

有不同的生成器可以尝试:我推荐 [GraphQL 代码生成器](https://graphql-code-generator.com/)，但也有 [TypeGraphQL](https://typegraphql.ml/) ，Apollo tools 提供 [Apollo code-gen](https://github.com/apollographql/apollo-tooling) 。

想象一下代码生成的未来。不难想象作为模板生成的集成组件，但是整个客户端应用程序呢？有了机器学习，这似乎很有可能。

## **利用编译时安全和静态类型尽早识别 bug**

如果你改变了一些东西，你会立即看到错误和警告，任何有问题的地方都会被突出显示。你得到了跨特性的契约验证，这产生了非常干净的代码，因为基本的错误很难犯，并且破坏性的改变会立即显现。

## 验证并遵守跨功能/团队的合同

考虑代码生成如何从 GraphQL API 创建类型定义，以及 GraphQL 如何将不同的数据源整合到一个数据图中。微服务更容易管理。

一些框架有“模式拼接”的工作实现，也称为“联邦”(Apollo)或“远程模式”(Hasura)。在扩展和订阅等方面还有一些工作要做，但是这些工具有效地将来自不同筒仓的多个图合并成一个简洁的数据图。

有了 TypeScript 类型定义和 GraphQL APIs，就有可能实现跨特性的端到端契约验证。这是非常强大的，如果一个团队破坏了一个集成，这个错误将在编译时被检测到，并且您的持续集成环境(CI)可以被配置为跨远程模式进行编译时检查。

这是一个令人兴奋的概念，因为你可以想象这将如何在整个网络中发展。考虑由不同小组开发的 ES 模块。

## **在新特性对 JavaScript 可用之前访问它们**

TypeScript 支持 ECMAScript 2015 中的所有功能，并添加了一组附加功能:

*   [类型注释和编译时类型检查。](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html#type-annotations)
*   [式推论。](https://www.typescriptlang.org/docs/handbook/type-inference.html)
*   [类型擦除。](https://github.com/microsoft/TypeScript/wiki/FAQ#what-is-type-erasure)
*   [接口。](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html#interfaces)
*   [枚举类型。](https://www.typescriptlang.org/docs/handbook/enums.html)
*   [仿制药。](https://www.typescriptlang.org/docs/handbook/generics.html)
*   [名称空间。](https://www.typescriptlang.org/docs/handbook/namespaces.html)
*   [元组。](https://www.typescriptlang.org/docs/handbook/basic-types.html#tuple)

除了这些特殊的 TypeScript 特性之外，还有一些未来的 ECMAScript 特性，这些特性通常只有在您使用类似于 [Babel plugins](https://babeljs.io/docs/en/plugins/) 的东西时才可用。TypeScript 包括流行的 ES next 特性，如:

*   [可选链接](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#optional-chaining)
*   [无效合并](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#nullish-coalescing)
*   [断言功能](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#assertion-functions)

不要忘记 TypeScript 可以编译成常规的 ECMAScript，所以这些特性应该可以与当今的 JavaScript 容器(browser、Node.js 等)一起工作。

## **了解代码变更的影响**

模式更改验证可以作为构建过程的一部分来实现。 [Apollo Graph Manager](https://www.apollographql.com/docs/graph-manager/integrations/) 甚至可以针对客户端验证服务器模式的变化。有一个 GitHub 集成，您可以在其中监控客户端请求，如果有客户端请求数据图上的不推荐使用的字段，您可以跟踪它们，或者在它们不再使用时删除它们。

TypeScript 几乎可以立即测量影响，如果有错误，它甚至不会传输。如果您的常规 CI 工作流只是运行测试或构建，TypeScript 将进行编译时检查。如果你对你的 API 运行 code-gen，这将标记一个问题，即使它在组织的另一边。

## **将原则性决策应用于标准化应用开发**

这是主观的，但有一个观点通常是一件好事。用[第一原理](https://fs.blog/2018/04/first-principles/)来接近 GraphQL 是一个**错误**。您应该尽可能遵循最佳实践，并积极寻求意见和固执己见的软件。如果需要扩展，可以跳到现有的开源实现上。

[Principled GraphQL](https://principledgraphql.com/) ，由阿波罗团队的 [Geoff Schmidt](https://twitter.com/GeoffQL) 和 [Matt DeBergalis](https://twitter.com/debergalis) 撰写，是一个受[十二因素应用](https://12factor.net/config)启发的指南(另一个你应该阅读的伟大的最佳实践指南)。这是一个很好的起点，有助于理解如何利用现场经验开发 GraphQL 应用程序。

固执己见的软件最酷的一点是，你不必浪费时间来阐述你自己的观点并与你的同事争论，通常你的问题的解决方案已经被考虑了。

T21 是我最喜欢的开源项目之一，它是固执己见的软件的一个极好的例子，它旨在通过接受一个共同的风格指南来停止所有正在进行的关于代码格式的争论。

## **用通用工具开发应用**

GraphQL + TypeScript 提供了对整个代码库的自省，GraphQL 自省模式可用于驱动许多应用程序。

有一些完整的框架是围绕 GraphQL 内省构建的。考虑像 [Apollo Client](https://github.com/apollographql/apollo-client) 这样的工具，它在浏览器中构建了一个全面的规范化数据缓存，可以直接查询或者合并到一个[本地链接状态](https://www.apollographql.com/docs/react/data/local-state/)中进行本地状态管理。

还有像 [GraphQL Playground](https://github.com/prisma-labs/graphql-playground) 或者[graph QL](https://github.com/graphql/graphiql/blob/master/packages/graphiql/README.md)这样的 GraphQL API 探索者。有像 [GraphQL Voyager](https://github.com/APIs-guru/graphql-voyager) 这样的图形可视化工具，像 [GraphQL Editor](https://graphqleditor.com/) 这样的设计工具，像 [GraphCMS](https://graphcms.com/) 这样的无头内容管理工具。每天都有新工具发布[。](https://github.com/search?q=graphql)

> 在开发过程中识别 bug 比让客户在生产中发现它要好得多。

## 获得在没有版本控制的情况下发展 API 的能力

过量提取是指提取的数据多于所需的数据。REST 端点通常会提供所有内容，并允许客户端进行精选。而提取不足则相反，当在提取时没有传递足够的数据时，客户端必须从其他地方提取更多的数据。

假设您必须添加一个新字段或重命名一个旧字段，如果您想要创建更多优化的端点或更改一个端点，REST 通常会采用版本化端点`/v1/` `/v2/`等。您可能有尚未更新的移动应用程序或其他客户端，并且您无法更新它们的代码。

GraphQL 使 API 的发展变得容易，而不必求助于端点版本控制，您可以标记字段以避免使用。

TypeScript 使 API 更新更干净，您可以在编译时看到错误，并且接口定义了您正在处理的对象的预期形状。

## 获取关于您的信息体系结构和业务决策的知识

“真理的单一来源”是信息系统理论中的一个术语，在讨论 GraphQL 时你会经常听到。这是一个组织可以作为其信息架构的一部分来应用的概念，以确保组织中的每个人在做出业务决策时都使用相同的数据。

GraphQL 提供的数据图是现代应用程序开发堆栈中的一个新层。它对您的整个组织都很有用，经理和开发人员都可以使用它，它对领域建模很有用，并且为跨团队功能提供了准确的自我记录的数据表示。

GraphQL 减少了摩擦，改善了沟通。它促进了单个数据图，而不是在整个公司中有不同的数据仓库。如果您在许多地方有数据，您必须同步和聚合，这会导致复杂性，从而导致代价高昂的错误。这也使得决定什么/谁是数据的权威来源变得具有挑战性。

## 了解 API 开发的不同方法

来自 Apollo GraphQL 文档:

> 因为模式直接位于应用程序客户机和底层数据服务之间，前端和后端团队应该在它的结构上协作。当你开发你自己的数据图时，练习 ***模式优先开发*** *并在你开始实现你的 API 之前就一个模式达成一致。*

**模式优先**是一种给予模式设计最高优先权的方法。它很有用，因为它解除了前端和后端团队的阻塞。在前端团队开始开发客户端之前，API 不一定要工作，反之亦然。如果存在模式，API 数据可能会被模仿。

**代码优先**(又名*解析器优先*)是另一种流行的方法。这是以编程方式生成模式的地方*。关于正确的方法存在一些争论，双方都有不同的倡导者。这可能非常令人困惑，这是你欢迎强烈意见的事情之一。*

*如果您使用 TypeScript，那么选择模式优先的**会有一些特殊的挑战。***

*用 TypeScript 在 Node.js 中构建 GraphQL API 可能很困难，因为您必须用 [SDL](https://graphql.org/learn/schema/) (模式定义语言)创建您的模式类型以及 ORM 的数据模型(即 TypeORM 中的类)，然后为您的查询/变异/订阅编写解析器。这里的问题是，在您可以实现解析器之前，您被迫创建您的 TypeScript 接口，因为否则，您会得到类型错误，这不仅仅是存根您的方法。*

*还有一个相当严重的冗余和管理问题。仅仅添加一个新的字段，您就会发现自己正在更新许多文件，您必须修改模型、模式、解析器、类型定义和单元测试接口，然后才能更新您的客户端接口。它很难管理并且容易出错。如果你能建立一个单一的**代码优先**的事实来源，你就有可能消除这个问题。*

# *结论*

*我从使用 TypeScript 和 GraphQL 中学到了很多。我的经历中最值得注意的一件事是，我学到了可以应用于任何应用程序开发堆栈的概念。我学到的很多东西可以很容易地应用于 REST 和常规的 JavaScript 应用程序。*

*它让我懂得了数据类型的价值，并教会了我要小心过度获取和不足获取。它给了我关于如何进行 API 设计的好建议。我已经了解了依赖注入以及它是如何影响自动化测试的。我学到了自省和自我记录代码的重要性。*

*我坚信这种堆栈会越来越受欢迎，尤其是在企业领域。缺点是(像往常一样)，不得不学习新的东西。TypeScript 增加了一点代码，我记得 JavaScript 开发人员会嘲笑 Java 和 C++开发人员巨大的函数签名、访问修饰符等等。随着对可伸缩和可维护的 web 软件需求的增长，TypeScript 开始变得相似，这是可以理解的。打字稿提高生产力，减少疯狂，它的价值随着时间的推移而增加。*

*未来是令人兴奋的，在许多方面，这只是第一步。*

*我强烈建议您学习 TypeScript 和 GraphQL，尤其是如果您正在开发生产 API 的话。祝你努力成功！*

# *资源*

*[阿波罗客户端](https://github.com/apollographql/apollo-client) / [阿波罗图形管理器](https://www.apollographql.com/docs/graph-manager/integrations/) /*

*[原理图 QL](https://principledgraphql.com/) / [十二要素 App](https://12factor.net/config)*

*[GraphQL 游乐场](https://github.com/prisma-labs/graphql-playground)/[graph QL](https://github.com/graphql/graphiql/blob/master/packages/graphiql/README.md)/[graph QL 航海家](https://github.com/APIs-guru/graphql-voyager)*

*[GraphQL 编辑器](https://graphqleditor.com/) / [GraphCMS](https://graphcms.com/)*

*[GraphQL 代码生成器](https://graphql-code-generator.com/)/[type graph QL](https://typegraphql.ml/)/[Apollo Code-gen](https://github.com/apollographql/apollo-tooling)。*

*[Swagger](https://swagger.io/)/[Swagger-to-graph QL](https://github.com/yarax/swagger-to-graphql)*