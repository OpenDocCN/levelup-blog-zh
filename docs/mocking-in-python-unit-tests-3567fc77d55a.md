# Python 单元测试中的模仿

> 原文：<https://levelup.gitconnected.com/mocking-in-python-unit-tests-3567fc77d55a>

## 用 Mock 更好地利用 Pytest

![](img/10a95df4b742caf99d6f5b07e80e654f.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/@cdr6934?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在[之前的文章](https://medium.com/p/5c59cdf89529)中，我们看到了 pytest 的第一个介绍——Python 的单元测试框架，很多人都选择了它。

在这里，我们将看看另一个特点，即嘲讽。在测试中，模仿被用来替换我们不想或者不能运行的函数。这方面的主要用例是昂贵、耗时的功能，需要外部资源和/或数据的功能——或者通常是遵循单元测试思想的测试:与完整的[集成测试](https://en.wikipedia.org/wiki/Integration_testing)相比，单独的、可测试的组件。

# 概观

在我看来，嘲讽是一个复杂的话题，你可以随意深入其中。老实说，我不太喜欢 Python，因为有太多的选择，却缺少好的文档。

这篇文章的目的不是试图解释嘲讽的所有特性，而是——给出一个快速的概述，使你能够以我认为最简单的方式使用嘲讽。特别是，我们将利用*打补丁*。如果你想更深入，我想参考一些现有的资源，比如 [realpython](https://realpython.com/python-mock-library/) 。

在这篇文章中，我们将使用 Python 的[模拟](https://pypi.org/project/mock/)包，它包含在 Python 3.3 版本的标准库中(对于早期版本，通过`pip install mock`手动安装)。

# 使用模拟

让我们以下面的例子为基础:

```
from unittest.mock import patch
import time

def expensive_function():
    time.sleep(10)
    return 42

def function_to_be_tested():
    expensive_operation_result = expensive_function()
    return expensive_operation_result * 2

def test_function_to_be_tested():
    assert function_to_be_tested() == 84
```

我们想要测试`function_to_be_tested`，它简单地调用`expensive_function`并返回其乘以 2 的返回值。在`expensive_function`内部，我们等待 10s(或者想象其他慢函数)，这对于单元测试来说是不可行的。

因此，让我们使用 mock 来修改单元测试。我们首先定义我们将使用的函数，而不是`expensive_function`:

```
def mocked_expensive_function():
    return 42
```

如上所述，我们现在想在现有的函数上“修补”这个函数。为此，有两种选择——使用[装饰器](https://medium.com/@hrmnmichaels/python-decorators-816236066ed8)或[上下文管理器](https://medium.com/@hrmnmichaels/managing-resources-in-python-with-context-managers-with-statement-f07afc1afb4f)。

## 使用上下文管理器打补丁

使用上下文管理器打补丁看起来像这样——瞧——测试立即完成:

```
def test_function_to_be_tested():
    with patch(
        "sample_file.expensive_function",
        new=mocked_expensive_function,
    ):
        assert function_to_be_tested() == 84
```

注意，这里我们必须指定函数的完整路径——在“何处打补丁”一节中有更多相关内容。

为了简化上述内容，我们还可以使用 lambda 函数:

```
def test_function_to_be_tested():
    with patch(
        "sample_file.expensive_function",
        new=lambda: 42,
    ):
        assert function_to_be_tested() == 84
```

## 用装饰者修补

或者(通常，我会采用这种方法——但这在某种程度上取决于它的使用场合),可以使用 decorators 进行修补(为了正确输入，导入`from unittest.mock import MagicMock`)——然后将上面的代码写成:

```
@patch("sample_file.expensive_function")
def test_function_to_be_tested(mock_expensive_function: MagicMock):
    mock_expensive_function.return_value = 42
    assert function_to_be_tested() == 84
```

正如我们所看到的，打了补丁的函数现在变成了类型为`MagicMock`的函数参数。这个参数的名称是任意的，但是，请注意参数的“反转”顺序，即:

```
@patch("sample_file.expensive_function")
@patch("sample_file.foo")
def test_function_to_be_tested(foo: MagicMock, mock_expensive_function: MagicMock):
    mock_expensive_function.return_value = 42
    assert function_to_be_tested() == 84
```

## 哪里打补丁

知道在哪里打补丁可能有点痛苦，而且容易搞砸。更糟糕的是，您不会收到错误通知，而是修补一个未使用的函数，同时仍然使用原来的函数。为了便于演示，让我们将上述代码分成如下两个文件:

**sample_file_two.py:**

```
import time

def expensive_function():
    time.sleep(10)
    return 42
```

**sample_file.py:**

```
from unittest.mock import MagicMock, patch
import sample_file_two

def function_to_be_tested():
    expensive_operation_result = sample_file_two.expensive_function()
    return expensive_operation_result * 2

@patch("sample_file_two.expensive_function")
def test_function_to_be_tested(mock_expensive_function: MagicMock):
    mock_expensive_function.return_value = 42
    assert function_to_be_tested() == 84
```

这段代码是正确的，并且遵循我们介绍的符号。不过，可以考虑把进口改成`from sample_file_two import expensive_function`。然后，我们需要将代码修改为:

```
from unittest.mock import MagicMock, patch
from sample_file_two import expensive_function

def function_to_be_tested():
    expensive_operation_result = expensive_function()
    return expensive_operation_result * 2

@patch("sample_file.expensive_function")
def test_function_to_be_tested(mock_expensive_function: MagicMock):
    mock_expensive_function.return_value = 42
    assert function_to_be_tested() == 84
```

即从当前文件中瞄准`expensive_function`。原因是导入的第二个版本将实际函数绑定到局部范围。这也可以作为一个经验法则:在函数存在/被查找范围内打补丁。

## 修补对象

最后，让我们快速浏览一下模仿对象。假设以下场景:

```
class MySummationClass:
    def expensive_sum(self, x, y):
        time.sleep(10)
        return x + y

def test_sum():
    my_summation_class = MySummationClass()
    assert my_summation_class.expensive_sum(2, 3) == 5
```

模仿成员函数`expensive_sum`与上面的例子非常相似，但是现在我们只需要使用`mock.patch.object()`，并且记住包括该函数的所有参数，也就是`self`:

```
def test_sum():
    my_summation_class = MySummationClass()
    with mock.patch.object(MySummationClass, "expensive_sum", new=lambda self, x, y: x + y):
        assert my_summation_class.expensive_sum(2, 3) == 5
```

# 结论

在这篇文章中，我们讨论了用 pytest 在 Python 中模拟。我们看到了如何使用上下文管理器和装饰器来修补函数，还讨论了修补的位置。此外，我们还略读了对象的修补函数。感谢阅读！如果你对 C++感兴趣，[本帖](https://medium.com/gitconnected/mocking-with-googletest-gtest-6dde5230e7aa)用 gtest 描述嘲讽。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)