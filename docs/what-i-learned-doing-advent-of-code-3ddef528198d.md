# 我在《代码降临》中学到了什么

> 原文：<https://levelup.gitconnected.com/what-i-learned-doing-advent-of-code-3ddef528198d>

数据科学家学习创建“生产就绪”代码的旅程

![](img/e71bebe67358f83ed18f3fd81c68f33d.png)

扎克·卢塞罗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这是季节！请注意，这不是与家人共度时光的季节，也不是用金属箔和彩灯装饰房子的时候。不要！“t”是学习数据结构、控制流、类型注释、断言测试、时间和空间效率以及文档字符串的季节！

代码的[出现是一个漂亮的小编码挑战，几乎数据科学和软件开发领域的每个人都知道。它给了你一个圣诞主题的谜语和一个令人难以置信的考验你作为一个开发者的价值。如果你认为 25 个代码挑战不足以满足你，不要担心，因为它们有两个部分。在你解决了第一部分之后，第二部分改变了你需要修改的代码。它。工作。](https://adventofcode.com/)

我能想到的最好的蒂姆·古恩印象。

因此，当我参加这个编码挑战时——并且仍然每天缓慢地工作——我发现自己学到了更多关于那些讨厌的计算机科学的东西，那些数据科学家试图忽略的东西。但是，是时候面对我们的恐惧，开始真正注释我们的 Jupyter 笔记本了(或者，喘息一下，真正从 Jupyter 笔记本中取出我们的代码，创建模块化功能！).我写这篇文章的目的不是试图详细描述我的每一个小发现，而是激励你努力应对代码的出现，并学习一些你可能一直忽略的东西。

# 我学到了什么

## 数据结构

啊，数据结构。

你有你的`int`你有你的`str`你有你的`dict`你有你的`list`和经常被遗忘的`array`(是的，数组存在于 NumPy 之外！只需导入数组 Python 模块。需要复习吗？查看[这篇关于 Python 中列表和数组区别的 get Medium 帖子](https://medium.com/backticks-tildes/list-vs-array-python-data-type-40ac4f294551)。)

关键是，你必须根据你打算用它做什么来选择你的数据结构。而且基于你的数据结构，它可以让操作变得非常快或者非常痛苦。代码出现的第三天教会了我(或者说，提醒了我)当你试图寻找两个事物之间的交集或者共同元素时，集合可以是列表的一个很好的替代品。

第三天，你要找到两条导线的交点，每条导线在笛卡尔平面上跨越超过 150，000 个点。强力检查导线 1 的每个位置和导线 2 的每个位置的大 O 符号是什么？您得到了它— O(n)(假设 wire 1 和 wire 2 都有位置名称编号“n”，在这种情况下基本上是正确的)。这是非常低效和耗时的。

虽然遍历集合比遍历列表开销更大，但检查集合中是否存在元素却快得离谱。为什么？都在 set 类的实现中。python 中的集合是一个"[不同的可散列对象](https://stackoverflow.com/questions/2831212/python-sets-vs-lists)的无序集合"，集合试图通过计算关键字的散列来找到元素。

老实说，我忘了大多数日子都有电视。但是现在，当我需要寻找交集时，我会记得使用集合来代替。

## 流控制

如果需要一个简单的`if`、`elif`、`else`陈述，我很乐意。我已经开始使用`break`和`continue`语句，有时甚至尝试厚颜无耻的`While True`语句。但是我必须承认，为了吸取教训，我已经陷入了过多的循环。

[第 4 天](https://adventofcode.com/2019/day/4)您已经尝试过密码破解，并确保您对流程控制了如指掌。你必须重复输入所有可能的密码。然后您需要一个内部循环来遍历密码中的所有数字，以确保它们是单调递增的。但是你也有另一个标准来确保至少有一组两位数。虽然您可以两次检查密码的整个长度，但是一旦发现密码无效，就退出更有计算意义。

如果这还不够，第 2 部分确保您可以跟踪重复数字持续多长时间。

我特别学到了什么？使用`break`的战略方式。一旦我发现这些数字不是单调递增的，我就会从迭代密码数字的内部循环中脱离出来。

```
def find_password(start=402328, end=864247):
    possible_passwords = []
    for i in range(start, end+1):
        i_str = str(i)

        criteria1 = True      # criteria1: digits never decrease

        for j in range(0, 5):
            if i_str[j+1] < i_str[j]:
                criteria1 = False
                break# code continues ...
```

## 键入注释

类型注释不是我在做《代码的来临》时学到的东西，但这是我第一次系统地、持续地实现它。

你会问，什么是类型注释？如果你在问这个问题，你可能是一个数据科学家，而不是一个核心开发人员。Python 是大多数数据科学家选择的语言，它做了一些被称为动态类型化的事情，其中类型检查是在程序执行期间执行的，而不是在运行之前。那是什么意思？这意味着您不需要显式定义变量的数据类型，Python 不会阻止您尝试向字符串添加 int，直到为时已晚。

Python 3.6 引入了类型注释，实际上我第一次看到它是在奥莱利的 JupyterCon 上的乔尔·格鲁什的演讲中，题目是“为什么我讨厌笔记本”。(热拍。此外，令人难以置信的演示——我强烈推荐它。)当我从头开始阅读[Data Science](https://www.amazon.com/Data-Science-Scratch-Principles-Python/dp/1492041130/ref=pd_sbs_14_t_0/136-0456662-7495958?_encoding=UTF8&pd_rd_i=1492041130&pd_rd_r=d72819e4-87df-4f9e-9319-122a35da8dee&pd_rd_w=AWD8S&pd_rd_wg=ri36F&pf_rd_p=5cfcfe89-300f-47d2-b1ad-a4e27203a02a&pf_rd_r=GGQFB32ZDSBTPTNGGM0N&psc=1&refRID=GGQFB32ZDSBTPTNGGM0N)时，这让我真的想尝试对我的代码进行类型注释，以至少提高可读性。更重要的是，我希望它可以帮助我避免陷入忘记我要做什么和我正在使用哪种数据结构的坏习惯。

这是我在代码出现的第一天[写的带类型注释的代码的一个例子。](https://adventofcode.com/2019/day/1)

```
from typing import List
def load_day1_input(filename: str) -> List[int]:
    mass_lst = []
    with open(filename) as f:
        for line in f:
            mass_lst.append(int(line))
    return mass_lst
```

(注意:Python 中的类型注释本身不做任何事情。它没有把 Python 变成静态类型语言。但是代码清晰总是好的——对你的程序员同事和未来的你都是如此。)

注意在参数声明后使用了`: str`——这表示我们期望类型为`str`的输入。而`-> List[int]`只是说函数的输出是一个整数列表。为了使用`List[int]`类型注释，我们首先需要从`typing`模块导入`List`类。

如果你想了解更多关于类型注释的知识，我强烈推荐这篇[中型文章](https://medium.com/@yothinix/python-type-annotation-and-why-we-need-it-91820a708170)。

## 断言和单元测试

这是另一件事，作为数据科学家，我们做得不够，乔尔·格鲁责备我们。(他是对的。)在编写代码时，使用 Python 中的 Assert 语句来确保您的代码按预期运行，这对于确保您的代码按预期运行至关重要。您编写几个断言语句——通常是常见的用例，然后是不同的边缘用例，以确保您没有任何错误——然后继续编写代码，直到您的断言语句通过。

断言语句也是实现单元测试的好方法。例如，在代码出现的第一天，我编写了下面的 assert 语句，以确保我获得燃料需求的计算是正确的:

```
# Day 1 Part 1 Testing
assert get_fuel_requirements(1969) == 654, "calculation to get fuel requirements is incorrect"
```

作为断言语句的快速介绍，您有三个组件:

1.  `assert`关键字，指定这是您想要检查的内容的开始
2.  要检查的逻辑表达式。在这种情况下，表达式正在检查我的函数`get_fuel_requirements`的输出，与给出的示例相同
3.  可选地，一个逗号`,`和一个用双引号括起来的语句，该语句是 AssertionError，如果 assert 语句失败，它将弹出您的终端。如果 assert 语句是`True`，您的代码将继续顺利运行。

要成为 assert 语句高手，参考这篇[中帖](https://medium.com/better-programming/an-intro-to-python-assert-statements-bdd45834d303)！

## 时间和空间效率

我们都知道这是什么。它把你糟糕的、蛮力的 O(n)代码进行重构，这样它就能在我们的太阳内爆之前完成运行并给你一个答案。

还需要我多说吗？不。但是如果你想要更多的解释，回到数据结构，想象一下试图计算两个 150K+元素列表的交集。(不是集合，是列表。)

![](img/8509ced6954e27da69c72e5deae0f4e9.png)

你职业生涯的太阳下山了，因为你的代码没有完成的意思。Johannes Plenio 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 文档字符串

我不会哀叹 docstrings 的观点。但是当我试着让自己在写代码的时候记录文档(而不是承诺自己会回去记录文档，然后立刻忽略掉)，这个代码挑战让我保持诚实。我不想再上传没有 docstrings 的函数了，你也不应该。

我们都应该在 2020 年翻开新的一页，并确保我们的代码是干净的，有文档记录的，并为生产做好准备。

```
def get_total_fuel_requirements(mass_lst: List[int]) -> int: """
    Iterates through the list of masses,
    calls the function to calculate fuel,
    and returns the total fuel requirement.

    Args:
        mass_lst: List of masses (int) Returns:
        The total fuel requirement (int)
    """ total_fuel = 0 for mass in mass_lst:
        fuel_requirement = get_fuel_requirements(mass)
        total_fuel += fuel_requirement return total_fuel
```

啊。如此清新，如此干净。

# 旅程还在继续

通过我的 [github repo](https://github.com/kathrinv/advent-of-code-2019) 继续我的代码之旅。我会慢慢地把每天的结果放在这里。

是的，我在回购名称中包含了这一年，因为我大胆地假设今后的每一年我都会参与。

是的，我建议你在代码出现的时候也完全尝试一下！挑战自己，看看你能学到什么或提高什么！