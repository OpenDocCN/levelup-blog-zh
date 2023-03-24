# Python:为什么无不是无

> 原文：<https://levelup.gitconnected.com/python-why-none-is-not-nothing-bb3de55dd471>

![](img/f53cc3234364ff8e0fe81838962eccfe.png)

图片由 space.com 拍摄

## 以及没有它该如何生活

这次我们来谈谈 Python 中著名的 **None** ，它的各种形状和使用模式。让我们设法找到没有它也能四处走动的方法。

在我的职业生涯中，我花了几年时间在函数式编程领域。你知道，这种严格意义上的东西。后来，当我回到 Python 时，我注意到他们同时引入了类型注释，这多酷啊？在某个时间点上，我开始深入挖掘，以便发现我是否能够在 Python 中应用一些 FP 概念。

从零开始，我什么也没找到。这就是这个故事的全部内容。-)

这是我最喜欢的 Python 函数:

```
def magic_function():
    # mysterious things happening here
    print("Pure magic!")
```

乍一看，它看起来像一个没有输入也不返回任何内容的函数，因为它没有 return 语句。现在让我们试试奇怪的东西:

```
>>> magic = magic_function()
Pure magic!
```

不出所料，该函数会打印到 stdout，但有趣的是，我们将 magic_function()的结果赋给了 magic 变量。这怎么可能呢？我们如何给一个变量赋值“nothing”(这是函数应该返回的值)？为什么我们没有得到一个错误？

```
>>> magic.__class__
<class 'NoneType'>>>> magic is None
True>>> None.__class__
<class 'NoneType'>
```

自然，博学的 Pythonista 知道每个(！)没有显式 return 语句的函数隐式返回 **None** (除非抛出异常)——这就是我们赋给‘magic’的东西。因此，magic 变成了一个“非类型类”的变量——magic 等同于 None。

如果一个东西有一个身份，即使它被称为**无**，它也不能是无，对吗？反过来，如果一个函数真的什么都不能返回(这实际上已经是一个矛盾了)，那么它怎么能被赋给一个变量呢？一个人怎么能把什么都不分配给什么呢？

好的， **None** 是一个“真实”类型的“真实”实例。似乎是“无不是无”的第一个证据，对吗？

多亏了类型注释，我们可以让函数签名更吸引人:

```
def magic_function() -> None:
    ....
```

magic_function()的用户现在立即知道，她不能期望该函数返回任何有意义的内容。闻起来像是《老友记》里的“空”东西:

```
void magic_function(void) {
    ....
}
```

像这样的结构不应该被称为“函数”，这就是为什么它们被命名为“过程”。

不幸的是，这并不是全部事实，因为 Python 中的 **None** 似乎对每种类型都是有效值。我们可以很容易地证明，通过重新编写我们的魔术函数，只在每月的第一天返回一些魔术:

```
from datetime import datetime
from typing import Optionaldef maybe_magic_function() -> Optional[str]:
    return "magic" if datetime.now().day == 1 else None
```

这里我们定义函数**可选地**返回字符串。但是也允许返回 **None** 。作为一个快速的旁注，在打字之前。可选介绍你必须写:

```
from typing import Uniondef maybe_magic_function() -> Union[str, None]:
    ....
```

从 3.10 版开始，您甚至可以写得更简洁:

```
def maybe_magic_function() -> str | None:
    ....
```

