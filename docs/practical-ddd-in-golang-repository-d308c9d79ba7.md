# 戈朗实用 DDD:知识库

> 原文：<https://levelup.gitconnected.com/practical-ddd-in-golang-repository-d308c9d79ba7>

## 领域驱动设计

## 使用著名的 DDD 模式——存储库实现反腐败层。

![](img/daf323f008b474cf03cba804f9d36e55.png)

乔治·科登伯格三世在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

今天，很难想象在运行时不访问一些存储就能编写一些应用程序。甚至可能不需要编写部署脚本，因为它们需要访问配置文件，从某种意义上说，这些文件仍然是存储类型。

每当你编写一些应用程序来解决现实商业世界中的一些问题时，你都需要连接到数据库、外部 API、一些缓存系统等等。这是不可避免的。

从这个角度来看，有一个 DDD 模式可以满足这种需求就不足为奇了。当然，DDD 并没有在其他文献中发明 Repository 及其许多设备，但是 DDD 增加了更多的清晰度。

```
Other articles from DDD series:**1\.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Domain Service**](/practical-ddd-in-golang-domain-service-4418a1650274)**4\.** [**Practical DDD in Golang: Domain Event**](/practical-ddd-in-golang-domain-event-de02ad492989)**5\.** [**Practical DDD in Golang: Module**](/practical-ddd-in-golang-module-51edf4c319ec)**6\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)**7\.** [**Practical DDD in Golang: Factory**](/practical-ddd-in-golang-factory-5ba135df6362)
```

