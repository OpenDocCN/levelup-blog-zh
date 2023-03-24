# LINQ 的延期执行

> 原文：<https://levelup.gitconnected.com/linqs-deferred-execution-429134184df4>

![](img/10dba439440d722559145608de1f2697.png)

照片由[杰克 B](https://unsplash.com/@nervum) 在 [Unsplash](https://unsplash.com/) 上拍摄

大多数 LINQ 方法使用延迟执行，这意味着只有在遍历集合时才执行查询。

使用延迟执行有两个好处:

*   无穷级数
*   最新数据

# 无穷级数

如果 Linq 立即执行方法，下面的代码将导致一个`OverflowException`。

当`FibonacciNumbers()`被调用时(就在`.Take(4)`之前)，它还没有被执行。如果像大多数方法一样，那么它将陷入无限循环。

由于延迟执行，Linq 知道在枚举方法之前只取前 4 项。

调用`ToList()`强制枚举方法，只返回 4 项。

# 最新数据

下面的代码打印 1 到 5，而不仅仅是第一眼看到的 1 到 4。

延迟执行意味着我们在枚举集合之前获得了最新的数据。

# 在幕后

如果你想了解更多 Linq 的幕后工作原理，可以看看这篇[文章](/linq-behind-the-scenes-efd664d9ebf8)。