这三种变体都是等价的，它们中的每一种都会让静态类型的棋手如[**【mypy】**](http://mypy-lang.org/)非常高兴。就我个人而言，我更倾向于有这样的怪癖。我来解释一下原因。

**None** 作为返回值常用来表示不同的事物:

1.  函数返回 **None** 或一个由**可选【Any】**表示的具体值- >
2.  函数返回 **None** 表示失败- >由**可选【任何】**表示
3.  函数返回 **None** 表示没有返回值- >由 **None** 表示

在任何一种情况下，返回 **None** 只不过是一种约定。从“无”中不可能得出任何明确的意图。正因为如此，你经常会发现无数的**“如果某样东西不存在”**瀑布(又名末日金字塔)。想想你在职业生涯中写了多少行代码来检查这些不符合常规的东西(参见[十亿美元的错误](https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare/))。

还有另一个微妙之处。我们已经发现，没有一个对象有标识，甚至有类型(NoneType 类)。现在，当注释变量和函数时，我们使用类型，对吗？

比如:

```
def magic(i: int) -> Union[str, None]:
    ...
```

“str”是一个类型，“int”是一个类型，但 **None** 是一个“class NoneType”类型的对象。我们是在混合类型和对象吗？

有趣的是，我们不被允许写:

```
>>> def maybe_magic_function(i: int) -> Union[str, NoneType]:
    ...
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'NoneType' is not defined>>> type(None)
<class 'NoneType'>>>> type(int)
<class 'type'>
```

我们不能使用 NoneType，正如 Python 所说:“名称‘NoneType’未定义”。如果 **NoneType** 未定义，但是调用 type(None)导致<类‘NoneType’>，那么它告诉我们关于 **None** 的什么信息？是否意味着 None 是一个未定义类型的对象？如果一个东西的类型没有定义，它怎么会是对象呢？永无止境的问题…

你看，有很多围绕着**的神话没有** …请随意深入挖掘:-)

无论如何，为了不弄乱所有这些特性，我怎样才能使返回值明确地表示像缺席(即没有)或失败(不同种类)这样的概念呢？我们怎样才能把 None 从我们的词汇中删除，以支持更明确的东西呢？

## 拯救的类型

正如我们所知，Python 的类型系统是动态的，并由 duck-typing 的概念实现(一个对象拥有的方法比具体类型更重要)。Python 中的每个对象都有一个类型，由解释器自动推断出来。随着类型注释和静态类型检查器的引入，添加类型信息给我们一种处理静态类型语言的(虚拟)印象，至少在 IDE 中是这样。

那么，我们如何利用这些优势呢？

**代表缺席**

我们可以用一个叫做**的专用类型来表示缺勤，可能是**，它有两个子类型。**只是**包装了一个具体的值，而**什么都没有**代表不存在。

```
class Maybe(abc.ABC):
    @abc.abstractmethod
    def get_or_else(self, fallback):
        ...class Just(Maybe):
    """Represents a concrete value."""
    def __init__(self, value):
        self.value = value
    def get_or_else(self, fallback):
        return self.valueclass Nothing(Maybe):
    """Represents Nothing"""
    def get_or_else(self, fallback):
        return fallback def magic_function() -> Maybe:
    return Just("magic") if datetime.now().day == 1 else Nothing()
```

我把 a 型叫做**也许是**，这不仅仅是巧合。不可否认，这可能是著名的 Maybe monad 有史以来最幼稚和不完整的实现:-)。只是现在不要关心术语“单子”，我们在这里学习代表缺席的概念:-)。

通过用**也许是**作为结果类型来注释我们的 magic_function()，就没有不确定性的空间了。结果类型要么是**只是**要么是**没有，**完全停止**。**

使用结构化模式匹配(Python ≥ 3.10)，你可以很容易地从一个 **Just** 中解开封装的值，并相应地处理 **Nothing** 的情况。

```
match magic_function():
    case Just(value=magic):
       print(f"{magic=}")
    case Nothing():
       print("no magic")
```

或者，使用 Python < 3.10 中的 **get_or_else()** 方法。

```
result = magic_function()
print(result.get_or_else("no magic")
```