[](https://blog.ompluscator.com/membership) [## 通过我的推荐链接加入媒体——马尔科·米洛耶维奇

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.ompluscator.com](https://blog.ompluscator.com/membership) 

# 反腐败层

领域驱动设计是一个我们可以应用到软件开发的许多方面和许多地方的原则。尽管如此，主要的焦点还是在领域层，我们的业务逻辑应该在那里。

由于存储库总是代表一种结构，这种结构保存了与一些外部世界连接的技术细节，它已经不属于我们的业务逻辑。

但是，有时，我们需要从领域层内部访问存储库。由于领域层位于底层，不与其他层通信，因此我们将存储库定义在其中，而是作为一个接口。

合同示例

这个接口我们称之为契约，它定义了我们可以在域内调用的方法签名。在上面的例子中，我们可以找到这样一个简单的接口，它定义了 [CRUD](https://www.codecademy.com/articles/what-is-crud) 方法。

因为我们将存储库定义为这样的接口，所以我们可以在域层的任何地方使用它。它总是期望并返回我们的实体，在这种情况下，`Customer`和`Customers`(我喜欢在 Go 中定义这样的特定集合，以便为它们附加不同的方法)。

实体客户不持有任何关于以下存储类型的信息:没有定义 JSON 结构的 Go 标记、 [Gorm](https://gorm.io/index.html) 列或任何类似的东西。为此，我们必须使用基础设施层。

基础设施层内部的实际实现

在上面的例子中，你可以看到`CustomerRepository`实现的一个片段。在内部，它使用 Gorm 来简化集成，但是您也可以使用纯 SQL 查询。最近，我经常使用 T4 图书馆。

在这个例子中，你可以看到两个不同的结构，`Customer`和`CustomerGorm`。第一个是实体，我们希望在其中保存我们的业务逻辑、一些领域不变量和规则。它对底层数据库一无所知。

第二个结构是一个[数据传输对象](https://martinfowler.com/eaaCatalog/dataTransferObject.html)，它定义了我们的数据如何从存储器中传输出来以及如何传输到存储器中。这个结构除了将数据库的数据映射到我们的实体之外，没有任何其他职责。

> 这两种结构的划分是在我们的应用程序中使用存储库作为反腐败层的基本点。它确保表结构的技术细节不会污染我们的业务逻辑。

这里的后果是什么？首先，我们需要维护两种类型的结构，一种用于业务逻辑，一种用于存储，这是事实。此外，我还插入了第三个结构，我将它用作 API 的数据传输对象。

这种方法给我们的应用程序和许多映射函数带来了复杂性，就像你在下面的例子中看到的那样。而且，您希望适当地测试这些方法，以避免常见的复制粘贴错误。

数据传输对象的反腐败

尽管如此，除了整个维护，它还为我们的代码带来了新的价值。我们可以以最好地描述我们的业务逻辑的方式在域层中提供我们的实体。我们不会用我们使用的存储来限制它们。

我们可以在业务内部使用一种类型的标识符(比如 UUID)，在数据库中使用另一种类型的标识符(无符号整数)。这适用于我们希望用于数据库和业务逻辑的任何数据。

每当我们对这些层中的任何一层进行修改时，我们可能会在映射函数内部进行调整，而层的其余部分我们不会触及(或至少破坏)。

我们可以决定要切换到 MongoDB、Cassandra 或任何其他类型的存储。我们可以切换到外部 API，但仍然不会影响我们的领域层。

# 坚持

我们主要使用存储库进行查询。它与另一个 DDD 模式完美地配合，您可能会在示例中注意到这个规范。我们可以不加规范地使用它，但有时它会让我们的生活变得更轻松。

存储库的第二个特性是持久性。我们定义了将数据发送到下面的存储器中的逻辑，以便永久保存、更新甚至删除数据。

生成 UUID 的持久性

有时我们决定在应用程序中创建惟一的标识符。在这种情况下，存储库是合适的位置。在上面的例子中，您可以看到我们在创建数据库记录之前生成了一个新的 UUID。

如果我们想避免从数据库引擎自动递增，我们可以用整数做到这一点。在任何情况下，如果我们不希望依赖数据库键，我们应该在存储库中创建它们。

数据库事务

我们希望存储库用于的另一件事是事务。每当我们想要持久化一些数据并对相同的大量表执行许多查询时，这是定义事务的最佳时机，我们应该在存储库中交付事务。

在上面的例子中，我们正在检查`Person`或`Company`的唯一性。如果它们存在，我们返回一个错误。我们可以将所有这些定义为单个事务的一部分，如果那里出现问题，我们可以回滚它。

在这里，存储库是这种代码的理想场所。很好的一点是，我们还可以在将来使我们的插入更加简单，这样我们就完全不需要事务了。在这种情况下，我们不改变存储库的契约，而只改变里面的代码。

# 储存库的类型

认为我们应该只为数据库使用存储库是错误的。是的，我们最常将它用于数据库，因为它们是我们的首选存储，但今天其他类型的存储更受欢迎。

如前所述，我们可以使用 MongoDB 或 Cassandra。我们可以使用一个存储库来保存我们的缓存，在这种情况下，例如 Redis。它甚至可以是 REST API 或配置文件。

不同的存储

现在我们可以看到业务逻辑和技术细节分离的真正好处。我们为我们的存储库保留了相同的接口，所以我们的域层可以一直使用它。

但是，有一天，我们的应用程序可能会发展到 MySQL 不再是分布式应用程序的完美解决方案的地步。因此，在迁移的情况下，我们不需要担心我们的业务逻辑是否会受到影响，只要我们保持我们的接口不变。

> 因此，您的存储库契约应该总是处理您的业务逻辑，但是您的存储库实现必须使用内部结构，您可以在以后将其映射到实体。

# 结论

存储库是一种众所周知的模式，负责在底层存储中查询和保存数据。这是我们应用程序内部反腐败的要点。

我们将它定义为域层内部的一个契约，并将实际的实现放在基础设施层内部。它是生成应用程序制作的标识符和运行事务的地方。

```
Other articles from DDD series:**1\.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Domain Service**](/practical-ddd-in-golang-domain-service-4418a1650274)**4\.** [**Practical DDD in Golang: Domain Event**](/practical-ddd-in-golang-domain-event-de02ad492989)**5\.** [**Practical DDD in Golang: Module**](/practical-ddd-in-golang-module-51edf4c319ec)**6\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)**7\.** [**Practical DDD in Golang: Factory**](/practical-ddd-in-golang-factory-5ba135df6362)
```

# 有用的资源:

*   https://martinfowler.com/
*   [https://www.domainlanguage.com/](https://www.domainlanguage.com/)