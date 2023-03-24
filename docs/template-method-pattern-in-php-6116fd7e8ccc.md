# 什么是模板方法设计模式？

> 原文：<https://levelup.gitconnected.com/template-method-pattern-in-php-6116fd7e8ccc>

## 模板方法模式可以帮助你更好地组织代码

![](img/345d1812343ac774bc2e3d657cd2562a.png)

在模板方法中，我们在基类中定义算法的框架，并将具体步骤的实现留给子类。当我们有多个功能相似但实现细节不同的类时，这是非常有用的。

**好莱坞原则**说:“不要打电话给我们，我们会打电话给你”。如果你在为一部电影挑选演员，你就是那个等着之后电话的人。该模式中的方法是让父类使用在其子类中实现的方法。这种行为也被称为控制反转。

如果我们已经提到了好莱坞，那就让我们继续这个话题吧。我们将模拟制作不同类型的大片的过程。创作一部电影的过程很大程度上取决于它的类型。每部即将上映电影都需要:演员、导演、预算和一些宣传。

我们已经定义了一个包含几个步骤的方法来发布电影。它被标记为`final`，因为它不能被覆盖，并且它的行为对于子类来说是不可改变的。还定义了基本的提升过程，但是这种行为可能会被改变。每个子类被迫实现选择演员、电影导演和收集所需预算的方法。

如果我们在制作动作片，我们有一个特定的演员和导演团队，可以让它成功。我们必须筹集大量资金进行生产，并从中挑选一些。

和喜剧电影很像。有些演员几乎总是不得不在其中任何一部中扮演角色。

制作好莱坞电影比听起来容易。让我们看看实际应用程序。

模板方法模式只有两个元素:

*   抽象类(`Movie`)-基类，定义所用算法的框架，并在具体类上强制实现其元素(全部或部分)
*   具体类(`ActionMovie`，`ComedyMovie`)——实现抽象类中定义的算法步骤

这里提供了一些其他模式的完整源代码和描述:

[](https://github.com/jkapuscik2/design-patterns-php) [## jkapuscik 2/设计-模式-php

### 这个项目是一组在现实世界中使用不同设计模式的简单例子。每个人都有一个…

github.com](https://github.com/jkapuscik2/design-patterns-php) 

如果您想了解一些更有用的设计模式，您可以在以下文章中找到它们:

*   [工厂方法](https://medium.com/@j.kapuscik2/getting-started-with-design-patterns-in-php-4d451ccdfb71)
*   [创作模式](https://medium.com/@j.kapuscik2/creational-design-patterns-in-php-db365d3245ce)
*   [观察者](https://medium.com/@j.kapuscik2/observer-pattern-in-php-2ba240f89fb2)
*   [迭代器](https://medium.com/@j.kapuscik2/iterator-pattern-in-php-b7624f6bdbcf)
*   [状态&策略](https://medium.com/@j.kapuscik2/state-strategy-design-patterns-by-example-f57ebd7b6211)
*   [轻量级](https://medium.com/swlh/flyweight-design-pattern-in-php-edcda0486fb0?source=friends_link&sk=a0fa3083d5afd7e41af8a4f7a1df05f1)
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