# 实施您自己与 MVCC 的交易

> 原文：<https://levelup.gitconnected.com/implementing-your-own-transactions-with-mvcc-bba11cab8e70>

![](img/be3f086e55107e9a29ebb785f5ba989e.png)

照片由 [Alex wong](https://unsplash.com/@killerfvith?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 摘要

1.  *什么是 MVCC？*提供总体概述。
2.  *什么是隔离？*更详细地介绍了我们提到交易时的真正含义。
3.  *术语*设置一些常用词和基本概念。
4.  *事务 id*解释了我们可以生成唯一事务 id 的不同方式。
5.  *是时候深入研究了*直接跳到代码示例。
6.  *记录可见性和锁定*解释了 MVCC 的核心概念，它允许记录具有条件可见性。
7.  *添加记录。*
8.  *删除记录。*
9.  更新记录。
10.  *提交更改*将所有累积的更改作为原子操作应用。
11.  *回滚更改*将所有累积的更改恢复为原子操作。
12.  *清空或回收空间*讨论了如何清理所有那些僵尸记录。

# 什么是 MVCC？

MVCC ( [多版本并发控制](https://en.wikipedia.org/wiki/Multiversion_concurrency_control))是一种简单而有效的方法来实现管理一组事物的任何应用程序的事务隔离。我们通常认为这些东西是数据库记录，因为它们实际上可以是任何东西。

MVCC 是实现最广泛的并发控制算法之一，因为读取不会阻塞其他读取，写入也不会阻塞其他读取或写入。这意味着我们可以安全并发地让许多客户端同时读写，而不会互相阻塞。我将在后面解释一些值得注意的具体案例。

# **什么是隔离？**

隔离(I from [ACID](https://en.wikipedia.org/wiki/ACID) )确保当客户端开始一个事务时，它看到的数据总是相同的。如果其他事务正在修改数据，则引发。

如果另一笔交易…

*   **增加一条记录**:它将*而不是*对你可见。
*   **删除一条记录**:它将*保持*可见。
*   **修改一条记录**:您将看到交易开始时的版本。

这些规则受制于一些边缘情况，称为[异常](https://en.wikipedia.org/wiki/Isolation_(database_systems)#Read_phenomena)。我现在不讨论这些。然而，他们在[另一篇博客文章](https://medium.com/@elliotchance/sql-transaction-isolation-levels-explained-50d1a2f90d8f)中有更详细的解释。

通常被忽视的是，应用于正确应用程序的事务可以；

*   **减轻阻塞**如果您以前依赖于简单地锁定整个数据库/集合，直到每个修改完成后再继续。
*   **防止出现读取问题**如果您的应用程序因两次读取相同的数据而意外获得两个不同的答案而受到影响。
*   **提供耐用性**。任何时候，如果你改变了主意，你都可以放弃这些改变，而不需要你自己明确地撤销所有的改变。

# **术语**

一个*记录*是一个单一的实体。这最好解释为数据库记录。它也可以是一个文件，一个 JSON 对象，任何封装了数据原子单元的东西。这里最重要的是，记录不能被两个独立的客户/事务同时修改。如果你有一个复杂的数据结构，你需要确保你定义的记录没有封装太多。

一个*集合*是记录的集合。这就像数据库中的一个表。这里重要的是，只要每个记录共享相同的事务 id，事务就不会孤立于单个集合。

一个*交易 ID* (也称为 *XID* )是交易的唯一编号。在同一事务下修改的所有记录都可以作为一个原子操作保存或回滚，这是我们最终想要的。

# **交易 id**

我们可以使用三种交易 ID 编号系统:

1.  一个递增的数字。在交易开始之前(在任何变化发生之前是重要的，即使没有变化)。这是所有记录更改的事务 ID。保存或丢弃更改并不重要，相同的事务 ID 永远不会被再次使用，因此它必须是原子的。同样重要的是，当应用程序重新启动时，必须保留该值，以防止事务 id 被重用。
2.  一个时间戳。我们不需要维护一个原子计数器。这有一个额外的好处，允许我们将集合恢复到某个时间点。有一个警告:如果时间戳没有足够高的分辨率(假设您使用了整秒),开始时彼此非常接近的事务可能会共享相同的事务 ID，可怕的事情将会发生…
3.  **自定义交易 ID 实现**。在特殊情况下，您使用分布式数据库，或者有上述两种类型无法满足的其他需求。这可能包括基于 UUID 的算法，甚至可能不是一个严格的数字。

第 2 项和第 3 项都应该有自己的博客帖子，所以我不会在这里谈论它们。我将只讨论使用递增计数器的最简单实现的机制。

# **是时候潜水了**

我将提供 Python 中的代码示例。完整的示例程序可在本要点中[获得。但是我会在下面解释每一块。](https://gist.github.com/elliotchance/d169e39f5d4d056a9138)

简而言之，我们需要的是下一个事务 ID 和当前活动事务的数组(或[集合](https://en.wikipedia.org/wiki/Set_(mathematics)))来产生新的事务:

```
next_xid = 1
active_xids = set()
records = []def new_transaction():
    global next_xid
    next_xid += 1
    active_xids.add(next_xid)
    return Transaction(next_xid)class Transaction:
    def __init__(self, xid):
        self.xid = xid
        self.rollback_actions = []
```

接下来，我们需要向每个记录添加两条独立的信息。叫做*创造了 XID* 和*过期了 XID* 。两者都代表一个事务 ID，但并不总是需要有值。每个场景都有更详细的解释。

最好将这些直接与唱片本身存储在一起。然而，这并不总是可能的，所以只要您是唯一一个修改数据以保持一致性的人，您就可以在外部的某个地方维护它们。

# **记录可见性和锁定**

最终，这就是 MVCC 的总结:

> 行的可见性取决于谁在看它。

如果从事务的角度来看每一行*可见*，则必须对其进行测试:

```
#class Transaction:
    def record_is_visible(self, record):
        # The record was created in active transaction that is not
        # our own.
        if record['created_xid'] in active_xids and \
            record['created_xid'] != self.xid:
            return False # The record is expired or and no transaction holds it that
        # is our own.
        if record['expired_xid'] != 0 and \
            (record['expired_xid'] not in active_xids or \
            record['expired_xid'] == self.xid):
            return False return True
```

此外，我们之前讨论过，并发事务不能对同一记录进行修改。如果发生这种情况，有两种方法可以处理:

1.  **中止并回滚**试图进行最近更改的事务。您可能还希望将错误传播回原始客户端。
2.  **【阻塞】**等待第二个事务，直到该记录可用。这对于性能和潜在的重新读取错误有一些特殊的挑战。

最安全和最容易的是第一选择——所以这就是我在本教程中要使用的。当我们需要修改记录时，我们需要检查它是否被另一个事务锁定，如下所示:

```
#class Transaction:
    def row_is_locked(self, record):
        return record['expired_xid'] != 0 and \
            row['expired_xid'] in active_xids
```

# **添加记录**

这是一个简单的问题。我们将`created_xid`设置为当前事务 ID，将`expired_xid`设置为`0`:

```
#class Transaction:
    def add_record(self, record):
        record['created_xid'] = self.xid
        record['expired_xid'] = 0
        self.rollback_actions.append(["delete", len(records)])
        records.append(record)
```

稍后将解释`rollback_actions`。

# **删除记录**

有两种可能性:

1.  `expired_xid`是`0`，表示该记录从未被任何人删除过。因此，通过将`expired_xid`设置为当前事务 ID，我们将其标记为已删除。
2.  `expired_xid`不是`0` *和* `expired_xid`是主动事务。该记录已被另一个活动事务删除，因此我们无法触及它。

由于正常的可见性约束，`expired_xid`不是活动事务的第三种情况是不可能的。

```
#class Transaction:
    def delete_record(self, id):
        for i, record in enumerate(records):
            if self.record_is_visible(record) and \
                record['id'] == id:
                if self.row_is_locked(record):
                    raise Error("Row locked by another transaction.")
                else:
                    record['expired_xid'] = self.xid
                    self.rollback_actions.append(["add", i])
```

稍后将解释`rollback_actions`。

# **更新记录**

更新是删除旧记录和添加新记录的简单组合。这允许其他事务仍然可以查看现有记录。如果`delete_record`失败，那么引发的异常将导致随后的`add_record`不发生，这正是我们想要的。

```
#class Transaction:
    def update_record(self, id, name):
        self.delete_record(id)
        self.add_record({"id": id, "name": name})
```

# **提交更改**

一旦所有的修改完成，我们需要*提交*所有的修改，这样未来的客户/交易就可以看到这些新的变化。非常简单，我们只需从活动交易列表中删除我们的交易 ID:

```
#class Transaction:
    def commit(self):
        active_xids.discard(self.xid)
```

请注意，此操作具有一致的时间复杂性，它不是基于我们提交的更改数量。这使得有大量变化的长时间运行的事务成为可能，有时甚至更可取。

# **回滚更改**

回滚可以通过几种方式完成，一种方式是反向重放更改:

```
#class Transaction:
    def rollback(self):
        for action in reversed(self.rollback_actions):
            if action[0] == 'add':
                records[action[1]]['expired_xid'] = 0
            elif action[0] == 'delete':
                records[action[1]]['expired_xid'] = self.xid active_xids.discard(self.xid)
```

这对于一个应用程序来说很好，它可以保证所有的回滚操作都将被重放，并且当应用程序被强制关闭时，所有的活动事务都将被调用`rollback()`。

如果您想要更高的持久性，可以从应用程序随机崩溃中恢复，您将需要将事务 id 存储在更持久的地方。然后，当应用程序启动时，您可以有额外的代码来检查它是否被安全关闭，并在需要时手动修复记录。因为这篇文章已经够长了，所以我不会深入讨论这个问题，但它可能会在以后的文章中解释。

# **吸尘或回收空间**

你可能已经注意到，这个算法实际上并没有删除数据，只是将它标记为已删除。这使得它非常适合在磁盘上保存大量记录，只需对文件进行追加/就地更新以进行修改。

随着时间的推移，你可能会想要收回所有的死角。如果它在内存数据库中，您可以简单地遍历记录，并永久删除现在完全失效的记录。

如果你使用像磁盘这样的媒介，这就不那么容易了。如果您的应用程序不太复杂，您可以将所有非死行重写到一个新文件中，并在您的应用程序下切换它。有大量的解决方案并不特定于 MVCC 的工作方式，所以我将把它留给你。

在任何情况下，我们也需要对行的可见性保持敏感。具有不是`0` *的`expired_xid`和不出现在活动事务中的*的记录是完全死行。

*原载于 2015 年 12 月 20 日*[*http://Elliot . land*](http://elliot.land/post/implementing-your-own-transactions-with-mvcc)*。*