# NumPy 教程:分分钟学会基础！

> 原文：<https://levelup.gitconnected.com/getting-started-with-numpy-learn-the-basics-in-minutes-5f896074e13f>

## 用大量的数据做快速的事情，但是确保你知道什么时候和为什么(什么时候不)使用它！

![](img/b1c6e88d74cc683455c2a42e006aa8ba.png)

想用 Python 处理大量数据吗？你应该学 NumPy。

NumPy 是一个非常棒的工具，如果您是 Python 开发人员、数据科学家或者只是对使用数据感兴趣的人，您应该熟悉它。

但是，也许您还没有尝试过使用它，或者被它明显的学习曲线和与标准 Python 数据结构的差异所吓倒。

在这篇文章中，我们将看看 NumPy，你可以用它做什么，什么时候你应该使用它，什么时候不使用它，以及为什么它如此受欢迎。

# 数字优势#1:速度

![](img/1f3851e3c81aa0bf91d9e965d093ae75.png)

速度，宝贝

当使用 Python 进行繁重的数据处理和计算时，您会经常听说 Python 天生就非常慢。

然而，尽管相对缓慢，Python 已经成为数据科学的首选语言。处理数百万或数十亿个数据点的应用程序使用 Python。

太奇怪了。为什么数据科学家和工程师会选择一种甚至稍微慢一点的语言呢？在数十亿次计算的过程中，这肯定会增加吗？

进入 Python 的 C API。虽然 Python 可能是速度较慢的语言之一，但 Python 最常见的发行版是建立在速度最快的语言之一 C 之上的，当我们需要极快的计算时，Python 有一个运行 C 代码的接口。这些 C 扩展使得在 Python 应用程序中提供 C 级性能变得容易。

NumPy 就是这样一个 C 扩展。虽然 CPython 本身是用 C 编写的，但它必须适合于一般用途以及许多不同的数据类型和边缘情况。相比之下，NumPy 在 C 中实现了自己的定制数据类型，这些数据类型针对快速计算和修改进行了优化。NumPy 可以让计算进行得更快，有时甚至快 10 倍。

