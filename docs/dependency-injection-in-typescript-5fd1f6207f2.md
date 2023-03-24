# TypeScript 中的依赖注入

> 原文：<https://levelup.gitconnected.com/dependency-injection-in-typescript-5fd1f6207f2>

![](img/8fca73d9401a271912b7461ddf00422b.png)

# 动机

在过去几年中，Node 成为最受欢迎的后端解决方案之一。在 Node 上启动一个应用程序并开始动态处理 HTTP 请求是非常容易的。但有一个问题，在大多数情况下，节点应用程序在增长时会变得非常复杂和耦合，因此保持域和持久层分离变得非常困难。

为了解决这个问题，我们应该深入到[固体](https://www.digitalocean.com/community/conceptual_articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)架构的世界中去(这是今天的一部分)。为了根据[坚实的](https://www.digitalocean.com/community/conceptual_articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)准则编写代码，我们需要[依赖注入](https://www.martinfowler.com/articles/injection.html)。它允许我们将类的创建从它们的实现中抽象出来，这将域/业务逻辑从所有其他层中分离出来，并使代码更容易维护。

对于节点中的依赖注入，没有现成的解决方案，但是我们可以在 TypeScript decorators 的帮助下实现我们自己的 DI 服务。

让我们开始吧。

# 履行

由于 decorators 仍处于实验模式，我们需要在 ***tsconfig.json*** 中启用它们

```
{ "compilerOptions": { "target": "ES5", "experimentalDecorators": true, "emitDecoratorMetadata": true, "outDir": "dist" }}
```

假设我们有一个后端应用程序，非常需要 Logger。Logger 可以有多种实现，有些可以直接在控制台中记录消息，有些可以将消息存储在数据库中，还有一些可以同时实现这两种功能。

让我们定义通用记录器抽象类，它将由实际的记录器实现

这里没有什么特别的，只是一个具有单一`log`方法的抽象类。现在让我们看看可能的日志记录器实现。

对于每个依赖项，我们需要一个惟一的字符串标识符，它将被映射到实际的类实例

现在我们需要某种管理器类，它将存储所有初始化的类，并在依赖类需要时注入它们。

我们有希望注入的日志类和存储所有需要的映射的 DependencyManager。唯一剩下的是`***@Inject***` decorator(这里阅读更多[关于 TypeScript decorators 的](https://www.typescriptlang.org/docs/handbook/decorators.html)，它将在我们的依赖管理器的帮助下负责注入类的实际实例

此时，我们已经准备好使用我们的注入装饰器，并看到我们的代码在运行

```
Output:
LogServiceA -> test
LogServiceB -> test
```

我们还可以用`@Injectable` decorator 轻松地定义可注入类，因为我们已经实现了`@Injectable`,我们只需要在类声明的顶部添加一个具有唯一 id 的调用

```
Output:
LogServiceC -> test
```

如你所见，注射剂也可以作为注射剂使用。

# 摘要

这是对 Node 中依赖注入的一个小介绍。依赖注入确实比这更深入，比如直接注入到构造函数中，用符号代替令牌(类似于 Angular 是怎么做的)。然而，我希望这已经让您对依赖注入在幕后是如何工作的有了一点了解。当我第一次开始编写代码时，依赖注入是一个黑盒，我知道它非常有用，但不知道它是如何工作的，我希望在这篇文章之后，还有一个关于软件工程的神奇部分，你不再被蒙在鼓里。

此处代码可用[。](https://github.com/dudupopkhadze/ts-dependency-injection)

## 黑客快乐！