# 数据表:一个 C++表格数据结构(v2.0)

> 原文：<https://levelup.gitconnected.com/datatables-a-c-tabular-data-structure-v2-0-1d2e7ba1c287>

在[的前一篇文章](https://medium.com/analytics-vidhya/datatables-a-c-tabular-data-structure-project-d2d928b1c579)中，我介绍并发布了一个名为[数据表](https://github.com/anthonymorast/DataTables)的开源项目，这是一个面向 C++用户的表格数据结构。在创建一个基本的机器学习库的过程中，我发现自己需要一种在 C++中存储表格数据的有效方法，就像如何使用 Pandas 或 R 数据帧来完成这项任务一样。作为那个项目的一部分，我用 C++创建了一个基本的表格数据结构，虽然非常原始，但很像熊猫。从那以后，机器学习项目就半途而废了，但是在效率和有用性方面的许多改进都被添加到了数据表项目中。这篇文章的目的是重新介绍 DataTables 项目的功能和用法，因为刚刚发布的 2.0 版本与 1.x 版本不兼容，但包含许多改进，使其更接近流行的表格数据结构库。

在[拉动请求](https://github.com/anthonymorast/DataTables/pull/16)中可以找到这些变化的简要概述。

示例用法和其他文档可以在[示例目录](https://github.com/anthonymorast/DataTables/tree/master/examples)和 [GitHub 库的自述文件](https://github.com/anthonymorast/DataTables/blob/master/README.md)中找到。

![](img/d225f034ab1d042505c99db44d87c503.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 日期时间支持

实际上，在 1.0 版本的最新更新中添加了日期/时间列支持。为此，将对照一小组正则表达式检查每列中的值，以确定它们是否采用以下日期/时间格式之一

*   年-月-日
*   年/月/日
*   年/月/日
*   年月日日
*   年/月/日时:分:秒
*   yyyy-mm-dd hh:mm:ss

目前，这些是数据表支持的唯一格式，但该框架是非常可扩展的，所以如果你想看到其他格式的添加，只需[打开一个问题](https://github.com/anthonymorast/DataTables/issues)。这些格式的列被自动视为日期/时间，并在处理过程中被视为日期/时间。

# 面向用户

## 增强的数据表形状

*   我创建了一个 DT_SHAPE_TYPE C++对象，它覆盖了<
*   行为与熊猫相似。熊猫的数据帧.形状

下面是该增强功能的一些使用示例。

## 改进的文件 I/O

我在个人项目中使用数据表时遇到的一个主要障碍是读/写时间。这与其他语言的类似项目根本无法相比。因此，我在 v2.0 中花了相当多的时间来增强文件 I/O，这导致了文件写入的显著改进。例如，对于 *mnist_train.csv* 数据集，文件写入时间从大约 30 秒减少到大约 16 秒，几乎快了一倍。

此外，还添加了从具有指定分隔符的文件中导入数据的功能。在 1.0 版本中，仅支持 CSV 文件，但在最新版本中，任何字符都可以被视为分隔符。但是，为了方便起见，项目中保留了 *from_csv(…)* 方法。对于使用 *to_csv(…)* 和 *to_delimited(…)* 方法*的文件写入也是如此。*

## 运行时异常处理

这一改变非常简单，当对数据表执行非法操作时，将会抛出运行时异常。以前，输出消息被发送到 stdout，代码将停止执行。这是一个幼稚的方法，但很容易实现，并获得了这一点。现在，如果某个操作不被允许，就会抛出一个 DataTableException 实例，并显示一条有用的消息，以确定问题来自逻辑中的哪个部分。

## 改进的数据表查看

做了许多改进来增强 DataTable 对象的可见性。此外，删除了一些方法，因为它们在其他增强中变得多余。下面列出了一些变化。

*   添加了两个查看数据的新方法，`head()`和`tail()`，它们分别返回数据表中顶部和底部的 10 行。
*   移除了`dt.print_row(std::ostream& stream, int row)`功能，支持`std::cout << dt.select_row_range(row, row+1) << std::endl;`(或任何其他输出流)。
*   `get_column_names()`方法返回一个可以打印的列名向量(一个 [ColumnNames](https://github.com/anthonymorast/DataTables/blob/master/include/DataTable/ColumnNames.hpp) 类的实例)，例如`ostream << dt.get_column_names() << std::endl;`
*   删除了`void print_column(std::ostream& stream, int column);`和`void print_column(std::ostream& stream, std::string column_name);`，分别支持`ostream << dt.select_columns(col_numbers);`或`ostream << dt.select_columns(col_names);`。
*   增加了通过方括号按名称选择列的能力，例如`dt[“colname”]`，它返回一个数据表对象。

下面的代码演示了其中的一些更改。

## 一些新功能

最后，添加了一些有用的新方法来提供数据表中单个列的更多细节。

*   添加了`min()`、`max()`、`mean()`和`sum()`函数，对于多列数据表，这些函数要么接受列号，要么不接受单列数据表的参数。

如果我们有一个单列数据表，那么我们可以使用接受 0 个参数的方法版本，例如`dt["colname"].max()`，因为列 select `dt["colname"]`返回另一个只有“colname”列的数据表。如果我们有一个多列的数据表，需要指定从 0 开始的列号。未来的工作将允许使用字符串代替数字，这样就可以传递列名而不是数字。

# 在幕后

下面是项目开发人员的一些细节，但不应该以视觉方式直接影响最终用户。

## 改进的单元测试

添加了一些 Catch2 单元测试，以确保进一步更新来打破当前的实现。这些是通过[测试/目录中的](https://github.com/anthonymorast/DataTables/tree/master/test) [Makefile](https://github.com/anthonymorast/DataTables/blob/master/test/Makefile) 构建和运行的。运行 example/目录中的 Makefile 也将间接测试构建到数据表中的许多功能。

更彻底的单元测试需要进一步的工作。

## 常数正确性

[坚持 Const correctance](https://isocpp.org/wiki/faq/const-correctness)原则，以确保不应该改变数据的方法不会改变，并确保不可变对象不会发生变异。这对库的用户来说并没有什么直接的好处，但是让这个项目使用起来更安全了。