关于 Maybe monad 还有很多内容要讲，但这远远超出了本文的范围。出于好奇，让我参考一下[**returns**package](https://returns.readthedocs.io/en/latest/pages/maybe.html#)，它提供了一个成熟的、完全带类型注释的实现。

**代表“未定义”**

下一个有趣的场景是数学意义上的函数，这些函数只是根据它们的输入集进行了部分定义。我们都知道像被零除、负数的平方根等函数。

考虑一个计算特定日期的日期名称的函数:

```
from datetime import datetimedef dayname(year, month, day):
    return datetime(year, month, day).strftime("%A")>>> dayname(2022, 11, 31)
Traceback (most recent call last):
  ...
ValueError: day is out of range for month
```

在编程语言中，我们通常以异常结束，因为不可能以有意义的方式表示“未定义”，至少在 Python 中没有。

我们也可以在这里使用 Maybe。要么计算返回一个具体的值，否则我们可以认为它是未定义的。作为一个有益的副作用，我们正在摆脱那些讨厌的运行时异常。

```
def dayname(year, month, day) -> Maybe:
    try:
        return Just(datetime(year, month, day).strftime("%A"))
    except ValueError:
        return Nothing()
```

我们甚至可以为自己编写一个方便的装饰器，这样我们就不必用错误处理代码来污染我们的函数:

```
def maybe_undefined(func) -> Callable[..., Maybe]:
    @functools.wraps(func)
    def wrapper(*args, **kwargs) -> Maybe:
        try:
            return Just(func(*args, **kwargs))
        except Exception:
            return Nothing()
    return wrapper
```

有了这些，当我们事先知道函数对于某些输入值是未定义的时，我们可以很好地修饰函数。

```
@maybe_undefined
def dayname(year, month, day):
    return datetime(year, month, day).strftime("%A")
```

**代表故障**

同样，我们使用一个小的类层次来表示成功和失败，我们称之为**结果**。

```
class Result(abc.ABC):
    @abc.abstractmethod
    def get(self):
        ...
    @abc.abstractmethod
    def get_or_else(self, fallback):
        ...class Success(Result):
    """Represents success."""
    def __init__(self, value):
        self.value = value
    def get(self):
        return self.value
    def get_or_else(self, fallback):
        return self.valueclass Failure(Result):
    """Represents failure"""
    def __init__(self, error):
        self.error = error
    def get(self):
        raise self.error
    def get_or_else(self, fallback):
        return fallback def throws(func) -> Callable[..., Result]:
    @functools.wraps(func)
    def wrapper(*args, **kwargs) -> Result:
        try:
            return Success(func(*args, **kwargs))
        except Exception as ex:
            return Failure(ex)
    return wrapper @throws
def dayname(year, month, day):
    return datetime(year, month, day).strftime("%A")
```

**结果**现在封装了失败案例的异常。感觉如何？如果计算成功，您将获得一个**成功**对象，否则将获得一个**失败**对象。这一次，**失败**对象也包含补充信息，该补充信息本身是一个异常对象。

这意味着解包一个**故障**仍然会导致一个异常:

```
>>> dayname(2021, 10, 10).get()
'Sunday'
>>> dayname(2021, 10, 32).get()
Traceback (most recent call last):
  ...
ValueError: day is out of range for month
```

我们可以通过使用 get_or_else()…来避免这种情况

```
>>> dayname(2021, 10, 32).get_or_else('Invalid date entered')
'Invalid date entered'
>>> dayname(2021, 10, 10).get_or_else('Invalid date entered')
'Sunday'
```

还是再次模式匹配…

```
match dayname(2021, 10, 32):
    case Success(value=day):
       print(f"{day=}")
    case Failure(error=failure):
       print(f"{failure=}")
```

再次，看一下 [**返回**包](https://returns.readthedocs.io/en/latest/pages/result.html)，它对**结果**有更好的实现。

顺便说一句:还有一种类型叫做**或者**，它有两个子类型，通常叫做**左**和**右**。它与结果非常相似，不同之处在于右边的**代表计算的快乐路径(如成功),而左边的**代表不快乐的路径——这不一定是例外情况。****

## 现在怎么办？也许，结果也是？

让我总结一下:

**也许**是用来表示一个函数是否返回一个具体的值。作为一个类比，考虑对于某些输入值未定义的数学函数。(BTW:在 Scala 中，Maybe 被称为 Option with Some(value)or None(！)作为具体的子类型)

**结果**通常用于“具体化”异常，并以定义的方式处理它们。例外是有史以来发明的最糟糕、最具破坏性的副作用。如果处理不当，它们会给你带来极大的混乱。所以，去避开他们吧！

**或者**更适合用来表示有两种可能输出的操作，一种比另一种更“快乐”。你也可以认为这是结果的概括。

## 结论

最后一个问题:在所有给出的例子中，你是否发现了一个 **None** 的例子？你没有！所以是的，Python 里有没有 **None** 的生命！

使用具体的类型来表示计算的具体结果，我们能够摆脱 **None** 并且我们甚至能够使异常显式化，因此程序的自然控制流保持不变。

就这样，我把你自己的实验留给你！

查看我关于[单元素管道和面向铁路的编程](https://medium.com/@cini01/python-the-unacceptable-except-fd633c85c3ae)的后续故事

**参考文献**

*   https://returns.readthedocs.io/en/latest/返回—
*   mypy—[http://mypy-lang.org/](http://mypy-lang.org/)