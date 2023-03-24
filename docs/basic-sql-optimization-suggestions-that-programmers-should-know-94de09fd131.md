# 程序员应该知道的基本 SQL 优化建议

> 原文：<https://levelup.gitconnected.com/basic-sql-optimization-suggestions-that-programmers-should-know-94de09fd131>

当我们谈到系统性能优化时，除了代码层面的各种有针对性的优化，还有一个非常重要的手段就是优化数据库的性能。

![](img/be2cc91daf5dc4f8a990e03f95fe0729.png)

在一个互联网系统中，当系统访问量越来越大，数据量越来越多，数据库的压力就会越来越大。如果数据库表结构设计不当，SQL 语句写得不好，代码性能可能极高，但系统却被数据库拖垮了。因此，我们程序员有必要了解数据库和数据库访问优化，以便设计高性能的系统。

作为程序员，我们可能不了解生产环境的服务器硬件配置，无法像 DBA 那样专业地对数据库进行各种实际测试和总结。然而，我们应该很好地理解我们的 SQL 业务逻辑和我们访问的表和字段的数据。事实上，我们不想知道数据库的高可用架构以及如何访问数据。我们只关心我的 SQL 是否能尽快返回结果。那么程序员应该如何优化数据库呢？怎样才能快速定位 SQL 性能问题，找到正确的优化方向？面对这些问题，我给程序员总结了一些基础的优化知识(本文基于 MySQL 数据库)。

## 系统性能优化的基本方向

为了优化计算机系统的性能，我们需要知道系统运行在哪里，并快速定位性能瓶颈。在大多数情况下，最慢的设备将成为瓶颈。众所周知，计算机系统的 CPU 运行速度比缓存快得多，缓存又比内存快得多。所以很多时候，磁盘 IO 和网络 IO 是系统的性能瓶颈。

根据数据库操作原理，这些设备在数据库操作过程中的主要工作内容如下:

*   CPU:事务控制、并发控制、SQL 解析、函数或逻辑计算。
*   内存:缓存数据的读/写。
*   网络:数据响应和查询结果传输。
*   磁盘:数据读写，日志记录，海量数据排序，表连接。

对于数据库，以上四点可以转化为以下四点优化建议:

*   减少磁盘使用和数据访问(设计适当的表结构并创建高性能索引)
*   减少网络访问(批量请求，返回更少的数据)
*   减少 CPU 开销(减少聚合函数调用，合理使用排序)

## 适当的表结构设计

良好的表结构设计是高数据库性能的基础。表结构设计的核心是字段数据类型的选择。选择正确的数据类型至关重要。选择数据类型有一些通用原则:

*   强烈建议为每个表创建一个自动递增的主键。自动增量主键可以将随机 io 提升为顺序 io，并且可以申请索引页，以便使索引页紧凑，并减少页拆分对性能的影响
*   如果长度符合要求，数据类型越小越好，使用的磁盘、内存和 cpu 缓存越少。对于数值数据，如果可以选择无符号类型，则可以选择无符号类型。无符号类型可以存储两倍于有符号类型的正数。
*   确定字符类型长度。请使用 char 而不是 varchar。相同字符长度的字符节省更多的存储空间。
*   如果可以使用时间戳，就不需要 datetime 了。时间戳只需要 4 个字节，日期时间需要 8 个字节。
*   尽量避免空字段。当 MySQL 中的字段为 NULL 时，仍然会占用空间，并使索引和索引统计更加复杂。更新空字段时很容易拆分索引页，这会影响性能。应该使用有意义的值，而不是 NULL。

## 创建高性能索引

顾名思义，数据库索引是用于优化查询的辅助数据结构。这可以看作是为了提高查询速度而创建的另一个冗余数据。索引相对简单。就 MySQL 的 innodb 引擎来说，是用 B+树数据结构存储的。然而，尽管索引很简单，但很少有人能在复杂的表中正确使用索引。

索引会大大增加表记录的 DML(更新、插入、删除)成本。优秀的索引可以使数据库性能提升数百倍，而不合理的索引可能会使性能降低数百倍。因此，有必要平衡在表中创建索引的业务需求。一般来说，关于在哪些字段上创建索引，有一些经验:

*   经常用于查询的字段，该字段过滤出的记录约占总记录的 10%。
*   建议为主键、表关联的外键以及具有标识意义的字段(如用户名、电子邮件等)创建索引。
*   状态标志就像 order_ status、is_ Delete 和 gender 字段不适合创建索引，大文本、大字段和描述字段不适合创建索引。

在下列情况下，即使创建了索引，也不会使用该索引:

*   当索引字段使用`<>`、`not in`、`is null`时，索引将不被使用，如`Index_column <> ?`。你可以用`union`代替`<>`来聚合搜索结果。

如`select id, product_name from order where amount!=1000`

到

`(select id, product_name from order where amount>1000) union all (select id, product_name from order where amount<1000)`

*   普通算术运算或函数运算后的索引字段不能使用索引，如`function(Index_column)=?`、`Index_column+1=?`
*   像带有前导模糊查询的语法不能使用索引，如`index_column like '%?%'`

## 分页查询

当查询的数据量超过总量的 30%时，MySQL 就不会使用索引，所以分页查询非常重要。但是，您也应该小心分页查询。比如`select * from table limit 100000 10;`，MySQL 会查询前 100010 条记录，丢弃前 100000 条记录，所以在分页到比较的页面时查询速度会越来越慢。我们可以通过使用延迟相关来解决这个问题。

`select * from table where id in (select id from table limit 1000000 10)`

这种方法巧妙地利用聚集索引来减少大量回表查询的执行次数，从而提高执行效率。

## 分类的合理使用

数据库排序一般在内存中进行。对于数据库，排序是一项消耗 CPU 的操作。因为现代 CPU 的高性能，上万个数据的排序可能对数据库影响不大。但是，如果一个表中有几十万个数据，就需要考虑如何处理排序。对大数据集进行排序不仅消耗内存和 CPU，内存不够还会发生硬盘排序，导致排序性能急剧下降。所以一般来说，不排序可以不排序。如果必须排序，请尝试为排序字段创建一个索引，因为索引本身是有序的。

还有一些简单的建议，比如只返回需要的数据，批量处理。本文旨在分析一些常见的数据库优化方法，并对面向程序员的 SQL 优化提出一些建议，希望能提高你的 SQL 优化能力。感谢您的阅读。

[](https://medium.com/javarevisited/five-api-performance-optimization-tricks-that-every-java-developer-must-know-75324ee1d244) [## 每个 Java 开发人员都必须知道的五个 API 性能优化技巧

### 为什么你的 API 响应这么慢？也许你需要解决这些问题。

medium.com](https://medium.com/javarevisited/five-api-performance-optimization-tricks-that-every-java-developer-must-know-75324ee1d244) [](https://medium.com/javarevisited/5-best-java-libraries-every-java-developer-should-know-28a1c080bbf) [## 每个 Java 开发人员都应该知道的 5 个最佳 Java 库

### 你知道这些优秀的 Java 库吗？

medium.com](https://medium.com/javarevisited/5-best-java-libraries-every-java-developer-should-know-28a1c080bbf) [](https://medium.com/javarevisited/use-spring-retry-to-implement-automatic-retry-742f4532620c) [## 使用 Spring Retry 实现自动重试

### 使用 spring retry 来优雅地实现重试代码。

medium.com](https://medium.com/javarevisited/use-spring-retry-to-implement-automatic-retry-742f4532620c) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)