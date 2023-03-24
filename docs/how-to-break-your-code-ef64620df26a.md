# 如何破解你的代码

> 原文：<https://levelup.gitconnected.com/how-to-break-your-code-ef64620df26a>

Python 中单元测试的快速介绍

![](img/00ca14030b33960aca0e22cc19ba4047.png)

Joshua Woroniecki 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

[最好在用户之前发现程序中的错误，这是一条举世公认的真理。](https://media.giphy.com/media/Jy51gZnlPcAFi/giphy.gif)

[正如一个古老的编程笑话所说:](https://www.reddit.com/r/Jokes/comments/a4a4jc/a_software_qa_engineer_walks_into_a_bar/)一个程序员走进一家酒吧，点了一杯啤酒。99 瓶啤酒。0.999999 啤酒。点了 0 瓶啤酒。点了一只鬣蜥。

然后一个顾客走进来，问洗手间在哪里——整个酒吧突然着火了。

经由吉菲和 tenor.com

当然，当用户输入任何数字时，您的代码都会运行——但是如果他们输入一串无意义的字母呢？一个随机符号？什么都没有？如果您希望程序打开的文件打不开，或者被重命名了，该怎么办？*如果顾客问洗手间在哪里怎么办？*

本文解释了使用 Python 中的 unittest 进行单元测试的基础知识——该做的事、不该做的事和请不要做的事。

让我们开始吧。(记住:永远在吧台后面放一个灭火器。)

# 探索性测试与单元测试

如果你正在读这篇文章，很可能你 a)以前写过一个程序，b)已经对它做过探索性的测试。这意味着你已经输入了一个值到一个函数中，看看输出会是什么。

via Giphy 原件

如果你的程序很小，探索性测试是很棒的。

然而，如果你正在编写一个更复杂的程序，并且希望它是“厨师之吻”的完美之作，你可能会想对你的程序进行一些更结构化的测试，看看到底哪里出错了。

