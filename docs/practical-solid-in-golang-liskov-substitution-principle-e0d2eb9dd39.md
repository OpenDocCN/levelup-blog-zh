# 戈朗的实用固体:利斯科夫替代原理

> 原文：<https://levelup.gitconnected.com/practical-solid-in-golang-liskov-substitution-principle-e0d2eb9dd39>

## 坚实的原则

## 我们通过介绍一个具有最复杂定义的原理——利斯科夫替代原理，继续我们的坚实原理之旅。

![](img/cac1b4789fb798258e55251a6a2c7aa1.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我不是一个喜欢阅读的人。很多时候，当我阅读的时候，我意识到我在最后几分钟没有跟上文章的主题。很多时候，我从头到尾看了一整章，却不知道最后讲的是什么。

有时当我试图专注于内容时会感到沮丧，但很快我意识到我需要回去。这就是我开始寻找不同类型的媒体来了解这个话题的地方。

我第一次遇到固体原理的阅读问题是在利斯科夫替代原理上。这个定义对于我的测试来说太复杂了，至少在它的主格式中是这样。

我们可以猜测，LSP 代表单词 SOLID 中的字母 L。可以想象，这并不难理解(虽然有一些不那么数学化的定义就好了)。

```
Other articles from the SOLID series:**1\.** [**Practical SOLID in Golang: Single Responsability Principle**](/practical-solid-in-golang-single-responsibility-principle-20afb8643483)**2\.** [**Practical SOLID in Golang: Open/Closed Principle**](/practical-solid-in-golang-open-closed-principle-1dd361565452) Some articles from the DDD series:**1.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)**4\.** [**Practical DDD in Golang: Repository**](/practical-ddd-in-golang-repository-d308c9d79ba7)**5\. ...**
```

[](https://blog.ompluscator.com/membership) [## 通过我的推荐链接加入媒体——马尔科·米洛耶维奇

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.ompluscator.com](https://blog.ompluscator.com/membership) 

# 当我们不尊重里斯科夫替代

我们第一次听说这个原理是在 1988 年，作者是[芭芭拉·利斯科夫](https://en.wikipedia.org/wiki/Barbara_Liskov)。后来，[Bob](https://twitter.com/unclebobmartin)大叔在他的论文里给出了他对这个话题的[看法](https://web.archive.org/web/20151128004108/http://www.objectmentor.com/resources/articles/lsp.pdf)，后来作为坚实的原则之一。让我们看看上面写着什么:

> 设φ(x)是关于 t 类型的对象 x 的一个可证明的性质，那么φ(y)对于 S 类型的对象 y 应该是真的，其中 S 是 t 的子类型。

祝这个定义好运。

不，说真的，这是什么定义？在写这篇文章的时候，即使我从根本上理解了 LSP，我仍然不能理解这个定义。让我们再试一次:

> 如果 S 是 T 的子类型，那么程序中 T 类型的对象可以用 S 类型的对象替换，而不会改变该程序的任何期望的属性。

好了，现在好一点了。如果`ObjectA`是`ClassA`的一个实例，`ObjectB`是`ClassB`的一个实例，`ClassB`是`ClassA`的一个子类型——如果我们在代码的某个地方使用`ObjectB`而不是`ObjectA`，应用程序的功能一定不能被破坏。

我们在这里讨论类和继承，这是我们在 Go 中不认可的两个范例。不过，我们可以通过使用**接口**和**多态**来应用这个原则。

这里我们可以看到一个代码示例。老实说，我几乎找不到比这更糟糕、更愚蠢的了。就像,`Update`方法所说的，它不是更新数据库中的`User`,而是删除它。

但是，嘿，这才是重点。我们可以看到一个界面，`UserRepository`。在接口之后，我们有一个结构，`DBUserRepository`。尽管这个结构实现了初始接口，但它并没有做接口声称它应该做的事情。

它破坏了界面的功能，而不是遵循预期。这里是 Go 中 LSP 的要点:struct 一定不能违背接口的目的。

现在让我们来看看不那么荒谬的例子:

这里我们有了新的`UserRepository`接口及其两个实现:`DBUserRepository`和`MemoryUserRepository`。正如我们所见，`MemoryUserRepository`确实需要`Context`参数，但它仍然存在以尊重接口。

问题开始了。我们修改了`MemoryUserRepository`来支持这个接口，尽管这种意图是不自然的。因此，我们可以在应用程序中切换数据源，其中一个数据源不是永久存储。

[存储库](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)模式的目的是表示底层永久数据存储的接口，比如数据库。它不应该扮演缓存系统的角色，就像这里我们在内存中存储`Users`一样。

有时不自然的实现会对编码本身产生影响，不仅仅是语义上的。这些情况对于实现来说更加明显，也是最难解决的，因为它们需要大量的重构。

为了说明这种情况，我们可以查一下[关于几何形状的著名例子](http://stg-tud.github.io/sedc/Lecture/ws13-14/3.3-LSP.html#mode=document)。这个例子的有趣之处在于它与几何学中的事实相矛盾。

在上面的例子中，我们可以看到几何图形在 Go 中的实现。在几何上，我们可以用[分型](https://en.wikipedia.org/wiki/Quadrilateral#Convex_quadrilaterals)来比较凸四边形、矩形、长方形、正方形。

如果我们把它移到 Go 代码中来实现面积计算的逻辑，我们可能会得到与上面类似的结果。在顶部，我们有一个接口`ConvexQuadrilateral`。

这个接口只定义了一个方法`GetArea`。作为`ConvexQuadrilateral`的子类型，我们可以定义一个接口`Rectangle`。这个子类型有两个侧面涉及它的区域，所以我们必须提供`SetA`和`SetB`。

接下来是实际的实现。第一个是`Oblong`，可以有更宽的宽度，也可以有更宽的高度。在几何学中，它是任何不是正方形的矩形。实现这个结构的逻辑很容易。

`Rectangle`的第二个子类型是`Square`。在几何学中，正方形是矩形的子类型，但是如果我们在软件开发中遵循这一点，我们只能在我们的实现中产生问题。

正方形有四条相等的边。所以，这使得`SetB`过时了。为了尊重我们最初选择的子类型，我们意识到我们的代码有过时的方法。同样的问题是如果我们选择一条稍微不同的道路:

在上面的例子中，我们引入了`EquilateralRectangle`接口，而不是`Rectangle`。在几何学中，那应该是一个四边都相等的矩形。

在这种情况下，当我们的接口只定义了`SetA`方法时，我们在实现中避免了过时的代码。尽管如此，这破坏了 LSP，因为我们为`Oblong`引入了一个额外的方法`SetB`，没有它我们不能计算面积，即使我们的接口说我们可以。

所以，我们已经开始理解围棋里的里斯科夫替代原理了。因此，我们可以总结一下，如果我们打破它，可能会出现什么问题:

1.  它为实现提供了一个错误的捷径。
2.  它会导致过时的代码。
3.  它会破坏预期的代码执行。
4.  它会破坏期望的用例。
5.  它会导致不可维护的接口结构。
6.  …

所以，我们又来了，让我们做一些重构。

# 我们如何尊重利斯科夫替代

> 只有尊重接口的目的和方法，我们才能在直通接口中提供子类型。

我将避免为我们的第一个例子添加适当的实现，因为它非常清楚——`Update`方法应该更新`User`而不是删除它。

因此，让我们首先来解决`UserRepository`接口的不同实现的问题:

在这个例子中，我们将接口一分为二，有明确的目的和不同方法的签名。现在，我们有了`UserRepository`接口和`UserCache`接口。

`UserRepository`现在的目的肯定是将用户数据永久存储到某个存储中。为此，我们准备了类似`MySQLUserRepository`和`CassandraUserRepository`的具体实现。

另一方面，我们有了`UserCache`接口，我们清楚地知道我们需要它来将用户数据临时保存在某个缓存中。作为具体实现，我们可以使用`MemoryUserCache`。

现在我们可以切换到几何示例，这里的情况稍微复杂一些:

为了支持 Go 中几何图形的子类型化，我们应该考虑它们的所有特性，以避免不完整或过时的方法。

在这种情况下，我们引入了三种新的界面:`EquilateralQuadrilateral`(四条边都相等的四边形)`NonEquilateralQuadrilateral`(有两对等边的四边形)`NonEquiangularQuadrilateral`(有两对等角的四边形)。

这些接口中的每一个都提供了提供面积计算所需数据所需的附加方法。

现在，我们可以定义一个`Square`，仅使用`SetA`方法，`Oblong`同时使用`SetA`和`SetB`，而`Parallelogram`则全部加上`SetAngle`。因此，我们在这里没有使用子类型，而是更像特征。

对于这两个固定的例子，我们重新构造了我们的代码，以便它总是能够满足最终用户的期望。它还删除了过时的方法，并且没有破坏其中的任何一个。代码现在是稳定的。

# 结论

利斯科夫替代原理告诉我们什么是正确的亚类型化方法。即使它反映了真实世界的情况，我们也不应该强迫多态。

LSP 代表单词 *SOLID* 中的字母 *L* 。虽然它绑定到了 Go 中不支持的继承和类，但我们仍然可以将这个原则用于多态和接口。

```
Other articles from the SOLID series:**1\.** [**Practical SOLID in Golang: Single Responsability Principle**](/practical-solid-in-golang-single-responsibility-principle-20afb8643483)**2\.** [**Practical SOLID in Golang: Open/Closed Principle**](/practical-solid-in-golang-open-closed-principle-1dd361565452)Some articles from the DDD series:**1.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)**4\.** [**Practical DDD in Golang: Repository**](/practical-ddd-in-golang-repository-d308c9d79ba7)**5\. ...**
```