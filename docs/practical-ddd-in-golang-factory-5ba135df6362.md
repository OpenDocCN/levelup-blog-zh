# 戈朗的实用 DDD:工厂

> 原文：<https://levelup.gitconnected.com/practical-ddd-in-golang-factory-5ba135df6362>

## 领域驱动设计

## 关于 DDD 的故事继续呈现一个传奇模式——工厂。

![](img/6e45f202cc6112d87258bea25a1931e2.png)

奥马尔·弗洛雷斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

当我写这篇文章的标题时，我试图回忆我从四人组[那里学到的第一个设计模式。我认为是以下之一:](https://springframework.guru/gang-of-four-design-patterns/)[工厂方法](https://refactoring.guru/design-patterns/factory-method)、[单件](https://refactoring.guru/design-patterns/singleton)或[装饰工](https://refactoring.guru/design-patterns/decorator)。

我相信其他软件工程师也有类似的经历。当他们开始学习设计模式时，工厂方法或抽象工厂是他们首先接触的三种方法之一。

今天，工厂模式的任何衍生物在领域驱动设计中都是必不可少的。而且，即使过了几十年，它的目的还是一样的。

```
Other articles from DDD series:**1\.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Domain Service**](/practical-ddd-in-golang-domain-service-4418a1650274)**4\.** [**Practical DDD in Golang: Domain Event**](/practical-ddd-in-golang-domain-event-de02ad492989)**5\.** [**Practical DDD in Golang: Module**](/practical-ddd-in-golang-module-51edf4c319ec)**6\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)
```

[](https://blog.ompluscator.com/membership) [## 通过我的推荐链接加入媒体——马尔科·米洛耶维奇

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.ompluscator.com](https://blog.ompluscator.com/membership) 

# 复杂的创作

我们将工厂模式用于任何复杂的创建，或者将创建过程与其他业务逻辑隔离开来。在这种情况下，最好在代码中有一个专门的位置，我们可以单独测试。

当我提供工厂时，在大多数情况下，它是领域层的一部分。从那里，我可以在应用程序的任何地方使用它。下面你可以看到一个简单的工厂的例子。

工厂模式的例子

工厂模式与规范模式密切相关(我将在下面的文章中介绍)。这里我们有一个关于`LoanFactory`、`LoanSpecification`和`Loan`的小例子。

`LoanFactory`代表 DDD 的工厂模式，更确切地说，代表工厂方法。它负责创建和返回新的`Loan`实例，这些实例会随着支付期的不同而变化。

# 变化

如前所述，我们可以用许多不同的方式来表示工厂模式。最常见的形式，至少对我来说，是工厂方法。在这种情况下，我们为我们的工厂结构提供了一些创建方法。

工厂方法示例

在上面的代码片段中，其中`LoanFactory`现在是工厂方法的具体实现。它提供了两种方法来创建`Loan`实体的实例。

在这种情况下，我们创建相同的对象，但它可以有不同的，这取决于`Loan`是长期的还是短期的。这两种情况之间的差异可能更复杂，每增加一个复杂性都是这种模式存在的新原因。

抽象工厂的例子

在上面的例子中，有一个抽象工厂模式的代码片段。在这种情况下，我们想要创建一些`Investment`接口的实例。

由于该接口有多种实现，这看起来是添加工厂模式的最佳时机。`EtfInvestmentFactory`和`StockInvestmentFactory`都创建了投资接口的实例。

在我们的代码中，我们可以将它们保存在一些`InvestmentFactory`接口的映射中，并在我们想要从任何`BankAccount`创建`Investment`时使用它们。

这里是使用抽象工厂的一个很好的地方，因为我们应该从它们的广阔空间中创建一些对象(甚至还有更多不同的投资)。

# 重建

我们可以在其他层上使用工厂模式。至少对我来说，场所是基础设施，是表示层。在那里，我用它将数据传输对象转换成实体，反之亦然。

重建的一个例子

`CryptoInvestmentDBFactory`是基础设施层内部的工厂，用于重建 CryptoInvestment 实体。这里，只有一个将 DTO 转换为实体的方法，但是同一个工厂可以有一个将实体转换为 DTO 的方法。

由于`CryptoInvestmentDBFactory`使用来自基础设施(`CryptoInvestmentGorm`)和领域(`CryptoInvestment`)的结构，它必须在基础设施层内部，因为我们不能依赖于领域层内部的其他层。

我总是喜欢在业务逻辑中使用 UUID，并在 API 响应中暴露唯一的 UUID。但是，由于数据库不喜欢将字符串或二进制文件作为主键，工厂看起来是进行这种转换的合适地方。

# 结论

工厂模式是一个源于“四人帮”旧模式的概念。我们可以将其实现为抽象工厂或工厂方法。

当我们希望将创建逻辑从其他业务逻辑中分离出来时，我们会使用它。我们还可以用它将我们的实体转换成 dto，反之亦然。

```
Other articles from DDD series:**1\.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Domain Service**](/practical-ddd-in-golang-domain-service-4418a1650274)**4\.** [**Practical DDD in Golang: Domain Event**](/practical-ddd-in-golang-domain-event-de02ad492989)**5\.** [**Practical DDD in Golang: Module**](/practical-ddd-in-golang-module-51edf4c319ec)**6\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)
```

# 有用的资源:

*   [https://martinfowler.com/](https://martinfowler.com/)
*   https://www.domainlanguage.com/