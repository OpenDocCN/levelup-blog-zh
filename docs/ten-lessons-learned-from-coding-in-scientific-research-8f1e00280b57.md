# 科学研究中编码的十个教训

> 原文：<https://levelup.gitconnected.com/ten-lessons-learned-from-coding-in-scientific-research-8f1e00280b57>

## 在我作为一名天体物理学研究生学习编程时，关于 Python 编码的简单事实给了我很大的帮助

![](img/166ea56be03d24f2853c91f2604acaa8.png)

对于那些从事科学研究的人来说，编码经验主要是通过与我们不同领域相关的各种问题解决任务来交付的。作为一名年轻的天文学家，这对我来说是千真万确的，因为我已经度过了我学术生涯的研究生和博士后阶段。我自学了如何用 Python 编码，这是有益的，但也有一些陷阱。对于专业软件程序员来说，我早期的一些代码可能看起来像是妈妈做的意大利面条。此外，我似乎花了更多的时间阅读 StackOverflow 论坛，而不是真正的 Python 文档…不幸的是。然而，即使没有正式的计算机科学课程的指导，我也在成为一名合格的 Python 程序员的道路上取得了一些不错的进展。以下是我学到的最突出的十条经验。

## 1.pandas 非常适合处理列表数据

观测天文学家经常以源目录的形式处理列表数据。用无线电、中红外和光学进行巡天的望远镜能够在有限的积分时间内(从几分钟到几天)探测天空中一个观察区域内的多个物体。当我作为学生开始我的第一次真实数据分析时，它涉及到一个包含数千个射电源的目录，由甚大阵列( [VLA](https://public.nrao.edu/telescopes/vla/) )观测。我需要编写一个软件管道来计算源的几个物理属性。

最初，我为循环写了一系列*来遍历目录，我的代码似乎运行得相当……慢。然后我发现了*熊猫*的神奇世界，经过几次尝试后，我很明显地发现，表格数据在*数据框架*格式*中更容易处理。*结果，我变得相当好奇，想知道为什么*熊猫*在*循环上比*有优势。参加 Python meetup ( *PyMunich* )为我提供了我需要的答案——主要原因是 *pandas* 使用 *NumPy* 数组操作来处理数据帧，这通常比传统的 *for* 循环执行得快得多。*

 [## [代码]-为什么熊猫的速度如此之快？如何定义这样的函数？熊猫

### 从产生正确结果的意义上来说，您的代码是正确的。显式迭代一个…

www.appsloveworld.com](https://www.appsloveworld.com/pandas/100/15/why-is-pandas-so-madly-fast-how-to-define-such-functions) 

## 2.列表理解非常漂亮

通过一些奇迹，我发现了理解列表的优雅。当我不知道什么更好的时候，我会为循环写一个*(他们又来了！)在 Python 列表上执行单个操作。正如你所想象的，这相当…慢。一旦我发现了 list comprehensions，我就明白了，一个包含 200 行不必要代码的 Python 脚本可以通过简单地调用 list comprehensions 而被简化为 50 行。*

这一小块代码…

```
some_list = list(range(100))
sq_root = []
for x in some_list: 
    sq_root.append(x**0.5)
print(sq_root)
```

在列表理解的帮助下，可以转换为:

```
some_list = list(range(100))
sq_root = [ x**0.5 for x in some_list ]
print(sq_root)
```

相当漂亮和…时髦！

![](img/0652091248b926a118640a87180b86f4.png)

## 3.重复的代码块应该是一个函数

这太尴尬了！在我早期，我经常想复制和粘贴需要在一个脚本中多次执行的代码行。重复的代码块可能是一个编程新手最明显的迹象之一，我显然就是这样。复制和粘贴大块的代码显然是低效的，而且会占用不必要的内存。时间教会了我(也教会了很多人)，重复的代码块应该被正确地插入到一个*函数中。*

举个例子，一个完全不了解 Python 函数的新手，可能会写出类似下面这样的东西:

```
x1,y2 = 4,5
h1 = x**2 + y**2

x2,y2 = 6,7
h2 = x**2 + y**2 
```

你的常识会告诉你这很可怕(当你了解 Python 函数的时候)。

将主运算(勾股定理)写成一个函数可以立即改善这一点。

```
def pythag(x,y): 
    return x**2 + y**2

values = [ (4,5), (6,7), (8,9), (9,10) ]
h = [ pythag(x,y) for (x,y) in values ]
```

## 4.这些类是什么，我们为什么需要它们？

在我理解“面向对象编程”的含义之前，为什么需要定义一个*类*对我来说并不总是显而易见的。我会检查我在 Python 发行版中安装的包的源代码，并想知道所有的*类*是关于什么的。

直到有一天，我需要组织工作流进行分析，并感到有一种冲动，要整齐地安排我进行数据分析所需的所有功能。我试图定义一个我自己的*类*。阅读 Python 文档和 Python 大师卢西亚诺·拉马尔霍写的令人惊叹的书 [*流利的 Python*](https://www.fluentpython.com/) ，我学会了如何在一个*类中设置模块(或函数)。*一旦我定义了这个*类*对象，我就可以将定义的模块导入到 Python 脚本中，并随意使用它们。现在这似乎是常识，但在当时，这是关于 Python 的一个相当微妙的细节，我对此一无所知。

这个例子受到 Brett Slatkin 的 [*有效 Python*](https://www.oreilly.com/library/view/effective-python-59/9780134034416/) 的第 3 章的启发，我定义了一个名为 *Apartment* 的*类*对象，其中创建了一个名为 *desk* 的空字典。我们可以向这个字典中添加键/值对。模块 *add_item* 可以向字典中添加条目。模块 *define_use* 然后将值分配给字典中的项目键。现在，创建了一个*类*对象，它包含对我们定义的变量执行操作的模块。

```
Class Apartment(object):
    def _init_(self): 
        self._desk = {}

    def add_item(self, item): 
        self._desk[item] = []

    def define_use(self, item, purpose):
        self._desk.append[item].append(purpose)
```

## 5.现在跟我一起说，“虚拟…环境”

好吧，哇，我曾经在我的机器上安装带有冲突的 [Python 依赖关系](https://stackoverflow.com/questions/36500141/python-packages-with-conflicting-dependencies)的 Python 包弄得一团糟。如果我需要运行我在 GitHub 上找到的 Python 包，而不干扰我的常规 *anaconda* 发行版中的安装，该怎么办？这是一个虚拟环境会派上用场的场景。在一个 P [ython 虚拟环境](https://docs.python.org/3/library/venv.html)中，完全可以安装一个独立于安装在计算机上的 Python 包发行版的包及其依赖项。在 Unix 命令行中创建并激活 Python 虚拟环境的简单方法是:

```
mkdir ./my_project   
cd 'my project'
python3 -m venv project_container
source project_container/bin/activate
```

就这样，您创建了一个有用的容器，可以在其中安装 Python 包及其所有依赖包(和版本),而不会中断机器上 Python 包的分发。

## 6.可以在 Matplotlib.rcParams 中编辑 matplotlib 默认设置

数据可视化在科学研究中至关重要。天文学也不例外。无论一个人是理论家还是观测天文学家，都必须以清晰的视觉形式展示自己的结果——这通常是一个情节。对于科学派的 python 来说，matplotlib 和 PyPlot 仍然是必不可少的。

在编写了几个脚本来创建图之后，人们可能会意识到他们已经开发了一种签名风格，如果你愿意的话，可以选择字体、刻度长度、刻度方向、填充、线宽、线条样式、标记大小等。您可以通过更改名为 *matplotlib.rcParams* 的字典类型对象中的条目来创建新的默认设置，而不是在您编写的每个新绘图脚本中更改所有这些变量。新设置将在运行时或 Python 解释器运行脚本时被采用。假设你选择的默认设置保持不变，那么每次你使用 *matplotlib* 创建图形时，你都可以看到自己定制的绘图设置。

[](https://matplotlib.org/stable/tutorials/introductory/customizing.html) [## 使用样式表和 rcParams 自定义 Matplotlib-Matplotlib 3 . 6 . 2 文档

### 您可以在 python 脚本中动态更改默认 rc(运行时配置)设置，也可以从…

matplotlib.org](https://matplotlib.org/stable/tutorials/introductory/customizing.html) 

## 7.哦，亲爱的，另一个绝对文件路径！

从长远来看，绝对文件路径显然会使代码不可用。我是吃了苦头才知道的。随着时间的推移，文件被删除，文件夹被重新排列或重命名，一个人的目录结构很少保持不变。因此，脚本中的绝对文件路径不可能长久有效。在对目录结构进行修改后运行脚本会导致 Python 解释器抛出一个 *FileNotFoundError。*

*AskPython* 已经提供了关于 *FileNotFoundError* 有时由绝对文件路径引起的综合指南。

[](https://www.askpython.com/python/examples/python-filenotfounderror) [## [已解决] Python filenotfounderror -快速指南- AskPython

### 在本文中，我们将解决 Python 中一个非常常见的错误——file not found error。如果您曾与……

www.askpython.com](https://www.askpython.com/python/examples/python-filenotfounderror) 

使用绝对文件路径的另一个不幸的后果是你的代码变得对用户不友好，这就引出了下一点。

## 8.硬编码的值使代码对用户不友好

我有故事要讲！有一次，我为一个软件工程面试过程提交 Python 编码脚本。该脚本有大量的硬代码值。我面试失败了，面试官在我的反馈表中称我为“初学者”。故事结束了。

当然，当我意识到在工业界(或者学术泡沫之外)，我可能最终会写一些由别人而不是我和我桌上的小妖精来运行和开发的代码时，我开始努力学习让代码变得对用户友好的艺术。在这个艰难的旅程中，一个很好的起点是 PEP-8，这里制定了正确编写 Python 代码的规则(甚至在我还在上小学的时候)。

## 9.考虑参加 MOOC

大规模在线开放课程(MOOCs)是用新鲜信息补充个人学习和编码经验的绝佳方式。我通过 Coursera 免费学习机器学习课程，度过了一段美好的时光。[可汗学院](https://www.khanacademy.org/)、 [edX](https://www.edx.org/) 和 [uDemy](https://www.udemy.com/) 是其他几个知名网站，它们开设了 MOOCs 课程，教授从新手到高级水平的 Python 编程。

## 10.编码当然可以自学

你需要一个计算机科学的博士学位才能成为一名编程专家吗？很可能不会。天文学家花大部分时间开发工具来处理望远镜数据，编写模拟天体物理过程的代码，并开发工具来帮助其他天文学家分析他们的数据，他们是一些最精通的程序员。任何从事物理现象建模和数据分析领域工作的学者都是如此。大多数有经验的程序员可能已经知道，当谈到学习如何编写更好的代码时，经常练习可能是一个人进步的最基本的关键。