# 使用工厂模式开始使用 PHP 设计模式

> 原文：<https://levelup.gitconnected.com/getting-started-with-design-patterns-in-php-4d451ccdfb71>

![](img/3c26d2f2ecd7c69069a64a5a5c8e1107.png)

来源:自有

通常，当有人在谈论设计模式时，我们会想到四人帮的一本名著中所涉及的一组模式。它由 23 个经过战斗考验、经过深思熟虑和描述的解决方案组成。这是一个完美的起点，但绝对不够。每种语言和用例都有其特殊性，这使得使用某些方法更可取，而有些则不太可取。设计模式也不是应该严格应用的封闭解决方案集，而是在给定情况下可能有所帮助的最佳实践和规则。

如果你要去参加一个初级以上软件开发职位的面试，一个关于设计模式知识的问题几乎是肯定的。我已经数不清有多少次我遇到过一个面带奸笑地问这个问题的招聘人员。这个问题常见的回答是:“当然，MVC”。不对！这是一种架构模式，而不是设计模式。如果你不能证明你理解什么是设计模式以及如何使用它们，你得到这份工作的机会就越来越小。这真的不难，而且随着时间的推移会变得非常有用。

软件开发人员遇到的许多问题已经被别人检查和解决了。你可以试着自己解决这些问题，你可能会成功。但是，您也可能会严重失败，损失大量时间，或者有一个错误的实现，这将对未来产生影响。设计模式帮助你从别人的错误和成功中学习，而不是从你自己的错误和成功中学习。此外，您可以将这些实践推广到架构概念。设计模式提出了一些关于类和对象组织的策略，但是类似的概念可以在处理模块、服务或组件时普遍使用。

设计模式是一个非常广泛的话题，在一篇文章中肯定涵盖不了太多。但是你知道吃大象吗？一次一片。所以我们先从一些基础和常用的例子开始。

# 工厂方法

工厂方法(也常被称为工厂)帮助你创造某个家族的产品，而不用担心建造它的过程。新对象的整个创建都被转移到工厂中的委托方法中。这种方法完全符合**开合原理**。通过添加新类型的元素来扩展功能是很容易的，并且您不必在现有的元素中做任何更改。

在这个例子中，我们有一组不同的元素为提供的文件类型生成 HTML 表示。根据文件的类型(图像、视频或音频),应该使用不同的类。对于一家提供这种方法的工厂来说，这是一项完美的工作。

每个 box 都必须扩展抽象类 Box 并实现一个生成一些 HTML 代码的方法:

…并将 FileItem 的一个实例作为参数。

在示例实现中，我们有三种可接受的文件类型:图像、视频和音频。每一个都由一个特定的盒子代表。

或视频:

或音频:

我们已经做了一些很好的设置，现在的问题是如何动态地生成每个文件类型表示，以及在哪里放置关于何时应该生成每个表示的逻辑。这个问题可能有很多解决方案，工厂推荐的是**使用一个单独的方法专门负责这个**。基本上这个名字就是这么来的。

让我们对要做的事情有一个最小的界面。

通过简单的实施，我们可以让一切正常运行。

如果我们想增加一个新类型的盒子，所有要做的就是用一个新类型的文件来扩展你的工厂。

总而言之，工厂方法由以下元素组成:

1.  产品(`Box` ) -为工厂创建的元素定义接口
2.  混凝土产品(`ImgBox`、`VideoBox`、`AudioBox` ) -产品实施
3.  创建者(`Factory` )-定义一个用于创建产品类型对象的接口
4.  混凝土创造者(`BoxFactory` )-生产混凝土产品的创造者的实现

**你应该在什么时候想你的工厂:**

*   根据外部提供的规则构建一个给定系列的复杂对象
*   将创建新对象的过程与其实现分开

完整的源代码和其他一些模式可以在这里找到:

[](https://github.com/jkapuscik2/design-patterns-php) [## jkapuscik 2/设计-模式-php

### PHP 中设计模式的例子。通过在…上创建帐户，为 jkapuscik2/design-patterns-php 开发做出贡献

github.com](https://github.com/jkapuscik2/design-patterns-php) 

如果您想了解一些更有用的设计模式，您可以在以下文章中找到它们:

*   [创作模式](https://medium.com/@j.kapuscik2/creational-design-patterns-in-php-db365d3245ce)
*   [观察者](https://medium.com/@j.kapuscik2/observer-pattern-in-php-2ba240f89fb2)
*   [迭代器](https://medium.com/@j.kapuscik2/iterator-pattern-in-php-b7624f6bdbcf)
*   [状态&策略](https://medium.com/@j.kapuscik2/state-strategy-design-patterns-by-example-f57ebd7b6211)
*   [模板法](https://medium.com/@j.kapuscik2/template-method-pattern-in-php-6116fd7e8ccc?source=friends_link&sk=ac4c483446bd5a5323c09a662bd54116)
*   [飞锤](https://medium.com/swlh/flyweight-design-pattern-in-php-edcda0486fb0?source=friends_link&sk=a0fa3083d5afd7e41af8a4f7a1df05f1)
*   [代理](https://medium.com/better-programming/proxy-design-pattern-and-how-to-use-it-acd0f11e5330)
*   [装潢师](https://medium.com/better-programming/decorator-c04fae63dfff)
*   [依赖注入](https://medium.com/better-programming/dependency-injection-8f09a93ec995)
*   [复合](https://medium.com/swlh/composite-908878748d0e)
*   [适配器](https://medium.com/swlh/building-cloud-storage-application-with-adapter-design-pattern-8b0105a1bda7)
*   [立面](https://medium.com/better-programming/what-is-facade-design-pattern-67cb09ce35d4)
*   [桥](https://medium.com/better-programming/what-is-bridge-design-pattern-89bfa581fbd3)
*   [责任链](https://medium.com/@j.kapuscik2/what-is-chain-of-responsibility-design-pattern-ff4d22abd124)
*   [访客](https://medium.com/@j.kapuscik2/what-is-visitor-design-pattern-8451fb75876)
*   [命令](https://medium.com/@j.kapuscik2/what-is-cqrs-command-design-pattern-5d400fd9f93a)
*   [空对象](https://medium.com/@j.kapuscik2/what-is-null-object-design-pattern-f3b4d3d28636)
*   [流畅的界面](https://medium.com/@j.kapuscik2/what-is-the-fluent-interface-design-pattern-2797645b2a2e)
*   [规格](https://medium.com/@j.kapuscik2/what-is-the-specification-design-pattern-4051dd9e71c3)