# 矩阵加法讲解(Python 示例)-线性代数

> 原文：<https://levelup.gitconnected.com/matrix-addition-explained-with-python-examples-linear-algebra-3857eebad44e>

## 在本文中，我们将通过例子讨论矩阵加法的步骤和直觉，并在 Python 中执行矩阵加法。

![](img/b42e05a2289463bad56f831ae8faafda.png)

作者图片

**目录**

*   介绍
*   矩阵加法解释
*   Python 中的矩阵加法
*   结论

# 介绍

在这篇文章中，我们解释了矩阵加法的直觉和步骤。

使用的例子相当简单，甚至不需要计算器。然而，本文中学习的方法可以应用于更复杂的矩阵加法。

我们还探索了使用 Python 执行矩阵加法是多么的快速和简单。

为了继续学习本教程，我们需要以下 Python 库:numpy。

如果您没有安装它们，请打开“命令提示符”(在 Windows 上)并使用以下代码安装它们:

```
pip install numpy
```

# 矩阵加法解释

当且仅当两个矩阵具有相同的维数(都是 2×2、3×3 等等)时，它们才可以相加。

举个直观的例子，让我们考虑两个农民，他们都有一些苹果和一些葡萄。我们可以用表格来表示:

![](img/84b140261fda011a36239c522c143675.png)

作者图片

这也可以简单地用矩阵表示:

![](img/367d54f25a39e394ebf5343c77a6062e.png)

作者图片

这两个农民又收集了一些水果，正把它们运回仓库。他们收集的水果数量也可以用一张表来表示:

![](img/88aa912d0bd878328ad980e2dd27206d.png)

作者图片

这也可以简单地用矩阵表示:

![](img/8490db81985d88e46b381f3975d23d0b.png)

作者图片

一旦拿到仓库，他们计算仓库里新的水果总数是多少。他们通过将水果数量相加得到:

![](img/29432e5c09d310d7044b130cf05d2aea.png)

作者图片

农民 1 把苹果加到苹果上(3+1=4)，把葡萄加到葡萄上(7+2=9)。农民 2 也是这样，分别得了 5 (2+3)和 7 (5+2)。

现在让我们以矩阵形式做同样的事情:

![](img/da3c98e9d7aca8eb45b7c30a148f5001.png)

作者图片

我们看到矩阵 **C** 与上面有总计的表有相同的值。

我们可以将这种方法进一步推广到一个 **m** × **n** 维矩阵:

![](img/8fb8a6450f401d85dd2904106a38ba62.png)

作者图片

# Python 中的矩阵加法

为了在 Python 中执行矩阵向量乘法，我们将使用 numpy 库。第一步是导入它:

Numpy 有很多有用的函数，对于这个操作，我们将使用 [add()](https://numpy.org/doc/stable/reference/generated/numpy.add.html) 函数，它按元素添加数组。

回想一下，在 Python 中，矩阵被构造为数组，矩阵需要有相同的维数才能相加。下一步是定义输入矩阵。

我们将使用与前面章节中[相同的 2×2 矩阵:](https://pyshark.com/matrix-addition-explained-using-python/#matrix-addition-explained)

现在我们已经有了所需的矩阵，我们可以很容易地计算矩阵相加得到的矩阵:

您应该得到:

```
[[4 5]
 [9 7]]
```

这与我们手动计算的[示例](https://pyshark.com/matrix-addition-explained-using-python/#matrix-addition-explained)中的输出完全相同。

# 结论

在本文中，我们讨论了矩阵加法的直觉和步骤，并展示了使用 Python 的完整示例。

如果你有任何问题或对一些编辑有建议，请随时在下面留下评论，并查看更多我的[线性代数](https://pyshark.com/category/linear-algebra/)文章。

*原载于 2022 年 1 月 17 日 https://pyshark.com*[](https://pyshark.com/matrix-addition-explained-using-python/)**。**