这就是[**unittest**](https://docs.python.org/3/library/unittest.html)**出现的原因。**

**它是一个内置的 Python 框架和测试运行器，这意味着它为我们构造自动测试提供了工具。它也很快——通常在几分之一秒内运行多个测试——并在测试失败时提供有用的反馈。**

**好了，介绍够了。我们去做测试吧。**

# **让我们从测试一些基本功能开始。**

**我们将这三个函数保存为一个模块(记住，模块只是一个 Python 文件)。**

```
**#1) Returns x raised to the power of y** def raise_to(x, y):
    return x ** y**#2) Returns the result of x divided by y** def divide_by(x, y):
    return x / y**#3) Returns a string created by concatenating first_name and last_name**
def concat_names(first_name, last_name):
    return first_name + last_name
```

******注意:**不要忘记将所有文件保存在同一个文件夹/目录中！****

# **现在，让我们测试这些功能。**

## **步骤 1)创建一个测试模块**

**创建一个新的 Python 文件，并使用以下命名约定保存它: **test_filename.py.** (如果没有这个‘test _’前缀，模块将不会按照我们想要的方式运行。)**

## **步骤 2)导入单元测试和测试模块**

**接下来，我们需要导入两个模块:unittest 和我们的测试模块。(我们的模块叫做‘project . py’，这就是我们导入‘project’的原因。)**

```
import unittest
import project
```

## **步骤 3)创建一个从 unittest 继承的类。测试案例**

****为什么？**这允许我们使用 unittest 的所有酷的[测试特性](https://www.youtube.com/watch?v=6tNS--WetLI&t=1438s)。测试用例必须提供。**

```
class ExampleTest(unittest.TestCase)
```

## **步骤 4)使用“assertEqual”关键字创建测试用例**

**因为我们有三个函数要测试，所以我们将创建总共九个测试用例(每个函数三个)——都使用特殊的‘assert equal’关键字。**

**Python 在 unittest 中有几个内置的 assert 关键字，允许我们测试给定的语句是否为真。你可以在这里看到它们的完整列表。**

**对于我们的第一个测试用例，我们将测试我们的“raise_to”函数。记住，这个函数应该把 x 提升到 y 的值。**

**下面是我们将使用的通用格式:**

```
def test_[functionName](self):
      self.assertEqual(filename.functionName(argument1, argument2), expectedResult)
```

**如果您以前使用过面向对象编程(OOP ),这种格式可能看起来很熟悉。这是因为 unittest 创建了 TestCast 类的一个实例——这就是它将“self”作为参数的原因。**

## **关于创建测试用例的注释**

**这是你想仔细考虑如果你的代码中有一个错误，什么会影响你的程序的地方。例如，在下面的代码中，我们的测试用例断言，如果我们将“3”和“2”作为参数传递给 raise_to 函数，该函数应该返回 9。或者，如果你把两个负数相乘，结果应该是正数。**

**为了提高效率，您可以一次运行多个测试用例。这里，我们运行三个测试用例:**

```
**# Define our class** class TestProject(unittest.TestCase):**#Create our testcase** def test_raise_to(self):
        self.assertEqual(project.raise_to(3, 2), 9)
        self.assertEqual(project.raise_to(4, 0), 1)
        self.assertEqual(project.raise_to(4, -2), 0.0625)
```

**就个人而言，我喜欢在 IDE 中运行测试，但是您也可以使用命令提示符。(如果你想走后一条路，我强烈推荐科里·斯查费的这篇教程[。)](https://www.youtube.com/watch?v=6tNS--WetLI)**

## **现在，让我们运行我们的第一个测试！**

**为此，只需像在 IDE 中一样运行代码。在我们的例子中，下面是测试运行程序返回的内容:**

```
.
__________________________________
Ran 1 test in 0.001sOK
```

**这意味着我们的测试通过了——我们的功能正常工作！**

**让我们看看，如果我们改变测试用例，使其中一个失败，会发生什么。我们将更新我们的测试用例，如果 4 和 2 分别是我们的 x 和 y 参数，我们的输出应该是 20。**

**下面是我们运行测试时发生的情况:**

```
F
====================================================================
FAIL: test_raise_to (__main__.TestProject)
--------------------------------------------------------------------
Traceback (most recent call last):
  File "/Applications/test_example.py", line 6, in test_raise_to
    self.assertEqual(project.raise_to(4, 2), 20)
AssertionError: 16 != 20--------------------------------------------------------------------
Ran 1 tests in 0.001sFAILED (failures=1)
```

**测试运行人员不仅告诉我们 a)问题在哪里(哪个断言失败了)，还告诉我们为什么(16！= 20).这是快速有效隔离问题的好方法。**

**下面是我们最终的测试模块代码:**

```
**# Import modules**import unittest
import project**# Define our class**class TestProject(unittest.TestCase):
    def test_raise_to(self):
        self.assertEqual(project.raise_to(3, 2), 9)
        self.assertEqual(project.raise_to(4, 0), 1)
        self.assertEqual(project.raise_to(4, -2), 0.0625)def test_divide_by(self):
        self.assertEqual(project.divide_by(9, 3), 3)
        self.assertEqual(project.divide_by(-1, 1), -1)
        self.assertEqual(project.divide_by(-1, -2), 0.5)def test_concat_names(self):
        self.assertEqual(project.concat_names('Jay', 'Reyes'), 'JayReyes')
        self.assertEqual(project.concat_names('Elina', 'Chen'), 'ElinaChen')
        self.assertEqual(project.concat_names('Cori', 'Malone'), 'CoriMalone')if __name__ == "__main__":
    unittest.main()
```

**和往常一样，学习单元测试的最好方法是实验——用你自己的代码做实验！这是一个很好的方法来找出你自己知识中的差距，并更深入地理解这个概念。**

**如果您想了解更多关于 unittest 的知识，我强烈建议查看下面的参考资料。**

****科里·斯查费视频:** [Python 教程:用 unittest 模块对你的代码进行单元测试](https://www.youtube.com/watch?v=6tNS--WetLI)**

**Python.org 文档 : [用 unittest 进行单元测试](https://docs.python.org/3/library/unittest.html)**

****真正的 Python 教程**:[Python 测试入门](https://realpython.com/python-testing/)**

**祝你好运，并快乐编码！**