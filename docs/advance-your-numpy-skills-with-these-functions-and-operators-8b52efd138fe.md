# 使用这些函数和运算符提高您的数字技能

> 原文：<https://levelup.gitconnected.com/advance-your-numpy-skills-with-these-functions-and-operators-8b52efd138fe>

## 计算机编程语言

## 学会使用 einsum、sliding_window_view、@运算符、省略号对象和几种整形方法来提高自己的 NumPy 技能。

![](img/a84ca58774af6c5b00f6b5942bf31d85.png)

使用 Midjourney 生成的图像。

# 滑动窗口视图()

你做过向量或矩阵的滑动运算吗？例如，假设我们有一个向量`x`,我们想在向量上滑动一个大小为 100 的窗口，并计算均方值。

这可以通过以下方式实现:

在这里，我使用 jupyter 笔记本中的神奇命令`%timeit`对函数计时。现在，如果我告诉你有一个更简单更快捷的方法……这个方法叫做`sliding_window_view()`:

这是大约 19 倍的加速！

那么它是如何工作的呢？如果您想要在多个维度上执行滑动窗口操作，该函数将一个向量或矩阵作为第一个参数，将一个具有单个值或元组的窗口作为第二个参数。第三个参数描述操作应用于哪个(些)维度。

但是这样不是很消耗内存吗？它没有！注意函数的名字，sliding_window_ **view。**NumPy 中的视图不改变原始数据缓冲区，只改变元数据。因此，数据不会被复制，使得操作非常高效。但是，这意味着在数组中的多个位置引用了相同的数据。因此，NumPy 增加了一项保护措施，以防您修改对象:

如果您仍然想修改数组，您可以将关键字参数`writeable=True`添加到函数中。但是要小心，正如您在下面看到的，当我修改数组时，数组中的多个值都发生了变化:

还有更快的方法使用`[scipy.signal.fftconvolve](https://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.fftconvolve.html#scipy.signal.fftconvolve)`进行窗口卷积操作，但这并不适用于所有操作。

# @运算符

`@`运算符与 NumPy 中普通的矩阵乘法(即`np.matmul()`)相同，但更简洁。那就是:

我个人一直写`@`而不是`matmul`。

# 省略号对象

省略号是一个 Python 对象。只需输入`Ellipsis`或更常见的使用`...`即可使用。在 NumPy 中，它在索引期间使用。当索引一个 NumPy 张量时，它实质上在剩余的未指定维度上添加了“:”，并且在处理许多维度时可以非常方便:

# new axis vs . expand _ dims()vs . shape()

在很多情况下，你想改变张量的形状而不改变它的内容。`.reshape()`是最灵活的方法，可以执行任何尺寸变化。另一个用例是简单地添加一个只有一个元素的新维度，例如将(10，)改为(10，1)。然后你可以用`np.newaxis`或者`expand_dims`。

假设你有一个形状为(10，10)的矩阵，你想把它变成一个形状为(10，10，1，1，1)的张量，让我们看看这三种方法是如何做到的:

# 挤压()

现在想象一下相反的问题。我们有一个张量，它有一个或多个尺寸为 1 的维度，我们想把它们去掉。我们该怎么办？`.reshape()`工作一如既往，但是如果维度很多就有点繁琐了。相反，我们可以使用`.squeeze()`:

# einsum()

这种方法可以做许多事情；`einsum`代表*爱因斯坦求和约定*，是一种简洁地编写张量运算公式的方式。首先，我将向您展示它的一些功能示例:

那么这个黑魔法是什么？让我们复习一下公式。这里我来解释一下*显式*模式。还有*隐式*模式，工作方式略有不同。例如，描述尺寸的字母的字母顺序在隐式模式下很重要，但在显式模式下则无关紧要。你可以在[文档](https://numpy.org/doc/stable/reference/generated/numpy.einsum.html)中了解更多信息。

有两部分，左手侧和右手侧由箭头`->`分开:

`<left-hand side>` `->` `<right-hand side>`

左手边描述了所涉及的张量的维数。每个字母代表一个维度，每个张量用逗号隔开。所有尺寸都必须写下来。如果一个字母重复出现，则该字母所代表的尺寸会成倍增加(因此，如果使用相同的字母，则尺寸必须相同)。因此，像`ij,jk`这样的左侧表达式意味着一个维度为`ij`的张量和另一个维度为`jk`的张量。由于重复了`j`，将执行第一张量的第二轴与第二张量的第一轴的乘法(即，第一矩阵的行与第二矩阵的列):

接下来，在右边，结果表达式被公式化。这里省略的尺寸/字母将在上进行*求和，每个字母最多只能出现一次。在上面的例子中，保留了最大可能的张量，但是通过移除索引，我们可以通过对其求和来降低维度:*

特殊情况是，如果你有一个尺寸相同的张量，并且在左边为这个张量多次指定相同的字母。然后，通过指定一个字母或对角线的和(如果留空)来获得右侧的对角线:

您还可以控制尺寸的最终*顺序*:

更多信息，请查看官方[文档](https://numpy.org/doc/stable/reference/generated/numpy.einsum.html)。

为什么当你处理多维度时，事情会变得混乱。谁没有尝试过一些复杂的张量乘法，然后检查结果张量的形状，以确认它工作正常？有了 einsum，最终结果变得更加清晰。此外，您还学习了一种跨框架甚至跨语言的通用技术。NumPy、Pytorch 和 TensorFlow 就是可以在 Python 中使用 einsum 的库的例子。虽然框架的细节可能不同，但符号将保持不变。

感谢阅读。

如果你想注册一个中级会员(无限故事)，同时也支持我，你可以使用我的[推荐链接](https://medium.com/@dreamferus/membership)。然后我会收到佣金(但是价格对你来说不变！).祝你有愉快的一天。

[](https://medium.com/@dreamferus/membership) [## 通过我的推荐链接加入 Medium—Jacob Ferus

### 阅读 Jacob Ferus(以及媒体上成千上万的其他作家)的每一个故事。您的会员费直接支持…

medium.com](https://medium.com/@dreamferus/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)