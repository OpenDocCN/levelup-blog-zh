# 面向 Go 的最快 PostgreSQL 客户端库

> 原文：<https://levelup.gitconnected.com/fastest-postgresql-client-library-for-go-579fa97909fb>

对一些流行的 PostgreSQL 客户端库进行基准测试，以找到最快的一个。

![](img/cfe5bc1f9183d00b9e7306b31fa10ed0.png)

# 动机

提高 RPS(每秒请求数)不是一件容易的事情。更改代码以提高性能更具挑战性。有几个流行的库，几乎每一个都声称是最快的。当基准测试结果过时时，问题就出现了。更糟糕的是，进行基准测试的人以非通用的方式调优库。因此，对于普通用户来说，达到同样的性能几乎是不可能的。

基准测试的目的是在一个受控的环境中找到最快的 PostgreSQL 客户端库，并且不需要任何*魔法来调整任何库。*

# 环境

使用的 Go 客户端库:

*   [jackc/pgx](https://github.com/jackc/pgx) v4.12.0
*   gorm.io/gormv 1 . 21 . 12
*   [lib/pq](https://github.com/lib/pq) v1.10.2
*   jmoiron/sqlx v1.3.4
*   [go-pg/pg](https://github.com/go-pg/pg) v10.10.2
*   [upper/db](https://github.com/upper/db) v4.2.1
*   [上升/下降](https://github.com/uptrace/bun) v0.3.3

规格:

*   使用 PostgreSQL v13.3.0
*   实例大小为 4 核 8 GB
*   使用连接池，其中最大连接池大小为 16

# 基准测试结果

下面的结果是运行基准测试 25 次后的平均结果。

# 了解结果

在每个 SQL 操作的右边有四列，比如上面结果中的 CREATE。它们中每一个的含义是:

*   **迭代** = >运算运行的迭代次数，****值越高越好**。**
*   ****ns/op** = >这是每个函数调用完成的平均时间，**一个** **越小的值越好**。**
*   ****B/op** = >这是每个函数调用使用的平均内存，**值越小越好。****
*   ****allocs/o** = >这是每个函数调用的平均内存分配，**值越小越好。****

**我们可以看到 ***jackc/pgx*** 几乎在所有的运行基准*上都优于其他库。*不包括*更新*操作，其中 ***uptrace/bun*** 实际上胜过它。**

# **结论**

**通过查看上面的结果，我们可以得出结论， ***jackc/pgx*** 是总体上最快的 PostgreSQL 客户端库。建议根据您自己的环境使用专用的最大连接池大小。根据您最繁重的操作，需要进行一些测试来找到平衡最大连接池大小。**