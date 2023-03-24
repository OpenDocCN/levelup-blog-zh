# 戈朗实用 DDD:规格

> 原文：<https://levelup.gitconnected.com/practical-ddd-in-golang-specification-6523d14438e6>

## 领域驱动设计

## 多实践模式的用例，用于验证、创建和查询-规范。

![](img/06c22c701fac3e9b1f5817c9bf4c70ad.png)

照片由[埃斯特万城堡](https://unsplash.com/@estebancastle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

没有那么多代码结构在我需要写的时候带给我欢乐。我第一次实现这样的代码是在 Go 中用一个轻量级的 ORM，当时我们还没有这样的。

另一方面，我使用 ORM 很多年了。在某些时候，当你依赖 ORM 时，使用 [QueryBuilder](https://www.doctrine-project.org/projects/doctrine-dbal/en/latest/reference/query-builder.html) 是不可避免的。在这里，您可能会注意到像**谓词**这样的术语。这就是我们可以找到规范模式的地方。

很难找到我们用作规范的任何模式，然而我们却听不到它的名字。我认为唯一困难的是不使用这种模式编写应用程序。

该规范有许多应用。我们可以用它来进行查询、创建或验证。我们可能会提供可以完成所有这些工作的独特代码，或者为不同的用例提供不同的实现。

```
Other articles from DDD series:**1\.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Domain Service**](/practical-ddd-in-golang-domain-service-4418a1650274)**4\.** [**Practical DDD in Golang: Domain Event**](/practical-ddd-in-golang-domain-event-de02ad492989)**5\.** [**Practical DDD in Golang: Module**](/practical-ddd-in-golang-module-51edf4c319ec)**6\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)**7\.** [**Practical DDD in Golang: Factory**](/practical-ddd-in-golang-factory-5ba135df6362)**8\.** [**Practical DDD in Golang: Repository**](/practical-ddd-in-golang-repository-d308c9d79ba7)
```

[](https://blog.ompluscator.com/membership) [## 通过我的推荐链接加入媒体——马尔科·米洛耶维奇

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.ompluscator.com](https://blog.ompluscator.com/membership) 

# 为了验证

规范模式的第一个用例是验证。我们主要验证[表单](https://angular.io/guide/form-validation)中的数据，但这是在表示级别上。有时，我们在创建过程中执行，比如对[值对象](/practical-ddd-in-golang-value-object-4fc97bcad70)。

在领域层的上下文中，我们可以使用规范来验证实体的状态，并从集合中筛选出实体。因此，域层上的验证已经比用户输入有了更广泛的含义。

使用规范来验证数据

在上例中，有一个接口`ProductSpecification`。它只定义了一个方法`IsValid`，该方法需要`Product`的实例，如果`Product`通过了验证规则，则返回一个布尔值。

这个接口的简单实现是验证最小数量`Product`的`HasAtLeast`。更有趣的验证器是两个函数，`IsPlastic`和`IsDeliverable`。

我们可以用一个特定的类型`FunctionSpecification`来包装这些函数。这种类型嵌入了与前面提到的两个签名相同的函数。除此之外，它还提供了尊重`ProductSpecification`接口的方法。

这个例子是 Go 的一个很好的特性，我们可以将一个函数定义为一个类型，并为它附加一个方法，这样它就可以隐式地实现一些接口。我们这里有这样一个例子，它提出了执行嵌入函数的方法`IsValid`。

此外，还有一个独特的规格，`AndSpecification`。这样一个结构帮助我们使用一个实现了`ProductSpecification`接口的对象，但是从所有包含的规范中分组验证。

附加规格

在上面的代码片段中，我们可以找到两个附加的规范。一个是`OrSpecification`，它和`AndSpecification`一样，执行它持有的所有规范。只是，在这种情况下，它使用的是`or`算法，而不是`and`。

最后一个是 NotSpecification，它否定了嵌入规范的结果。`NotSpecification`也可以是功能规范，但我不想把它弄得太复杂。

# 用于查询

我已经在本文中提到了作为 ORM 一部分的规范模式的应用。在许多情况下，您不需要实现这个用例的规范，至少如果您使用任何 ORM 的话。

我在脸书的 [Ent](https://entgo.io/) 库中找到了谓词形式的规范的优秀实现。从那时起，我没有用例来编写查询规范。

尽管如此，当您发现您对领域级存储库的查询过于复杂时，您需要更多的可能性来过滤所需的实体。实现可能如下例所示。

查询示例

在新的实现中，`ProductSpecification`接口提供了两个方法，`Query`和`Values`。我们使用它们来获取特定规范的查询字符串及其可能包含的值。

同样，我们可以看到额外的规格，`AndSpecification`和`OrSpecification`。在这种情况下，它们根据所提供的运算符连接所有底层查询，并合并所有值。

在领域层有这样的规范是有问题的。正如您可以从输出中看到的，Specifications 提供了 SQLike 语法，该语法过多地涉及了技术细节。

在这种情况下，解决方案可能是在领域层为不同的规范定义接口，在基础设施层定义实际的实现。

或者重新构造代码，以便规范保存有关字段名、操作和值的信息。然后在基础设施层上有一些映射器，可以将这样的规范映射到 SQL 查询。

# 为了创作

规范的一个简单用例是创建一个变化很大的复杂对象。在这种情况下，我们可以将它与工厂模式相结合，或者在域服务中使用它。

创作范例

在上面的例子中，我们可以找到规范的第三种实现。在这个场景中，`ProductSpecification`支持一个方法，`Create`，它期望`Product`，修改它，并返回它。

同样，有`AndSpecification`来应用从多个规范定义的变更，但是没有`OrSpecification`。在创建对象的过程中，我找不到`or`算法的实际用例。

即使它不存在，我们也可以引入`NotSpecification`，它可以处理特定的数据类型，比如布尔值。尽管如此，在这个小例子中，我找不到一个合适的例子。

# 结论

规格说明是一种模式，我们在许多不同的情况下到处使用。今天，如果不使用规范，在领域层提供验证是不容易的。

规范，我们也可以用它来查询底层存储中的对象。今天，它们是 ORM 的一部分。第三种用法是创建复杂的实例，我们可以将它与工厂模式结合起来。

```
Other articles from DDD series:**1\.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Domain Service**](/practical-ddd-in-golang-domain-service-4418a1650274)**4\.** [**Practical DDD in Golang: Domain Event**](/practical-ddd-in-golang-domain-event-de02ad492989)**5\.** [**Practical DDD in Golang: Module**](/practical-ddd-in-golang-module-51edf4c319ec)**6\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)**7\.** [**Practical DDD in Golang: Factory**](/practical-ddd-in-golang-factory-5ba135df6362)**8\.** [**Practical DDD in Golang: Repository**](/practical-ddd-in-golang-repository-d308c9d79ba7)
```

# 有用的资源:

*   [https://martinfowler.com/](https://martinfowler.com/)
*   [https://www.domainlanguage.com/](https://www.domainlanguage.com/)