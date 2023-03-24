# 戈朗实用固体:开/闭原理

> 原文：<https://levelup.gitconnected.com/practical-solid-in-golang-open-closed-principle-1dd361565452>

## 坚实的原则

## 我们将通过展示为应用程序提供灵活性的原则——开放/封闭原则，继续我们的坚实原则之旅。

![](img/4ace5df6c575fa8e088996fa274bf0b4.png)

由[泰克顿](https://unsplash.com/@tekton_tools?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

许多不同的方法和原则为我们的代码提供了长期的改进。他们中的一些人在软件开发社会中很有名，而一些人却不为人所知。

我的看法是，开/闭原理就是这样，它代表单词 *SOLID* 中的字母 *O* 。根据我的经验，只有渴望了解 SOLID 的人才能真正理解这个原则的含义。

我们有机会学习这个原则在[策略](https://refactoring.guru/design-patterns/strategy)模式中的一些应用，但没有意识到我们应用了它。尽管如此，战略模式只是 OCP 的一个应用。

在本文中，我们将试图揭示这一原则的全部目的。像往常一样，所有的例子都将在 Go。

```
Other articles from the SOLID series:**1\.** [**Practical SOLID in Golang: Single Responsability Principle**](/practical-solid-in-golang-single-responsibility-principle-20afb8643483)Some articles from the DDD series:**1.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)**4\.** [**Practical DDD in Golang: Repository**](/practical-ddd-in-golang-repository-d308c9d79ba7)**5\. ...**
```

[](https://blog.ompluscator.com/membership) [## 通过我的推荐链接加入媒体——马尔科·米洛耶维奇

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.ompluscator.com](https://blog.ompluscator.com/membership) 

# 当我们不遵守开/关原则时

> 您应该能够扩展系统的行为，而不必修改该系统。

对 OCP 的要求，我们可以在上面看到，[鲍勃大叔](https://twitter.com/unclebobmartin)在他的[博客](http://blog.cleancoder.com/uncle-bob/2014/05/12/TheOpenClosedPrinciple.html)中提供。我更喜欢这种定义开/闭原则的方式，因为它展示了它的全部光辉。

第一眼看完，我们可能会觉得这是一个荒谬的要求。是的，说真的，我们应该如何在不修改的情况下扩展某个东西？我是说，有没有可能在不改变的情况下改变一些东西？

通过检查下面的代码示例，我们可以看到一些结构不遵守这个原则意味着什么以及可能的后果。

该示例显示了一个结构`PermissionChecker`。它应该检查是否有访问某些资源所需的权限，这取决于 web 应用程序的`Context`，由包 [Gin](https://github.com/gin-gonic/gin) 支持。

这里我们有主方法`HasPermission`，它检查带有特定名称的权限是否与`Context`中的数据相关联。

根据用户是使用 JWT 令牌、基本授权还是应用程序密钥进行授权，从上下文中检索权限可能会有所不同。在该结构中，我们提供了权限片的所有不同提取。

如果我们尊重[单一责任原则](/practical-solid-in-golang-single-responsibility-principle-20afb8643483)，`PermissionChecker`负责决定权限是否在`Context`内部，与授权过程无关。

授权过程必须在其他地方定义，在其他结构中，甚至可能是不同的[模块](/practical-ddd-in-golang-module-51edf4c319ec)。因此，如果我们想在其他地方扩展授权过程，我们也需要在这里调整逻辑。

假设我们想要扩展授权逻辑并添加一些新的流程，比如将用户数据保存在会话中或者使用[摘要授权](https://httpwg.org/specs/rfc7616.html)。那样的话，我们也需要在`PermissionChecker`进行改编。

这种实现带来了问题的激增:

1.  `PermissionChecker`保持最初在其他地方处理的逻辑。
2.  授权逻辑的任何修改，可能是不同的模块，需要在`PermissionChecker`中进行修改。
3.  要添加一种提取权限的新方式，我们总是需要修改`PermissionChecker`。
4.  `PermissionChecker`内部的逻辑不可避免地随着每个新的授权流而增长。
5.  `PermissionChecker`的单元测试包含太多关于不同权限提取的技术细节。
6.  …

所以，现在，我们又有一些代码要重构。

# 我们如何尊重开放/封闭原则

> 打开/关闭原则[告诉](https://deviq.com/principles/open-closed-principle)软件结构应该**打开**进行扩展，而**关闭**进行修改。

上面的陈述为我们应该尊重 OCP 的新代码提供了一些可能的方向。这段代码应该提供一些允许从外部进行扩展的东西。

在面向对象编程中，我们通过对同一接口使用不同的实现来支持这种扩展。换句话说，我们使用[多态性](https://en.wikipedia.org/wiki/Polymorphism_(computer_science))。

在上面的例子中，我们可以看到一个尊重 OCP 的候选人。适配器`PermissionChecker`没有隐藏从`Context`中提取权限的技术细节。

相反，我们引入了一个新的界面，`PermissionProvider`。这个新的构造代表了放置不同权限提取的逻辑的位置。

比如可以是`JwtPermissionProvider`，或者`ApiKeyPermissionProvider`，或者`AuthBasicPermissionProvider`。现在，负责授权的模块可能还包含权限提取器。

这意味着我们可以将关于授权用户的逻辑放在一个地方，而不是分散到整个代码中。

另一方面，我们的主要目标，扩展`PermissionChecker`，不需要修改它，现在是可能的。我们可以用任意多的不同的`PermissionProviders`来初始化`PermissionChecker`。

假设我们需要添加从会话密钥获取权限的可能性。在这种情况下，我们需要实现一个新的`SessionPermissionProvider`，它将从上下文中提取 cookie，并使用它从`SessionStore`中获取权限。

我们使得在需要时扩展 PermissionChecker 成为可能，而不再修改其内部逻辑。现在我们看到了什么是开放的扩展和封闭的修改。

# 更多的例子

前一个问题我们可以用稍微不同的方法来解决。让我们看看下面的代码片段:

在新的实现中，我们从`PermissionChecker`中移除了`PermissionProviders`的内部片。相反，我们将正确的提供者定义为方法`HasPermission`的参数。

我更喜欢第一种方法，但这也可能是一种解决方案，这取决于我们在应用程序中的用例。

我们可以将开放/封闭原则应用于方法，而不仅仅是结构。示例可能是下面的代码:

函数`GetCities`从某个来源读取城市列表。该来源可以是文件或互联网上的一些资源。尽管如此，我们可能希望在将来从内存、Redis 或任何其他来源读取数据。

因此，无论如何，让读取原始数据的过程更抽象一点会更好。也就是说，我们可以从外部提供一个阅读策略作为方法参数。

正如你在上面的解决方案中看到的，在 Go 中，我们可以定义一个嵌入函数的新类型。在这里，我们描述了一个新的类型，`DataReader`，表示从某个源读取原始数据的函数类型。

新方法`ReadFromFile`和`ReadFromLink`是`DataReader`类型的实际实现。`GetCities`方法期望将`DataReader`的实际实现作为参数，然后它在函数体内执行并获取原始数据。

正如你所看到的，OCP 的主要目的是给我们的代码和代码的用户更多的灵活性。只要有人能够扩展我们的库，而不分叉它们，不为它们提供拉请求，或者以任何方式修改它们，我们的库就有了真正的价值。

# 结论

开/关原理是第二个立体原理，代表字母 *O* 。它说我们应该总是扩展我们的代码结构，而不实际修改它们。

在它的源头，我们应该使用多态来满足这个需求。我们的代码应该提供一个简单的接口来增加这种可扩展性。