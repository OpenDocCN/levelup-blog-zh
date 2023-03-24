# 房间数据库迁移需要记住的事项

> 原文：<https://levelup.gitconnected.com/things-to-keep-in-mind-for-room-db-migrations-7edef257ea3>

![](img/e9c10c078adad7f51b0cecdc134051f2.png)

[形象信用](https://medium.com/@elye.project/android-sqlite-database-migration-b9ad47811d34)

我已经看到了大多数的文章/博客解释了房间图书馆如何工作的基础知识。但是，在使用 Room 时，我意识到没有太多关于 Room 数据库的高级主题，其中之一就是 Room 迁移。所以，我在这里，尝试我的第一篇媒体文章。

对于那些不太了解 Room 的人来说，它是 SQLite 上的一个包装器，允许流畅的数据库访问，这要方便得多。

Room Library 的一个有用特性是易于处理迁移。这意味着您可以轻松地迁移到现有数据库，同时在现有应用程序中添加新功能。

例如，如果您想向现有数据库中添加一个表，您可以这样做:

但是，在迁移数据库时，您还需要记住一些事情，否则事情可能不会像您计划的那样进行。

下面是其中的一些:

# 1.布尔值和长整型值存储为整数

这是文档中没有写的东西。这是非常合理的，因为 SQLite 本身不支持 Boolean & Long 数据类型。

在进行迁移时，您可能会遇到这样一种情况，您希望向现有表中添加一个数据类型为 Boolean 或 Long 的列。因此，这类问题的解决方案是在编写迁移查询时使用 INTEGER 作为数据类型，如下所示:

# 2.TypeConverters 按照我们提供的转换进行存储

如果你使用类型转换器，这是非常明显的。如果你不了解 TypeConverters，下面我来解释一下。

有时，您的应用程序需要使用自定义数据类型，您希望将其值存储在单个数据库列中。为了增加对定制类型的支持，我们提供了一个`[TypeConverter](https://developer.android.com/reference/androidx/room/TypeConverter)`，它将一个定制类 ***转换成一个已知的类型*** ，这样 Room 就可以持久化。

如上所述， ***与已知类型*** 之间的转换非常重要，因为我们作为开发人员负责 TypeConverters 的转换和逆转换。看一下下面的实现示例:

例如，如果您想要添加一个名为 **dob** 的列来存储用户的出生日期，那么我们就必须编写一个`DateConverter`类来将日期转换为 Long，反之亦然，如下所示:

接下来，您将`[@TypeConverters](https://developer.android.com/reference/androidx/room/TypeConverters)`注释添加到`AppDatabase`类中，这样 Room 就可以使用您为每个[实体](https://developer.android.com/training/data-storage/room/defining-data)和 [DAO](https://developer.android.com/training/data-storage/room/accessing-data) 在那个`AppDatabase`中定义的转换器:

因此，由于我们要将 date 转换为 Long，反之亦然，而且正如我们之前看到的那样，Long 在 SQLite 中存储为 INTEGER，因此我们将在 User 表中添加一个类型为 INTEGER 的列。所以，迁移的代码应该是这样的:

# 3.修改现有表的主键

根据我到目前为止所做的研究，如果您想要添加/更新任何现有表的主键，您必须手动迁移该表(就像您在 SQLite 实现中所做的那样)。你能做的是:

*   用修改后的列创建一个新表
*   将旧表中的所有现有数据复制到新表中
*   删除/丢弃旧表
*   将新表重命名为旧表

例如，如果您想在用户表中添加一个名为 ***mobile_no*** 的列，该列需要成为它的主键，您可以像这样进行迁移:

更改现有表的主键的代码段

**Android(Java)Devs 的额外提示:**如果您需要在现有的表中添加一个应该具有 NOT NULL 属性的列，那么您必须为将在 Model/POJO 类中定义的字段提供@NonNull 注释，否则您的应用程序将在第一次启动时崩溃。

# TLDR

> 您应该记住，我们在 **migrate()** 函数中编写的查询是直接在 SQLite 上执行的。因此，无论我们将在其中编写什么查询，都必须由 SQLite 支持，而不是由 Room 支持。

房间库中有些东西没有明确地写在任何地方，比如:

1.  在 SQLite 中，Boolean 和 Long 在内部实现为整数。
2.  TypeConverters 是按照我们在 TypeConverters 类中提供的转换实现的，它实际存储在 type converters 类中。
3.  如果您需要修改现有表的主键，没有直接的方法，您需要手动处理迁移。

**PS** :这些是我尝试过的观察和替代方法。我尽力询问我的一些开发伙伴，除了我提到的以外，他们是否还有其他选择。如果有比我列出的更好的选择，建议总是受欢迎的。

参考资料:

[](https://developer.android.com/training/data-storage/room/referencing-data) [## 使用 Room | Android 开发人员引用复杂数据

### Room 提供了在原始类型和装箱类型之间转换的功能，但不允许对象引用…

developer.android.com](https://developer.android.com/training/data-storage/room/referencing-data) [](https://developer.android.com/training/data-storage/room/migrating-db-versions) [## 迁移房间数据库| Android 开发人员

### 当您在应用中添加和更改功能时，您需要修改房间实体类来反映这些更改…

developer.android.com](https://developer.android.com/training/data-storage/room/migrating-db-versions)