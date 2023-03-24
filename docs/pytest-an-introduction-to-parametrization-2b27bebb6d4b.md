# 一个非常小的单元测试技巧

> 原文：<https://levelup.gitconnected.com/pytest-an-introduction-to-parametrization-2b27bebb6d4b>

## 为你的 Python 包编写*非常*简洁的单元测试指南。

![](img/d65fdf683660013c3c4de26cd1db2680.png)

第一部分将解释随附软件包的结构以及如何在您的系统上设置它。如果您只想查看 PyTest 中参数化的例子，您可以直接跳到第二部分“参数化测试”。

## 要克隆本文附带的源代码，[点击这里](https://github.com/RishabhMalviya/PyTestParametrization_Article)。

# 包装结构和设置

克隆的存储库将包含一个名为`efficient_dedup`的包和一个包含测试用例的`tests`文件夹。

该包旨在 Python 列表中执行高效的重复数据删除(删除重复值)。它分两步完成，首先对列表进行排序，然后使用其中一种重复数据消除算法对排序后的列表进行重复数据消除。注意，这个包只是说明性的；因此，许多代码在功能上是多余的。这是包的结构:

```
efficient_dedup
|
| - efficient_deduplication
| - deduplication_algorithms
| - sorting_algorithms
|
| - tests
    |
    | - test_efficient_deduplication
```

它基于[战略设计模式](https://refactoring.guru/design-patterns/strategy/python/example)。在`efficient_deduplication`模块中定义的`EfficientDeduplicator`类是上下文:

需要来自每个`deduplication_algorithms`和`sorting_algorithms`模块的策略实现。

在`sorting_algorithms`模块中只定义了两个策略行为，它们都封装在一个类`SortingAlgorithm`中；您想要的特定行为可以通过将`reverse`参数设置为`True`或`False`来选择:

`deduplication_algorithms`模块包含四个不同的实现，每个都封装在不同的类中。第一个类`DeduplicationAlgorithm`，充当其他三个实现的基类(这不是实际的代码，因为这里省略了函数的定义):

要运行测试用例，将`cd`放到包的根文件夹中并运行下面的命令:

```
> cd <git-repo-root>/efficient_dedup
> python -m pytest
```

# 参数化测试

所以，我们先总结一下再继续。我们有一个名为`EfficientDeduplicator`的上下文类，它需要两个策略:一个`SortingAlgorithm`和一个`DeduplicationAlgorithm`。这个上下文类只有一个功能，`deduplicate_efficiently`。

我们想用所有可能的策略组合和一些不同的测试数据来测试`deduplicate_efficiently`。正如我们将看到的，如果我们利用参数化，在一个测试用例中完成所有这些是可能的。

## 数据参数化

这是我们可以编写的最基本的测试用例:

假设我们想要用几个不同的可能输入来执行这个测试，例如，变量`list_with_duplicates`的几个不同的值。首先，我们让`list_with_duplicates`成为测试函数中的一个参数。然后，我们通过`@pytest.marker.parametrize`装饰器为该参数提供一个值列表。

这是在 Pytest 中进行参数化的一般程序:

1.  您可以在测试函数的参数列表中指定要参数化的变量
2.  然后使用`@pytest.marker.parametrize`装饰器为变量提供一个值列表。

## 类的参数化

现在，假设我们想要用所有可能的不同的`DeduplicationAlgorithm`实现来运行基本测试，但是保持数据不变。这可以通过 Pytest 参数化、Python 的`inspect`模块以及类在 Python 中是一等公民的事实(意味着它们可以作为函数的参数传递)的巧妙结合来实现。

要获得`deduplication_algorithms`模块中所有类的列表，我们可以导入该模块，然后运行以下代码:

```
print([
    getattr(deduplication_algorithms, name)
    **for** name, obj
    **in** inspect.getmembers(deduplication_algorithms)
    **if** inspect.isclass(obj)
])
```

这为我们提供了模块中的类列表:

```
[
<class 'efficient_dedup.deduplication_algorithms.DeduplicationAlgorithm'>, <class 'efficient_dedup.deduplication_algorithms.HashSetDeduplicationAlgori
thm'>,<class 'efficient_dedup.deduplication_algorithms.CustomComparisonDeduplicationAlgorithm'>,<class 'efficient_dedup.deduplication_algorithms.LookaheadCustomComparisonDeduplicationAlgorithm'>
]
```

然后我们可以将它传递给`@pytest.marker.parametrize`装饰器:

瞧吧。您已经在 Pytest 中实现了类参数化！

## 组合数据和类参数化

Pytest 的一个很好的特性是，如果你在一个测试函数上堆叠了许多`@pytest.marker.parametrize`decorator，那么它会自动运行所有 decorator 的所有可能组合的测试(就像一个集合产品)。最后，我们将使用这个特性在一个测试用例中组合上述两个参数化；事实上，我们还将在`SortingAlgorithm`的`reverse`属性上添加一个参数化:

这是您将要克隆的包中测试用例的最终形式。如果您冗长地运行 Pytest:

```
> python -m pytest -v
```

您将看到如下输出:

```
tests/test_efficient_deduplication.py::TestEfficientDeduplicators::test_deduplicate_efficiently[DeduplicationAlgorithm-True-list_with_duplicates0] PASSED [33%]
```

上面输出中的第二行列出了测试用例中使用的参数的精确组合。

# 结论

使用本文中描述的技术在将来编写更简洁有效的 Python 测试用例！