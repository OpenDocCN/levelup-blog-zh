# 中更好地增强了存储库模式的实现。NET C#

> 原文：<https://levelup.gitconnected.com/better-enhanced-repository-pattern-implementation-in-net-c-4e6f4bbe48a9>

## 设计模式

## 了解如何遵循最佳实践来实现更好的增强的**存储库**设计模式，以满足诸如节流之类的扩展需求。

![](img/819d307a486573d89b32e96d9b9112d2.png)

由[弗兰克·麦肯纳](https://unsplash.com/@frankiefoto?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 故事

我和我的团队将要开始一个大项目。是关于一家大公司的网络应用程序。你无法想象我有多想透露它的名字，但不幸的是，我不能。

客户有一些具体的要求，其中一些实际上是技术性的。我知道这不是正常的事情，但是，这个客户已经有了自己的技术团队，他们可以应付我们的团队。

长话短说，让我跳过太长的需求列表，直接跳到我对本文真正感兴趣的需求。

[](https://medium.com/subscribe/@eng_ahmed.tarek) [## 订阅艾哈迈德的时事通讯？

### 订阅艾哈迈德的时事通讯📰直接获得最佳实践、教程、提示、技巧和许多其他很酷的东西…

medium.com](https://medium.com/subscribe/@eng_ahmed.tarek) ![](img/4497771cc2dc2d15940290554da6428b.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 要求

正如我所说，我们的客户有一些具体的要求，其中一些是技术性的。所以，让我带你浏览一下这些有趣的需求，我相信你会爱上它们的。

应用程序需要有一个后端来存储所有处理过的数据。而且，一旦我说**后端**，你就说… **仓库**。

需求中最有趣的部分与要实现的存储库有关。因此，让我告诉你这几个。

## 1.对存储库进行定期自动运行状况检查

RR**需求**:客户希望能够设计、实现并运行所有存储库的自动健康检查。**无论储存库类型如何，设计都应允许盲态健康检查**。

D **设计**:对于允许这样做的设计来说，系统中所有的库应该有一些**的高级抽象。这个抽象层应该尽可能多地公开所有存储库的公共 API。**

## 2.API 对查询的节流限制

R **要求**:所有查询都应该允许应用一些**变量节流限制**来控制每次调用检索的**数据量**。节流标准可以实现为运行时逻辑**，而不是一些硬编码的阈值**。此外，**应告知打电话者**此事，以及**如果可能的话，获取其余**数据的方式。

DD**design**:对于允许这样做的设计，我们应该实现**分页**来使用节流逻辑控制每次调用检索的数据量。此外，一些统一的返回对象应该返回给调用者，通知他有关分页和如何获得下一页，…

![](img/728f2d39f8b62bab15a247681b04376d.png)

由 [Mikael Seegen](https://unsplash.com/@mikael_seegen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 放弃

1.  一些最佳实践可能会被忽略/放弃，以便将主要焦点转移到本文中针对的其他最佳实践上。
2.  所有的代码都可以在 [**这个资源库**](https://github.com/AhmedTarekHasan/BetterRepository) 中找到，这样你就可以很容易地跟随。
3.  客户的一些细节和项目的性质故意不透露，因为他们是机密。
4.  一些代码实现只是为了演示，我不建议在生产代码中使用或模仿它。在这种情况下，会提供一个注释。
5.  要完全理解本文中的代码和设计，需要对其他主题和概念有所了解。在这种情况下，会提供一个注释，也可能是一个链接。
6.  我们的例子中使用的分页实现是专门针对内存列表应用分页的。它不适用于使用实体框架或任何其他框架的生产代码。
7.  请将本文中的代码用作 kickstarter 或思想开创者。在投入生产之前，你需要修改设计，使之适应你自己的需要。

![](img/e5d9c392c4d0dd1873a14e51418d7419.png)

由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# **更好地增强实施和分析**

## 实体

我们将从一个代表雇员的非常简单的实体开始。然而，由于我们的设计通常需要一些高层次的抽象，我们需要一个抽象实体，它可以被我们所有的系统实体继承。

**实体**

对于我们的解决方案，我们将保持简单，不为抽象的`Entity`类提供任何公共成员。但是，对于您自己的解决方案，您可能需要这样做。

**员工**

我们在这里可以注意到:

1.  这是一个简单的`Employee`类，它继承了抽象的`Entity`类。
2.  它有`Id`和`Name`。
3.  它是不可变的，这就是为什么我们有一个构造函数从另一个雇员那里复制所有成员——除了 Id。
4.  我们有一个`ToString`方法的替代方法，仅用于演示目的。**这不是可遵循的最佳实践。**

## 分页

分页概念之前已经在文章 [**分页/分区中用代码示例解释过了——学习主要等式使其变得简单**](/paging-partitioning-main-equations-to-make-it-easy-44fe89d5290b?sk=f65265d7b4c203ac219e7dec1208c0f2) 。

如果您希望理解分页代码，我建议您首先阅读这篇文章。

为了简洁起见，我将在这里包含分页代码，作为快速参考。

我们在这里可以注意到:

1.  这与关于分页的另一篇文章中解释的代码相同。
2.  唯一增加的部分是关于覆盖`ToString`方法。
3.  这样做只是为了演示。这不是可遵循的最佳实践。

## 查询结果

这将我们引向从 Get repository 调用返回的统一对象，在我们的例子中称为`QueryResult`。

我们在这里可以注意到:

1.  我们有`IQueryResult`接口，它表示从 **Get** 存储库调用返回的对象。
2.  它有`PagingDescriptor`和`ActualPageZeroIndex`属性，因此调用者将知道他的调用所检索的数据的确切分页细节。
3.  并且，作为抽象基础`Entity`的`IEnumerable`的`Results`属性产生。
4.  **注意**:下一部分需要注意两个重要概念；**差异。NET** 和**在抽象中隐藏成员**。如果你需要阅读关于这些概念的文章，你可以查阅两篇文章 [**中的协变和逆变。NET C#**](/covariance-and-contravariance-in-net-c-c2b8576b2155?sk=13f0128f87d2cbfb24f30219796bff31) 和 [**中设计接口的最佳实践。NET C#**](/a-best-practice-for-designing-interfaces-in-net-c-2c6ebdb4f1c1?sk=1f07b674adf763b7567d652ddd9f4a43) 。
5.  我们定义了`IQueryResult<out TEntity>`通用接口，这样我们就可以为特定的存储库提供强类型语言支持。
6.  在这个接口上，我们隐藏了`IEnumerable<Entity> IQueryResult.Results`属性，并用一个新的类型化属性替换它。
7.  然后是实现`IQueryResult<TEntity>`接口的`QueryResult<TEntity>`类。
8.  我们有一个对`ToString`方法的覆盖，仅用于演示目的。这不是可遵循的最佳实践。

## AddOrUpdateDescriptor

我们将在我们的存储库上支持不同的方法，包括`AddOrUpdate`方法。这将使来电者的生活更容易随时需要。

为此，我们需要定义一个从`AddOrUpdate`方法返回的统一对象结构。

我们在这里可以注意到:

1.  我们定义了`IAddOrUpdateDescriptor`接口。
2.  还定义了实现`IAddOrUpdateDescriptor`的`AddOrUpdateDescriptor`类，它是不可变的。
3.  还定义了枚举`AddOrUpdate`。

## 查询存储库

现在我们转到我们的存储库定义。我们将把我们的存储库方法分成两部分；**查询**和**命令**。我能听到你说**命令和查询责任分离(CQRS)** 。

是的，我们的设计将包含 CQRS 的相同概念，但它没有在这里完全实现。所以，请不要认为这个设计是一个不完整的 CQRS，因为它本来就不是设计出来的。

N **ote** :下一部分需要注意两个重要概念；**差异。NET** 和**在抽象中隐藏成员**。如果你需要阅读这些概念，你可以查阅两篇文章 [**中的协变和逆变。NET C#**](/covariance-and-contravariance-in-net-c-c2b8576b2155?sk=13f0128f87d2cbfb24f30219796bff31) 和 [**中设计接口的最佳实践。NET C#**](/a-best-practice-for-designing-interfaces-in-net-c-2c6ebdb4f1c1?sk=1f07b674adf763b7567d652ddd9f4a43) 。

## IQueryRepository

我们在这里可以注意到:

1.  这是我们的`IQueryRepository`接口，它表示任何非通用的**查询存储库**。
2.  我们定义了`Entity Get(int id)`方法来通过 Id 获取实体。
3.  我们定义了`IQueryResult<Entity> GetAll()`方法来获取实现库中的所有实体。请记住，应该应用节流限制，这就是为什么我们不只是返回实体列表，我们正在返回`IQueryResult`。
4.  我们定义了`IQueryResult<Entity> Get(int pageSize, int pageIndex)`方法来获取实体划分成一定大小的页面。请记住，这里也应该应用节流限制。因此，如果限制阈值以某种方式设置为 5，并且调用者请求获取每页 8 个实体的页面中的实体，那么将会应用一个自适应，调用者将会通过使用返回的`IQueryResult`对象知道这一点。

## IQueryRepository

遵循文章 [**中解释的相同的主要概念。NET C#**](/a-best-practice-for-designing-interfaces-in-net-c-2c6ebdb4f1c1?sk=1f07b674adf763b7567d652ddd9f4a43) ，我们定义了`IQueryRepository<TEntity>`接口。

我们还定义了`IQueryResult<TEntity> GetByExpression(Expression<Func<TEntity, bool>> predicate)`方法，在使用传入的谓词过滤实体后获取它们。请记住，这里也应该应用节流限制。

此外，我们定义了`IQueryResult<TEntity> GetByExpression(Expression<Func<TEntity, bool>> predicate, int pageSize, int pageIndex)`方法，在使用传入的谓词过滤实体后应用分页。请记住，这里也应该应用节流限制。

## 查询存储库

我们在这里可以注意到:

1.  这是一个实现`IQueryRepository<TEntity>`的抽象类。
2.  来自`IQueryRepository<TEntity>`接口的所有 **Get** 方法的实现将被委托给继承自`QueryRepository<TEntity>`的子类。
3.  对于`IQueryResult<Entity> IQueryRepository.GetAll()`，默认调用另一个`IQueryResult<TEntity> GetAll()`，由子类实现。
4.  对于`Entity IQueryRepository.Get(int id)`,默认调用另一个`TEntity Get(int id)`,由子类实现。
5.  对于`IQueryResult<Entity> IQueryRepository.Get(int pageSize, int pageIndex)`，默认调用另一个`IQueryResult<TEntity> Get(int pageSize, int pageIndex)`，由子类实现。

## 命令存储库

以下是与任何命令存储库相关的定义。

## ICommandRepository

我们在这里可以注意到:

1.  这是我们的`ICommandRepository`接口，它代表了任何非通用的**命令库**。
2.  我们定义了所有需要的方法来与我们的存储库交互。像`Add`、`Update`、`AddOrUpdate`和`Delete`这样的方法。

## ICommandRepository

遵循文章 [**中解释的相同的主要概念。NET C#**](/a-best-practice-for-designing-interfaces-in-net-c-2c6ebdb4f1c1?sk=1f07b674adf763b7567d652ddd9f4a43) 中，我们定义了`ICommandRepository<in TEntity>`接口。

这里值得一提的是，从 **C#8.0 开始，**你可以把这个添加到一个接口上。

`abstract int ICommandRepository.Add(Entity entity);`

在我们的例子中，这意味着尽管`ICommandRepository<in TEntity>`扩展了`ICommandRepository`，但是任何实现`ICommandRepository<in TEntity>`接口的类都不会公开`int Add(**Entity** entity)`方法，除非它被隐式或显式地强制转换为非泛型`ICommandRepository`接口。

## 命令存储库

我们在这里可以注意到:

1.  这是一个实现`ICommandRepository<TEntity>`的抽象类。
2.  来自`ICommandRepository<TEntity>`接口的所有方法的实现将被委托给从`CommandRepository<TEntity>`继承的子类。
3.  对于`int ICommandRepository.Add(Entity entity)`，默认情况下调用其他`int Add(TEntity entity)`，这将由子类实现，但是，我们对传入的类型添加了额外的检查，以确保它与预期的类型相匹配。
4.  对于`IEnumerable<int> ICommandRepository.Add(IEnumerable<Entity> entities)`,默认情况下调用子类实现的另一个`IEnumerable<int> Add(IEnumerable<TEntity> entities)`,但是，我们对传入的类型增加了额外的检查，以确保它与预期的类型匹配。
5.  对于`void ICommandRepository.Update(Entity entity)`,默认情况下调用由子类实现的另一个`void Update(TEntity entity)`,但是，我们对传入的类型添加了额外的检查，以确保它与预期的类型相匹配。
6.  对于`void ICommandRepository.Update(IEnumerable<Entity> entities)`,默认情况下调用另一个将由子类实现的`void Update(IEnumerable<TEntity> entities)`,但是，我们对传入的类型添加了额外的检查，以确保它与预期的类型相匹配。
7.  对于`IAddOrUpdateDescriptor ICommandRepository.AddOrUpdate(Entity entity)`,默认情况下调用由子类实现的另一个`IAddOrUpdateDescriptor AddOrUpdate(TEntity entity)`,但是，我们对传入的类型添加了额外的检查，以确保它与预期的类型相匹配。
8.  对于`IEnumerable<IAddOrUpdateDescriptor> ICommandRepository.AddOrUpdate(IEnumerable<Entity> entities)`,默认情况下调用另一个由子类实现的`IEnumerable<IAddOrUpdateDescriptor> AddOrUpdate(IEnumerable<TEntity> entities)`,但是，我们对传入的类型添加了额外的检查，以确保它与预期的类型相匹配。
9.  对于`bool ICommandRepository.Delete(Entity entity)`,默认情况下调用另一个由子类实现的`bool Delete(TEntity entity)`,但是，我们对传入的类型增加了额外的检查，以确保它与预期的类型相匹配。
10.  对于`IDictionary<int, bool> ICommandRepository.Delete(IEnumerable<Entity> entities)`，默认情况下调用另一个`IDictionary<int, bool> Delete(IEnumerable<TEntity> entities)`，它将由子类实现，但是，我们对传入的类型增加了额外的检查，以确保它与预期的类型相匹配。

## 员工查询和命令存储库

现在，是时候实现我们的雇员**查询**和**命令**存储库了。这是我们开始从基础抽象类`CommandRepository<TEntity>`和`QueryRepository<TEntity>`继承的地方。

但是，首先让我们澄清一下，在我们的例子中，我们不会使用一个真实的数据库来存储我们的雇员数据。我们要做的是创建一个静态列表。

## 员工持久性

就像员工名单一样简单。

## EmployeeQueryRepository

我们在这里可以注意到:

1.  `EmployeeQueryRepository`类继承自`QueryRepository<Employee>`。
2.  我们不必实现来自非泛型`IQueryRepository`接口的方法，因为它们已经在抽象基类`QueryRepository<TEntity>`中实现了。
3.  所有的 **Get** 方法最终都在调用`IQueryResult<Employee> Get(Func<Employee, bool> predicate, int? pageSize, int? pageIndex)`方法中的集中式逻辑，神奇的事情发生了。
4.  在这个方法中，使用静态的`MaxResultsCountPerPage`和我们在**分页**部分实现的`Page`扩展方法进行节流。

## 雇员命令仓库

我们在这里可以注意到:

1.  `EmployeeCommandRepository`类继承自`CommandRepository<Employee>`。
2.  我们不必实现来自非泛型`ICommandRepository`接口的方法，因为它们已经在抽象基类`CommandRepository<TEntity>`中实现了。
3.  实现这些方法只是为了使用静态雇员列表。

![](img/9b07e3edb62d9965f6544a3e7cd3995f.png)

布鲁诺·范德克朗在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 斗牛中的最后一剑

现在，是时候测试我们的设计了。我为快速测试创建了一个控制台应用程序。

测试将分为三个部分:

1.  演示基本操作。
2.  演示错误的铸造检查。
3.  演示如何处理抽象概念。

因此，我们将以如下的`Program`类结束:

我们还定义了一个继承自 Entity 的`Student`类，我们将在某个时候使用它进行测试。

现在，我们来逐一实现`DemonstratingBasicOperation()`、`DemonstratingWrongCastingChecks()`和`DemonstratingDealingWithAbstractions()`。

## 演示基本操作

我们先调用`var result = m_EmployeeQueryRepository.GetAll();`，然后调用`Console.WriteLine(result);`，结果是:

> ActualPageZeroIndex: 0
> 
> 分页描述符:
> ———
> 实际页数:5
> 页数:2
> 
> pages boundries:
> ————
> 第一项零索引:0，最后一项零索引:4
> 第一项零索引:5，最后一项零索引:5
> 
> 结果:
> ———
> Id:0，姓名:艾哈迈德
> Id: 1，姓名:塔里克
> Id: 2，姓名:帕特里克
> Id: 3，姓名:穆罕默德
> Id: 4，姓名:萨拉

因此， **Get All** 工作正常。

然后我们调用`result = m_EmployeeQueryRepository.Get(result.PagingDescriptor.ActualPageSize, result.ActualPageZeroIndex + 1);`，后面跟着`Console.WriteLine(result);`，结果是:

> ActualPageZeroIndex: 1
> 
> 分页描述符:
> ————
> 实际页数:5
> 页数:2
> 
> pages boundries:
> ————
> first item zero index:0，last item zero index:4
> first item zero index:5，LastItemZeroIndex: 5
> 
> 结果:
> ——————————
> Id:5，姓名:阿里

因此，**获取分页**工作正常。

然后我们调用`result = m_EmployeeQueryRepository.Get(6, 0);`，后面跟着`Console.WriteLine(result);`，结果是:

> ActualPageZeroIndex: 0
> 
> 分页描述符:
> ———
> 实际页数:5
> 页数:2
> 
> pages boundries:
> ————
> first item zero index:0，last item zero index:4
> first item zero index:5，LastItemZeroIndex: 5
> 
> 结果:
> ———
> Id:0，姓名:艾哈迈德
> Id: 1，姓名:塔里克
> Id: 2，姓名:帕特里克
> Id: 3，姓名:穆罕默德
> Id: 4，姓名:萨拉

这里我们需要记住 **5** 项的应用节流限制。分析这一点，我们会确信**使用分页**工作正常。

然后我们先调用`var tarek = m_EmployeeQueryRepository.Get(2);`，再调用`Console.WriteLine(tarek);`，结果是:

> Id: 1，姓名:Tarek

因此， **Get By Id** 工作正常。

然后我们先调用`result = m_EmployeeQueryRepository.Get(emp => emp.Name.ToLower().Contains(“t”));`，再调用`Console.WriteLine(result);`，结果是:

> ActualPageZeroIndex: 0
> 
> 分页描述符:
> ———
> 实际页数:2
> 页数:1
> 
> pages boundries:
> ————
> first item zero index:0，LastItemZeroIndex: 1
> 
> 结果:
> ———
> Id:1，姓名:Tarek
> Id: 2，姓名:Patrick

因此， **Get With 谓词过滤器**工作正常。

然后我们调用`var erikId = m_EmployeeCommandRepository.Add(new Employee(0, “Erik”));`，后面跟着`Console.WriteLine(erikId);`，结果是:

> 6

因此，**添加**工作正常。

那我们就打电话

> var added = m _ EmployeeCommandRepository。Add(new []
> {新员工(0，“哈桑”)、新员工(0，“麦”)、新员工(0，“约翰”)})；

其次是`Console.WriteLine(String.Join(“\r\n”, added));`，结果是:

> 7
> 8
> 9

因此，**添加集合**工作正常。

那我们就打电话

> m_EmployeeCommandRepository。更新(新员工(1，“Tarek —更新”))；
> var tarek updated = m _ EmployeeQueryRepository。get(1)；

接着是`Console.WriteLine(tarekUpdated);`，结果是:

> Id: 1，姓名:Tarek —已更新

因此，**更新**工作正常。

那我们就打电话

> m_EmployeeCommandRepository。AddOrUpdate(新员工(1，“Tarek —已更新—已更新)”)；
> var tarekUpdatedUpdated = m _ EmployeeQueryRepository。get(1)；

接着是`Console.WriteLine(tarekUpdatedUpdated);`，结果是:

> Id: 1，姓名:Tarek —更新—更新

因此，**添加或更新**工作正常。

然后我们调用`var deletedTarek = m_EmployeeCommandRepository.Delete(1);`，后面跟着`Console.WriteLine(deletedTarek);`，结果是:

> 真实的

然后呼叫`var checkTarek = m_EmployeeQueryRepository.Get(1);`接着是`Console.WriteLine(checkTarek != null);`，结果是:

> 错误的

所以，**删除**工作正常。

## 演示错误的铸造检查

我们通过转换`m_EmployeeQueryRepository`来定义类型为`IQueryRepository`的局部变量`queryRepository`。

`var queryRepository = m_EmployeeQueryRepository as IQueryRepository;`

并通过强制转换`m_EmployeeCommandRepository`来定义类型为`ICommandRepository`的局部变量`commandRepository`。

`var commandRepository = m_EmployeeCommandRepository as ICommandRepository;`

然后，当试图执行`commandRepository.Add(new Student());`时，失败并出现异常`System.ArgumentException: ‘The type “BetterRepository.Student” does not match the type “BetterRepository.Entities.Employee”’`。

然后，当试图执行`commandRepository.Add(new Student[] { new Student(), new Student() });`时，失败并出现异常`System.ArgumentException: The type “BetterRepository.Student[]” does not match the type “System.Collections.Generic.IEnumerable`1[BetterRepository.Entities.Employee]”`。

因此，**错误铸造检查**工作正常。

## 演示如何处理抽象概念

首先，我们重置员工列表`EmployeePersistence.Reset();`。

然后，我们通过强制转换`m_EmployeeQueryRepository`来定义类型为`IQueryRepository`的局部变量`queryRepository`。

`var queryRepository = m_EmployeeQueryRepository as IQueryRepository;`

并通过强制转换`m_EmployeeCommandRepository`来定义类型为`ICommandRepository`的局部变量`commandRepository`。

`var commandRepository = m_EmployeeCommandRepository as ICommandRepository;`

然后我们执行:

> //当我们实际上不知道他们的类型
> //并且我们不关心他们的类型
> var first two items = query repository 时，获取前两个雇员。Get(2，0)；
> 控制台。WriteLine(first two items)；

结果是:

> Id: 0，姓名:艾哈迈德
> Id: 1，姓名:塔里克

然后我们执行:

> //现在我们在不知道它们的类型
> //也不关心它们的类型
> commandRepository 的情况下，盲目地删除前两项。删除(前两项。结果)；
> 
> //现在我们再次让前两个雇员来检查它是否有效
> first two items = query repository。Get(2，0)；
> 控制台。WriteLine(first two items)；

结果是:

> Id: 2，姓名:帕特里克
> Id: 3，姓名:穆罕默德

因此，**处理抽象**工作正常。

![](img/be67180360063f52236fe5798b9e7c26.png)

Arthur Chauvineau 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 最后的话

哇，长途旅行。现在，让我们休息一下，放松一下，退一步看大局。

我们在这里实现的不是火箭科学。这些概念并不难，只是在实现时要有稳定的手。

就这样，希望你觉得读这个故事和我写它一样有趣。

# 希望这些内容对你有用。如果您想支持:

如果您还不是**媒介**会员，您可以使用 [**我的推荐链接**](https://medium.com/@eng_ahmed.tarek/membership) ，这样我就可以从**媒介**那里获得您的一部分费用，您无需支付任何额外费用。订阅 [**我的简讯**](https://medium.com/subscribe/@eng_ahmed.tarek) 将最佳实践、教程、提示、技巧和许多其他很酷的东西直接发送到您的收件箱。

# 其他资源

这些是你可能会发现有用的其他资源。

[](/paging-partitioning-main-equations-to-make-it-easy-44fe89d5290b) [## 分页/分区—简化这一过程的主要等式

### 最后，这是您理解分页/分区主要等式并学习如何在代码中应用它们的机会。

levelup.gitconnected.com](/paging-partitioning-main-equations-to-make-it-easy-44fe89d5290b) [](https://itnext.io/when-string-gethashcode-in-net-c-drives-you-crazy-c97ac7507d7b) [## 当字符串。中的 GetHashCode()。NET C#让你抓狂

### 知道什么时候依赖字符串。中的 GetHashCode()。NET C#，而当不是。

itnext.io](https://itnext.io/when-string-gethashcode-in-net-c-drives-you-crazy-c97ac7507d7b) [](/useful-free-online-tools-for-developers-ed882d4761c1) [## 对开发者有用的免费在线工具

### 这是一个有用的免费在线工具列表，可以帮助开发者完成日常任务。

levelup.gitconnected.com](/useful-free-online-tools-for-developers-ed882d4761c1) [](/mistakes-made-by-developers-79af38b070b7) [## 开发人员犯的错误

### 这些是开发人员最常犯的错误。

levelup.gitconnected.com](/mistakes-made-by-developers-79af38b070b7)