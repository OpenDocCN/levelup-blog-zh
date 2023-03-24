# 利用 DiffUtil 提升回收查看性能

> 原文：<https://levelup.gitconnected.com/boost-recyclerview-performance-with-diffutil-4110b668e534>

嗨，在本文中，我将展示如何使用 DiffUtil 实用程序类来帮助我们以优化的方式更新 RecyclerView 列表。

![](img/201328556ba8e89207c1570f7fc8c2b3.png)

## 它是如何工作的？

*DiffUtil，使用 Eugene W. Myers 的差分算法计算将一个列表转换为另一个列表的最小更新次数。*

## **实现**

首先，让我们为 RecyclerView 适配器创建数据模型。

之后，我们将通过扩展 DiffUtil 来创建 MovieDiffUtil 类。回调抽象类，它有 4 个抽象方法要覆盖。

让我们看看哪些方法将被覆盖，以及我们如何使用它们。

getOldListSize(): 它返回旧的列表大小。

getNewListSize(): 它返回新的列表大小

**areItemsTheSame():** 返回一个布尔值，判断两个对象是否代表同一个项目。在这个方法中，我们可以通过两个对象的唯一值(如 id)来比较它们。

**areContentsTheSame():** 返回一个布尔值，表明两个项目是否有相同的数据。在这个方法中，我们可以直接比较对象引用。

完成这些步骤后，我们将能够在我们的 MovieAdapter 中使用 MovieDiffUtil。

在 setData()方法中，我们使用 calculateDiff()方法计算两个列表之间的差异，然后将结果保存在 DiffResult 对象中，使用该对象，我们使用 dispatchUpdatedTo()方法将更新后的列表发送到适配器。

最后，每当我们的列表改变时，我们可以在片段或活动中调用 setData()方法。

# asynclistdifferent

当我们的列表中有大量数据时，计算两个列表之间的差异的操作可能需要很长时间，因此我们应该异步地执行这些操作，以免阻塞主线程。幸运的是，有了 AsyncListDiffer，我们可以很容易地在后台线程上实现它。

## 履行

这一次，我们使用扩展 DiffUtil 的匿名类创建了一个 DiffUtil 对象。然后实现了它的成员。

> val list differ = AsyncListDiffer(this，diffUtil)

使用这段代码，我们在一个后台线程上进行所有的比较，并将结果分配给一个名为 list different 的对象。

通过使用这个对象，

我们使用**list different . current list**访问更新后的列表，并使用**list different . submit list(movie list)**将新列表提交给适配器

之后，只要我们的列表发生变化，我们就可以在片段或活动中调用 setData()方法。

感谢阅读，下次见😎