# 我在为熊猫做贡献时学到的高级 pytest 技术

> 原文：<https://levelup.gitconnected.com/advanced-pytest-techniques-i-learned-while-contributing-to-pandas-7ba1465b65eb>

![](img/5d242cf4322d14884ebc87d555301bbf.png)

在过去的几个月里，我向`[pandas](https://pandas.pydata.org/)`贡献了相当多的 PRs，这是 Python 优秀的数据处理生态系统核心的开源数据分析和操作库。

由于`pandas`已经有 10 多年的历史了，包含了超过 50 万行代码，没有人能够知道一个简单的代码变化的所有后果。因此，我们需要依靠我们的测试套件来避免不断破坏数百万用户代码的风险，并且广泛覆盖角落和边缘案例是至关重要的。

在本文中，我想分享我在那段时间学到的一些高级 pytest 特性。我假设对`[pytest](https://docs.pytest.org/en/stable/)`有基本的了解，包括夹具、常规参数化和测试异常。如果你不熟悉这些，我推荐你先看看 realpython.com 的 pytest 教程。

# **参数化您的夹具**

我非常喜欢测试中的参数化。这是一种测试多种配置的优雅而简洁的方法，目的是找出程序中的错误，增强对应用程序的信心。我不知道的是，你甚至可以直接参数化 fixtures，而不仅仅是测试用例。

例如，我们广泛使用我们钟爱的`[index](https://github.com/pandas-dev/pandas/blob/master/pandas/conftest.py#L388-L422)`夹具，它为我们提供了各种不同的`Index`实例:

```
indices_dict = {
    **"unicode"**: tm.makeUnicodeIndex(100),
    **"string"**: tm.makeStringIndex(100),
    **"datetime"**: tm.makeDateIndex(100),
    **"datetime-tz"**: tm.makeDateIndex(100, tz="US/Pacific"),
    **"period"**: tm.makePeriodIndex(100),
    **"timedelta"**: tm.makeTimedeltaIndex(100),
    **"int"**: tm.makeIntIndex(100),
    **"uint"**: tm.makeUIntIndex(100),
    **"range"**: tm.makeRangeIndex(100),
    **"float"**: tm.makeFloatIndex(100),
    **"bool"**: tm.makeBoolIndex(10),
    **"categorical"**: tm.makeCategoricalIndex(100),
    **"interval"**: tm.makeIntervalIndex(100),
    **"empty"**: Index([]),
    **"tuples"**: MultiIndex.from_tuples(zip(["foo", "bar", "baz"], [1, 2, 3])),
    **"mi-with-dt64tz-level"**: _create_mi_with_dt64tz_level(),
    **"multi"**: _create_multiindex(),
    **"repeats"**: Index([0, 0, 1, 1, 2, 2]),
}

@pytest.fixture(params=indices_dict.keys())
def index(request):
    *"""
    Fixture for many "simple" kinds of indices.
    These indices are unlikely to cover corner cases, e.g.
        - no names
        - no NaTs/NaNs
        - no values near implementation bounds
        - ...
    """* # copy to avoid mutation, e.g. setting .name
    return indices_dict[request.param].copy()
```

让我们用一个小例子来实现这一点:运行下面的测试

```
def test_indices(index):
    assert isinstance(index, pd.Index)
```

生产

```
test_example.py::test_indices[unicode] PASSED
test_example.py::test_indices[string] PASSED
test_example.py::test_indices[datetime] PASSED
test_example.py::test_indices[datetime-tz] PASSED
test_example.py::test_indices[period] PASSED
test_example.py::test_indices[timedelta] PASSED
test_example.py::test_indices[int] PASSED
test_example.py::test_indices[uint] PASSED
test_example.py::test_indices[range] PASSED
test_example.py::test_indices[float] PASSED
test_example.py::test_indices[bool] PASSED
test_example.py::test_indices[categorical] PASSED
test_example.py::test_indices[interval] PASSED
test_example.py::test_indices[empty] PASSED
test_example.py::test_indices[tuples] PASSED
test_example.py::test_indices[mi-with-dt64tz-level] PASSED
test_example.py::test_indices[multi] PASSED
test_example.py::test_indices[repeats] PASSED
```

