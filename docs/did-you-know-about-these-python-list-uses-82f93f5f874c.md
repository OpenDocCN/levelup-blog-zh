# 了解奇特的 Python 列表用法

> 原文：<https://levelup.gitconnected.com/did-you-know-about-these-python-list-uses-82f93f5f874c>

![](img/701bfdd55da71bfc9e2f1020b8473c7b.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是列表？

列表是 Python 中的数据结构，它

*   由**有序的**项目序列组成
*   **是否可变**(项目可以修改)
*   可以包含**不同数据类型**的项目(包括其中的其他列表)

# 创建列表

可以使用`[]`(方括号)创建一个列表，并在其中放置由逗号分隔的项目，或者使用内置的 Python 函数`list(iterable)`

*如果您不熟悉 Python 中的内置函数和可重复项，这些链接可能会对您有所帮助！*

[](/namespaces-scopes-an-easy-lesson-in-python-13d654a95ee) [## 名称空间和作用域:Python 中的一堂简单课

### Python 开发者必备！

levelup.gitconnected.com](/namespaces-scopes-an-easy-lesson-in-python-13d654a95ee) [](/but-how-does-looping-work-in-python-af8c8624e80d) [## 但是 Python 中的循环是如何工作的呢？

### 跳下兔子洞学习迭代的秘密！

levelup.gitconnected.com](/but-how-does-looping-work-in-python-af8c8624e80d) 

让我们看看 Python 中的列表能做些什么奇妙的事情吧！

# 以奇特的方式创建列表:列表理解

列表理解是创建列表的一种快速而简洁的方式。

```
list_of_squares = [i**2 for i in range(10)]
```

这是一种比使用`for`循环和向空列表追加值更快的创建列表的方式。

让我们看看这是怎么回事！

*因时差而惊讶？*

# 嵌套列表

列表中可以包含列表。

这允许灵活地表示复杂的真实世界的数据。

让我们使用嵌套列表创建一个 3x3 的矩阵，并看看它是如何工作的。

![](img/c135a940dc1d6096cd9cc230ad232862.png)

[活动创建者](https://unsplash.com/@campaign_creators?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 作为堆栈列出

S tack 是一种数据结构，允许以 **LIFO** ( **后进先出**)的顺序访问其项目/元素。

把这个想象成一堆书。

您可以从顶部添加和移除图书。

这意味着堆叠的最后一本书将是第一本被拿起的书。

不支持从底部添加和移除图书。

![](img/496fbeac190ac21c86899cd9e2dd5402.png)

由 [Florencia Viadana](https://unsplash.com/@florenciaviadana?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

让我们用 Python 来实现它。

在这个例子中，我们将使用`append`和`pop`列表方法。

# 列为队列

Q ueue 是一种数据结构，允许以 **FIFO** **(先进先出**)顺序访问其项目/元素。

把它想象成商店前的购物者队列。

最先加入队列的人最先购买商品(最先离开队列)。

![](img/41ddc1a0000cca3651fb9a90002ac20f.png)

Melanie Pongratz 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 使用列表作为队列的问题

请记住，从列表末尾追加和弹出元素是快速操作。

如果从列表的开始做同样的事情，这些操作会变得非常慢，因为所有其他元素的位置都必须在内存中移位一位。

## 解决办法

为了解决这个问题，我们在 Python 中使用了一种特殊类型的集合。

这叫做`deque`。

要使用这种类似数据结构的优化列表，请从 Python 中内置的`collections`模块导入`deque`。

***希望这能帮助你今天学到一些新东西！***

***非常感谢阅读这篇文章！***

[](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium-Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)