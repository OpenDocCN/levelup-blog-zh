# 完美的语言是 Java 和 Python 的结合

> 原文：<https://levelup.gitconnected.com/the-perfect-language-is-a-combination-of-java-and-python-c2eb3bef6fff>

## 我会从 Java 和 Python 中选择什么来构建我自己的编程语言

![](img/1e61ae1c710a26b0ad16fb818431a034.png)

凯文·Ku 从[派克斯](https://www.pexels.com/photo/black-farmed-eyeglasses-in-front-of-laptop-computer-577585/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄的照片

如果我们可以轻松地选择特性来构建我们自己的编程语言，这不是很酷吗？想象一下，当你买一辆车或一台电脑时，例如，你选择一个基本模型/东西开始，然后添加或删除选项。我用 Java 工作了几年，我喜欢它——它肯定还没有死(至少对我来说是这样)。但是我希望我可以在终端中快速开始编码，就像你可以使用 Python 一样。我要求的太多了吗？

我不想完全迁移到 Python。总的来说，我不想为了处理不同的领域而切换语言。也许我可以从零开始建立我自己的，包括我想要的任何东西。但这不是重点。我想有一个通用的模板，并挑选和选择不同的功能*容易*。

事不宜迟，下面是我希望我的完美语言具备的一些东西。受 Java 和 Python 的启发。

# 快速开始编码

我喜欢 Python 的这一点。安装 Python(如果还没有安装的话)。你打开终端，输入 python，瞧。你可以马上开始。

Python 在终端上。

其他如 Ruby 也以这种方式工作。只需输入 irb。

露比在终端上。

但是用 **Java** ，不行，相反，你需要创建一个单独的文件，编译，然后执行。如果必须创建一个“public static void main(String[]args)”呢？！

# 列出理解

这是一种简单明了的列清单的方式。我非常喜欢 Python 中的这个选项。比较好读(一定程度上)。你可以专注于你想对列表做什么，而不用关心它的构造/初始化。据说使用 list comprehensions [更 Pythonic 化](https://realpython.com/list-comprehension-python/)是因为它是一个可以在各种情况下使用的单一工具——不需要使用函数 [map](https://docs.python.org/3/library/functions.html#map) ()和 [filter](https://docs.python.org/3/library/functions.html#filter) ()。作为 Pythonic 语言就是这么简单。

这里有一个不使用它的例子。

没有列表理解。

另一种使用列表理解。

列表理解。

上面演示的例子很简单，但也可能更复杂。你可以在其中包含条件，嵌套列表理解，等等。直到它违背了目的——代码变得可读性更差。

还有集合和字典理解。语法几乎是一样的。你不用方括号，而是用花括号。对于字典，还需要指定一个键值对。

设置理解。

词典释义。

# 仅未检查的异常

没有争论，没有决定，没有困惑。在 Python 中，你不会被迫处理一个[异常](https://docs.python.org/3/tutorial/errors.html)。它们未被检查。

我喜欢未检查的异常。这些年来我越来越喜欢它了。在 Java 中，既有选中的[，也有未选中的](https://docs.oracle.com/javase/tutorial/essential/exceptions/runtime.html)，围绕它有一些争议。这里有一篇关于它的好的[文章](https://www.infoworld.com/article/3142626/are-checked-exceptions-good-or-bad.html)。我在另一个故事中谈到过他们。我更多地使用检查异常，而不是不检查，但我不会说我更喜欢它。这是甲骨文的建议，我认为在一个团队中，你不能简单地选择做一个或另一个，必须保持一致。

检查异常被认为是更安全的；它更早地捕捉到错误。它强制引发异常的方法的调用方正确处理错误。问题是你不能“适当地”强迫。有很多方法做得不正确:吞下异常，重新抛出 RuntimeException 或者只抛出一般的类异常。

这不同于强制编译错误，比如标记一个类型不是 int。没有多少方法可以用别的东西来愚弄/回避编译错误。底线是，我喜欢只在需要的地方处理错误的想法——这可以通过未检查的异常来实现。

# 静态类型的

我不会让 Java 知道的。Python 是动态类型的，也就是说[类型检查](https://realpython.com/lessons/dynamic-vs-static/)只在运行时进行，同一个变量可以有多种类型。请参见下面的 Python 示例。变量 b 首先等于 1，然后它被改为等于“test”——这很好。

在 Java 中则不同，它是静态类型的。当你写代码时，你必须定义变量的类型。如果它是一个字符串或者一个整型数，你就写。而且你不能像使用 Python 那样事后改变。看出区别。

Python 例子。动态类型化。

Java 例子。静态类型化。

我更喜欢静态类型的。

我尝试的第一种动态类型语言是 Ruby。感觉不一样，爽。但是当你思考和阅读更多关于它的内容时，你会发现它是多么的麻烦。您可能有意想不到的数据类型，这可能很难发现错误。如果代码库很小，也许没问题，但是随着它的增长，您需要更多地依赖文档和测试。

Python 也是强类型的，这使得事情稍微安全一些。正如在这个 [wiki](https://wiki.python.org/moin/Why%20is%20Python%20a%20dynamic%20language%20and%20also%20a%20strongly%20typed%20language) 中解释的，解释器跟踪所有变量类型。如果你试图将一个数字和一个字符串相加，你会得到一个错误。比如:5+“ABC”。

值得注意的是，我不太喜欢基于研究和常识的动态类型。我对 Python 没有多少经验；我从未在小型或大型应用程序中使用过它。如果有一天机会来了，我可能会改变主意。

# 花括号

我是花括号的粉丝。可能是因为我更习惯了吧。感觉更清楚，更容易看到作为条件、循环或方法的一部分正在执行什么。我认为它有助于维护。在 Python 中，使用制表符或空格进行缩进，缩进的内容都是块的一部分。

我想知道，Python 中的空格和制表符是怎么回事？当我第一次使用 [Atom](https://atom.io/) 编写我的第一个 python 程序时，我陷入了一个愚蠢的错误，花了我很长时间才解决——我混淆了空格和制表符，Python 抱怨了。这似乎是 Python 开发者的偏好。python.org 建议使用空格。我不确定我更喜欢制表符还是空格。

# Int 的大小不受限制(来自 Python 3)

从 Python 3 开始，[对数字的长度没有限制](https://realpython.com/python-data-types/)。唯一的限制是系统的内存。数字被表示为一个对象，它可以具有任意值。就像 Java 中使用 BigInteger 一样，只是不必使用方法进行基本运算，比如 add()、valueOf()、divide()等。

Python3 中对 int 的大小没有限制

不过，我还没决定买这个。我认为这是一种编码便利，我喜欢它，因为不需要像 Java 那样在 int、long 或 BigInteger 之间做出决定。但是也有开销。对象比基本类型占用更多的内存。

# 最后的想法

总而言之，我最完美的编程语言应该是:

*   快速开始编码
*   列出理解
*   仅未检查的异常
*   静态类型的
*   花括号
*   Int 的大小不受限制

基础将是 Java。我想要 Java 虚拟机(JVM)、ide、社区(当然不是反对 Python 社区)、Spring / Spring Boot。我可能想从 Python 中添加的另一个东西是机器学习相关的库。然而，我没有和他们打交道的经验。也不能与 Java 中可用的工具(如深度学习 4j)兼容。

你的完美语言会是什么样的？感谢阅读。