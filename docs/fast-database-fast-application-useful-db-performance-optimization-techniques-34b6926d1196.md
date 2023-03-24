# 快速数据库—快速应用程序(有用的数据库性能优化技术)

> 原文：<https://levelup.gitconnected.com/fast-database-fast-application-useful-db-performance-optimization-techniques-34b6926d1196>

## 了解加速关系数据库的最佳实践。

![](img/66a135f1e896e932b3867fc4202b3e23.png)

奥斯卡·萨顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Web 应用程序由几个相互连接的层组成:客户端、web 框架、ORM，以及最底层的数据库。

当应用程序运行缓慢时，性能瓶颈可能隐藏在以下几个级别之一:

*   该表可能在数据库中缺少索引**。**
*   实体框架中可能存在 N+1 问题**。**
*   **应用**逻辑主动“变异”不可变的数据类型，导致大量分配到堆中。
*   **web 框架**在将大型响应发送给客户端之前不会对其进行压缩。

与任何其他复杂的应用程序组件一样，该数据库有许多加快速度的实践和建议。让我们来看看一些对提高性能最有用的实践。

# 根据您的领域选择数据类型

通过在设计表时为列选择尽可能小的数据类型，可以大大节省 SQL Server 使用的内存。

在为任何列选择数据类型之前，仅仅了解它将存储字符串、数字还是其他东西是不够的。您应该更好地理解您的域，并考虑存储在列中的值的范围。

比如用`int`类型来存储一个人的年龄是没有意义的，因为这个类型的范围是-2 到 2。一个人的年龄不能是负数，而且一个人的最大可能年龄从 2⁷到 2⁸不等。这一知识允许我们使用`tinyint`类型(从 0 到 255)来节省内存并提高处理较少数据的查询的性能。

## 估计内存节省

让我们做一个简单的数学计算，估计一下选择`tinyint`而不是`int`类型可以节省多少内存。

假设您的`dbo.Users`表有 250，000 个注册用户。`int`类型大小为 4 字节，因此分配用于存储用户年龄的总内存为 250000 * 4 = 1000000 字节或 1 兆字节。

然后你把`int`换成`tinyint.`一个`tinyint`的大小是 1 字节，比`int`类型小 4 倍。通过做一个简单的改变，你将节省 **0.75** **兆字节**的内存。

# 创建和维护索引

聚集和非聚集索引可以显著加快对表的选择查询，因为索引有助于避免对整个表的线性扫描。

当表有聚集索引时，表中的行按键(通常是主键)排序。一个表可以有一个聚集索引，因为数据不能同时按不同的键进行物理排序。

非聚集索引是一种单独的 B 树数据结构，它包含索引中包含的数据和指向表中实际行的指针。一个表上可以有许多非聚集索引，因为与聚集索引不同，表中的实际数据保持不变。

索引有一个缺点——它们增加了在表上执行 **INSERT、UPDATE 或 DELETE** 语句的时间，因为当表中的数据发生变化时，需要一些时间来重建索引。一个表的索引越多，需要重建的索引就越多。因此，索引许多列会大大降低写操作的速度。

此外，因为非聚集索引是作为单独的数据结构实现的，所以它们**需要额外的内存**。这是将索引数量限制在合理数量的第二个原因——优化内存消耗。

在使用索引来实现更好的应用程序性能时，需要记住以下几点:

*   为外键创建索引。
*   通过基于实际工作负载创建索引来优化性能。仅根据假设创建索引是一种[不成熟的优化](https://en.wikipedia.org/wiki/Program_optimization#:~:text=%22Premature%20optimization%22%20is%20a%20phrase,of%20a%20piece%20of%20code.)。
*   考虑在频繁读取但很少用于写入的表上创建索引。
*   定期检查数据库中以前需要但现在不再需要的索引，并删除它们以避免不必要的写性能下降。
*   了解如何分析数据库中的[缺失索引](https://www.mssqltips.com/sqlservertip/1634/find-sql-server-missing-indexes-with-dmvs/)。

# 对数据进行反规范化以加快查询速度

数据规范化意味着数据是根据[范式](https://en.wikipedia.org/wiki/Database_normalization)构建的。

规范化提高了插入、更新和删除操作的性能，使它们更加简单，因为规范化的数据不会重复，因此只需要在一个地方访问。

规范化表的缺点是选择查询速度慢，这主要是因为两点:

*   规范化的数据分布在许多表中，因此选择查询必须使用连接操作符来获得最终的数据集。
*   定期调用包含 COUNT()、SUM()、AVG()等聚合函数的 SELECT 查询会降低大表上的执行速度。

数据库反规范化旨在提高 SELECT 语句的性能，因为数据存储在更少的表中，所以需要执行更少的 JOIN 语句。此外，如果 SELECT 语句只是从表中检索预先计算的值，而不是在每次执行时计算值，那么它的执行速度会快得多。

反规范化的缺点是它会使写语句变得非常复杂和缓慢，因为必须在多个列/表中更新相同的重复数据。

这是数据库中非规范化数据的示例:

```
Orders table: **ID  Title    Price**
    1   Order 1  545.35
    2   Order 2  766.25
    3   Order 3  1943.12PrecalculatedOrderInfo table: **ID Type         Value**
    1  total_price  3,254.72
    2  max_price    1943.12
    3  min_price    545.35
```

不需要定期运行像`SELECT SUM(Price) FROM Orders`这样的查询，因为记录数量太多，可能需要很长时间，只需运行一次查询，然后将值存储到`PrecalculatedOrderInfo`表中。然后，可以通过运行一个简单的不进行计算的查询来获得总价格。

每次添加或删除新订单时，`PrecalculatedOrderInfo`中的数据也必须更新，这使得维护非规范化数据变得困难。

## 报告数据库

在规范化和反规范化之间找到平衡，就是在数据一致性、优化的内存消耗和良好的选择查询性能之间找到平衡。

但是，通过创建两个单独的数据库，其中规范化数据用于写入，非规范化数据用于读取，并实现在两个数据库之间同步数据的机制，应用程序可以同时获得规范化和非规范化的好处。

你可以在 Martin Fowler 的这篇文章中读到更多关于这种方法的内容。

尽管应用程序由许多层组成，但通常数据库仍然是基础，需要特别的关注和投资来保持整个应用程序快速运行。

## 我的其他文章

[](/5-ways-to-improve-the-performance-of-c-code-for-free-c89188eba5da) [## 5 种免费提高 C#代码性能的方法

### 慢速代码是可选的。

levelup.gitconnected.com](/5-ways-to-improve-the-performance-of-c-code-for-free-c89188eba5da) [](/5-github-repositories-for-net-developers-to-take-tech-skills-to-the-next-level-84f244f6257b) [## 5 GitHub。净回购，让你的技术技能更上一层楼

### 亲身体验 GitHub 资源库。

levelup.gitconnected.com](/5-github-repositories-for-net-developers-to-take-tech-skills-to-the-next-level-84f244f6257b) [](/why-is-list-struct-is-15-times-faster-to-allocate-than-list-class-17f5f79889ae) [## 为什么 C#中 List <struct>的分配速度比 List <class>快 15 倍</class></struct>

### 在上一篇文章《免费提高 C#代码性能的 5 种方法》中，在其中一个例子中，我测量了…

levelup.gitconnected.com](/why-is-list-struct-is-15-times-faster-to-allocate-than-list-class-17f5f79889ae)