这里有一个例子(感谢 [tom10](https://stackoverflow.com/a/994545) 的这段代码片段，评论是我的):

```
from numpy import arange
from timeit import TimerNelements = 10000
Ntimeits = 10000x = arange(Nelements) # <-- A NumPy range
y = range(Nelements) # <-- A standard Python range# The numpy .sum() method is optimized for calculations
t_numpy = Timer("x.sum()", "from __main__ import x")# The CPython sum() function is more generalized
t_list = Timer("sum(y)", "from __main__ import y")print("numpy: %.3e" % (t_numpy.timeit(Ntimeits)/Ntimeits,))
print("list:  %.3e" % (t_list.timeit(Ntimeits)/Ntimeits,))
```

在我的电脑上，输出:

```
python time-sum.py 
numpy: 2.650e-05
list:  5.758e-04
```

在这种情况下，NumPy 对一个范围内的值求和的速度要快 20 倍。这是很重要的，它开始让你了解为什么 NumPy 如此有价值和被广泛使用。

# 数字优势#2:内存

![](img/615bfc90c226df9ca43c8aa04e690f58.png)

更少的内存支持更多的数字

NumPy 的内存效率更高，读写速度更快。这是因为 NumPy 对如何构建阵列设置了限制。

在标准 Python 中，列表可以包含任何值:

```
# This is a valid list
my_list = ['a', 2, len, {'z': 1}]
```

在这个列表中，我们有一个字符串、整数、函数和字典。但是 Python 实际上并没有在内存中顺序存储这四样东西。相反，Python 列表只是一系列指向对象本身的指针，无论它们存在于何处。

因此，字符串`'a'`存在于其他地方。整数`2`是 Python 在启动时创建的单例(所有介于-1 和 255 之间的整数都是如此)。函数`len`在标准库中定义。字典实际上是一个独立的复杂对象，也由指向其子对象的指针组成。

所有这些指针都增加了 Python 列表的复杂性和规模。Python 列表中的每个值至少有 20 个字节。每个指针 4 个字节，再加上 16 个字节，即使是最小的 Python 对象也是如此(4 个字节用于类型指针，4 个字节用于引用计数，4 个字节用于值——内存分配器四舍五入到 16)。感谢[阿历克斯·马尔泰利](https://stackoverflow.com/a/994010)的数学计算。]

我们可以自己测试列表对象是通过引用传递的:

```
>>> my_list = ['a', 2, len, {'z': 1}]
>>> my_list[2](my_list)
4
>>> len(my_list) # Equivalent to the line above
4
```

相比之下，NumPy 数组足够智能，可以判断出您是否传入了统一值的列表。如果你做了，NumPy 将优化这些值的存储和操作。

```
>>> a = np.array([2,3,4])
>>> a
array([2, 3, 4])
>>> a.dtype
dtype('int64') # Stored as a 64-bit integer, instead of Python obj# We can still create a np array from my_list
>>> b = np.array(my_list)
# But everything is stored in the standard Python way as objects
>>> b
array(['a', 2, <built-in function len>, {'z': 1}], dtype=object)
```

当您给出 NumPy 个标准化输入时，内存优化可能是实质性的。例如，Python 需要 12GB 的内存来处理 10 亿个`float`。另一方面，NumPy 可以用大约 4GB 来处理。[再次向[亚历克斯·马尔泰利](https://stackoverflow.com/a/994010)致敬]

# 数字优势#3:方便

![](img/c99eb9d27a78cfbd2c08722fa828d3ae.png)

放松，放松，让 NumPy 来做工作

如果您正在处理大型数据集，那么您需要执行一些常见的操作。

向量、矩阵操作、多维数组和回归都很常见。

在标准 Python 中，这些实现起来并不简单。在 NumPy 中，它们是开箱即用的，具有经过优化和测试的实现。

此外，您可以直接从文件读取数据到 NumPy 数组中，并对大型数据集更有效地执行其他 I/O 任务。

此外，大多数其他数据包，包括统计和机器学习包，都支持 NumPy 数据结构，使 NumPy 成为任何数据密集型项目的良好基础，即使内存和速度不是问题。

# 试试吧！

希望您现在已经看到了 NumPy 是多么有用，并决定了它对您的应用程序是否有意义。

无论如何，如果你是一个经常接触数据的人，这是一项强大的技术，值得学习如何使用。现在对 NumPy 的理解可以在你以后遇到一个可能用到它的任务时得到回报。

## 1.装置

NumPy 作为已建立的科学计算 Python 发行版的一部分使用时效果最佳。这样你就可以充分利用它。

到目前为止，最流行的科学 Python 发行版是 Anaconda。还有一个更小的版本叫做 Miniconda，它可以让你得到你需要的大部分东西。

在虚拟环境中创建新目录并安装 Anaconda/Miniconda:

```
# Install the distribution
pyenv install miniconda3-latest# Create a virtual environment using that distribution
pyenv virtualenv miniconda3-latest <your-env-name># Set that virtual environment as the default for this directory
pyenv local <your-env-name>
```

现在我们已经安装了它，我们需要激活 Anaconda。具体来说，Anaconda 使用自己的包管理器(就像您可能熟悉的`pip`)。这个包管理器叫做`conda`。

您可以对许多类型的 shells 使用`conda`包管理器:

*   尝试
*   鱼
*   tcsh
*   xonsh
*   zsh
*   powershell

要激活它:

```
conda init <your shell name>
eval $SHELL # Restart your shell so changes take effect
conda activate <your-env-name>
```

现在，我们可以安装 NumPy 了。

```
conda install numpy
```

看看吧！

```
$ python
Python 3.8.0 (default, Nov  1 2019, 18:25:29) 
[GCC 7.3.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import numpy
>>> numpy.__version__
'1.11.3'
```

## 2.尝试一些教程

这个帖子已经很长了，我就不把它做成初学者 NumPy 教程了。另外，已经有很多很棒的资源了。假设您已经有了一些 Python 经验，以下是我的首选:

1.  [官方快速入门教程](https://numpy.org/devdocs/user/quickstart.html)了解基础知识
2.  [科学计算生态系统简介的科学讲座笔记](https://scipy-lectures.org/)
3.  [从 Python 到 NumPy](https://github.com/rougier/from-python-to-numpy) 学习如何将 Python 函数实际转换成速度更快的 NumPy 等价函数

## 3.用 NumPy 做点什么

通过一些练习来测试和扩展你的新知识:

*   [100 个数字练习](https://github.com/rougier/numpy-100/blob/master/100_Numpy_exercises.md)

然后尝试一些简单的项目:

*   [使用 NumPy 数组的数独求解器](https://medium.com/datadriveninvestor/solving-sudoku-in-seconds-or-less-with-python-1c21f10117d6)
*   你所在的[城市地铁系统的平均延误时间](https://opendata.cityofnewyork.us/)
*   计算 1、5、10、20 年内[各种投资组合分配](https://finance.yahoo.com/quote/%5EGSPC/history?p=%5EGSPC)的回报

# 如何以及为什么使用 NumPy

NumPy 并不是每个用例的完美解决方案。最值得注意的是，使用 NumPy 会让你的代码更难阅读，所以一定要大量注释。

NumPy 的数据结构也不能完全替代 Python 的列表。相反，您应该将 NumPy 视为一个额外的工具，使统一数据集的使用更加容易。

根据一般经验，每当有大量相同格式的数据时，最好使用 NumPy，尤其是当有数百万或数十亿个数据点时。

如果您有一个数据密集型应用程序，NumPy 有一种方法可以帮助您优化操作。

# 关于班尼特

我是一名用 Python 和 JavaScript 构建东西的 web 开发人员。

想要我关于 web 开发和成为更好的程序员的最佳内容吗？

我在邮件列表中分享我最喜欢的建议——没有垃圾邮件，没有推销内容，只有有用的内容。

加入我的电子邮件系列中的 500 名其他开发人员。