那不是很方便吗？我们可以编写一个测试，就像我们使用一个常规的 fixture 一样，每个 fixture 值`pytest`自动执行一次！

在 pandas，这很好，因为我们提供了大量不同的指数，这些指数有时(由于历史或性能原因)不共享一个通用的实现。参数化的夹具让我们不必对此想太多。如果一个测试用例应该适用于所有的索引，我们可以使用`index` fixture 并自动覆盖所有的索引类型。这很有效:通过在我们的测试套件中应用更多的参数化夹具，我实际上发现了我们没有覆盖的 bug。

我不认为参数化 fixtures 对于每个代码库都是必不可少的，但是在以下两种情况下它们会派上用场:

1.  您希望确保无法仅通过继承来保证的通用功能(就像上面的例子)。
2.  您拥有在多个测试中使用的公共参数，例如，多个功能接受相同的参数([见此处](https://github.com/pandas-dev/pandas/blob/master/pandas/conftest.py#L147-L253))。

*注意:即使参数化夹具有多个值，你也应该给它们一个单数名称。*

# 在参数化中设置 id

另一个(相关的)我以前没有使用的特性是在参数化中指定`ids`。大多数时候这是不必要的，因为`pytest`会找到好的默认值。然而，有时这些默认值很难读懂或者不够简洁。举以下例子:

```
@pytest.mark.parametrize("obj", [pd.Index([]), pd.Series([])])
def test_with_ids(obj):
    assert obj.empty
```

运行该测试会导致

```
test_example.py::test_with_ids[**obj0**] PASSED                                                                                                      
test_example.py::test_with_ids[**obj1**] PASSED
```

万一将来其中一次运行失败，您总是需要通过计算参数化值来导出失败的配置。也就是说，如果`obj1`失败，你必须搜索第二个参数化值。虽然这种方法有效，但它相当烦人。一个更好的替代方法是手动覆盖 id

```
@pytest.mark.parametrize(
    "obj",
    [pd.Index([]), pd.Series([])]**,
    ids=["Index", "Series"]** )
def test_with_ids(obj):
    assert obj.empty
```

或者传递一个可调用函数来隐式派生 id

```
@pytest.mark.parametrize(
    "obj",
    [pd.Index([]), pd.Series([])]**,
    ids=lambda x: type(x).__name__** )
def test_with_ids(obj):
    assert obj.empty
```

两种方法都产生

```
test_example.py::test_with_ids[**Index**] PASSED                                                                                                      
test_example.py::test_with_ids[**Series**] PASSED
```

*注意:上面的例子集中于参数化测试，但是当参数化夹具时，它以同样的方式工作。*

# 在测试中调用 skip 或 xfail

通常，当您想要`skip`或`xfail`一个测试时，您会使用`pytest`优雅的修饰语法。但是，有时您需要检查的条件只有在运行时才可用。在这种情况下，您也可以在测试逻辑内部调用`pytest.skip`或`pytest.xfail`:

```
def test_indexing(index):
    if len(index) == 0:
        pytest.skip("Test case is not applicable for empty data.")
    index[0]
```

这产生了

```
test_example.py::test_indexing[unicode] PASSED
test_example.py::test_indexing[string] PASSED
test_example.py::test_indexing[datetime] PASSED
test_example.py::test_indexing[datetime-tz] PASSED
test_example.py::test_indexing[period] PASSED
test_example.py::test_indexing[timedelta] PASSED
test_example.py::test_indexing[int] PASSED
test_example.py::test_indexing[uint] PASSED
test_example.py::test_indexing[range] PASSED
test_example.py::test_indexing[float] PASSED
test_example.py::test_indexing[bool] PASSED
test_example.py::test_indexing[categorical] PASSED
test_example.py::test_indexing[interval] PASSED
**test_example.py::test_indexing[empty] SKIPPED**
test_example.py::test_indexing[tuples] PASSED
test_example.py::test_indexing[mi-with-dt64tz-level] PASSED
test_example.py::test_indexing[multi] PASSED
test_example.py::test_indexing[repeats] PASSED
```

*注意:虽然这种可能性很大，但它也有不利的一面，那就是建立一个在结果中被有效忽略的测试运行。因此，装饰语法应该总是首选(如果可能的话)，因为它已经可以在测试集合中进行评估。*

# 间接参数化

有时，您只想使用夹具参数化值的子集。例如，如果您想使用`index`夹具，但您只想使用它的`MultiIndex`值，您会怎么做？那么你有以下选择:

1.  使用新的参数化或夹具(→多余)
2.  使用明确的`pytest.skip`(→创建未使用的测试运行)
3.  使用间接参数化

我们已经讨论了第 2 点，让我们来看看选项 3:

```
@pytest.mark.parametrize(
    "index",
    ["mi-with-dt64tz-level", "multi"],
    indirect=True
)
def test_dummy(index):
    assert isinstance(index, pd.MultiIndex)
```

语法一开始可能有点让人不知所措，但实际上很有意义(像往常一样):首先，将参数化的 fixture 添加到签名中。然后，将参数化添加到您指定的测试函数中

1.  要间接参数化的夹具
2.  您要选择的值
3.  将`indirect`标志设置为`True`

这会导致以下两次测试运行:

```
test_example.py::test_multiindex[mi-with-dt64tz-level] PASSED
test_example.py::test_multiindex[multi] PASSED
```

我认为这种方法的主要缺点是它要求您手动指定 fixture 值。因此，如果我们向我们的`index`夹具添加一个新的`MultiIndex`值，间接参数化不能自动选取它。因此，您必须权衡使用这三种方法中的哪一种。

如果你想深入这个话题，我推荐你阅读 pytest-tricks 上的这篇文章，这篇文章提供了更多的背景知识。

# pytest.raises vs nullcontext

本文的最后一部分讨论了在处理参数化时可能会遇到的冗余。更具体地说，当您测试只在一些参数化值中出现，而在其他参数化值中没有出现的异常时。举以下例子:

```
@pytest.mark.parametrize(
    "obj, raises",
    [
        ([], False),
        (pd.Index([]), True),
    ],
)
def test_exception(obj, raises):
    if raises:
        with pytest.raises(ValueError):
            # you should also test the error message, using
            # the 'matches' parameter in .raises()
            # I don't have enough space here though...
            bool(obj)
    else:
        bool(obj)
```

当测试变得比这个最小的例子更大时，调用`bool(obj)`两次的冗余可能会很烦人。

经过一番挖掘之后，我在 pytest GitHub 页面中偶然发现了[这条线索，有人最终建议使用`contextlib.nullcontext`:](https://github.com/pytest-dev/pytest/issues/1830)

```
from contextlib import nullcontext

@pytest.mark.parametrize(
    "obj, expected_raises",
    [
        ([], nullcontext()),
        (pd.Index([]), pytest.raises(ValueError)),
    ],
)
def test_exception2(obj, expected_raises):
    with expected_raises:
        bool(obj)
```

*注意:* `*contextlib.nullcontext*` *仅在 Python3.7 之后可用。如果您使用的是更早的版本，* [*请在提到的线程*](https://github.com/pytest-dev/pytest/issues/1830#issuecomment-425653756) *中查看此评论。*

但是，请不要过度使用这个特性。虽然对于简单的用例来说，这是一种优雅的方式，但它也鼓励您对不同的行为使用参数化，而单独的测试会更合适。

# 结论

虽然还有很多更棒的`pytest`特性我没有谈到，但我希望你和我一样喜欢探索这些特性。

为开源项目做贡献通常是发现和学习新概念或新技术的好机会，同时也是回报社区的好机会。因此，如果您还没有，请继续检查您感兴趣的或已经使用的开源项目中尚未解决的问题。这对你和其他人来说都是双赢的；)

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)