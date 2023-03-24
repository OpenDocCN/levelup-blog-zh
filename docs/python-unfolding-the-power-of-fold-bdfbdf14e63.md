# Python:展现折叠的力量

> 原文：<https://levelup.gitconnected.com/python-unfolding-the-power-of-fold-bdfbdf14e63>

![](img/838f8d29f159abddf61eebfd49e37bde.png)

## 或者:如何花式循环

这一次我们将讨论循环以及如何以函数的方式使用它们。“我们为什么要这么做？”，你可能会问。因为我们可以。我们可以用一种非常奇特和灵活的方式做到这一点，只需使用函数和表达式，而不是重复命令语句。

随着深入挖掘，我们将逐渐揭开有史以来最具争议的功能之一。吉多·范·罗苏姆本人曾发帖称:“我一直最讨厌的人…”。所以，我的文章——17 年后——试图把这句话变成类似“我非常欣赏的那句话……”。

注意:本文中给出的示例有意保持尽可能简单，并且可以在您的 Python REPL 中轻松执行。

我假设您听说过内置的 sum()函数:

```
>>> sum([1, 2, 3, 4, 5])
15
```

我们如何编写自己的(简化的)sum 版本呢？

```
def sum_(xs):
    result = 0
    for x in xs:
        result += x
    return result>>> sum_([1, 2, 3, 4, 5])
15
```

简单。如果我们需要 product_()？

```
def product_(xs):
    result = 1
    for x in xs:
        result *= x
    return result>>> product_([1, 2, 3, 4, 5])
120
```

简单。如果我们想编写一个函数，通过连接列表元素的单个字符串表示来创建一个字符串，会怎么样呢？

```
def concat_(xs):
    result = ""
    for x in xs:
        result += str(x)
    return result>>> concat_([1, 2, 3, 4, 5])
'12345'
```

简单。如果我们想把元组列表转换成字典呢？

```
def dictify_(xs):
    result = {}
    for k, v in xs:
        result.update({k: v})
    return result>>> dictify_([("A", 1), ("B", 2), ("C", 3)])
{'A': 1, 'B': 2, 'C': 3}
```

简单。如果……会怎么样？简单。如果…简单…

尽管非常简单，但仔细看看我们的实现，我们会发现，我们一次又一次地产生了许多样板代码。模式是这样的:

```
def some_looping_function(iterable):
    result = some_initial_value
    for item in iterable:
       result = operation(result, item)
    return result
```

我们在编程初级班学过，要把常用的部分分解成一个函数，把可变的部分(一个初始值，一个运算，一个可迭代)作为参数传递，就是为了坚持干原理。让我们这样做:

```
def fold(initial_value, operation, iterable):
    result = initial_value
    for item in iterable:
        result = operation(result, item)
    return result
```

这就是:自豪地介绍我们的超级函数，称为 **fold()** ，作为编写循环的功能性的通用方法。

> 备注:在纯函数世界中，fold() —像任何其他循环一样—将使用递归实现。但是，正如我们所知，Python 和递归不是最好的朋友，所以我们在这里坚持使用命令式 fold()实现。话虽如此，但在本文中您再也不会遇到任何 for 语句了:-)

该函数有 3 个参数。初始值、一个运算和一个迭代。首先，让我们注意一下操作:

```
result = operation(result, item)
```

该操作正好需要(！)2 个参数，并通过以某种方式组合它们来产生一个结果，不多也不少。fold()对 iterable 中的每一项重复调用这个函数，总是将计算结果作为第一个参数传递给下一次迭代。这样，随着迭代的进行，结果会随着时间而“积累”。因为我们需要一些东西来开始，我们用初始值来预置累积状态。

> 因为该操作通过组合两个参数返回单个值，所以它也被称为“reducer”函数。
> 
> 重要提示:reducer 函数从不改变累积状态，它总是返回一个新的(！idspnonenote)状态。)版态！

让我们一步一步地想象 sum_()函数的这个过程。该操作是一个简单的 add()函数:

```
def add(a, b):
    return a + b>>> result1 = add(0,       1) # = 0 + 1
>>> result2 = add(result1, 2) # = 1 + 2
>>> result3 = add(result2, 3) # = 3 + 3
>>> result4 = add(result3, 4) # = 6 + 4
>>> result5 = add(result4, 5) # = 10 + 5
```

我们最终会得到一些方程式。如果我们现在将结果代入它们的右边，我们得到以下表示:

```
>>> result5 = add(add(add(add(add(0, 1), 2), 3), 4), 5)
>>> result5
15
```

或者，反过来，当用相应的“+”运算符替换 add()时，我们得到以下等式:

```
>>> result5 = (((((0 + 1) + 2) + 3) + 4) + 5)
>>> result5
15
```

现在很清楚了，fold()是一个左关联操作，也就是说，最终结果是从左边开始计算的(就像吃豆人从左到右吃怪物……)。这就是为什么 fold()也被称为 foldLeft(在 Scala 中)或 foldl(在 Haskell 中)。

这就是 fold()的全部魔力。我们现在能够根据 fold()将我们的函数重新定义为简单的一行程序:

```
def sum_(xs):
    return fold(0, lambda acc, item: acc + item, xs)def product(xs):
    return fold(1, lambda acc, item: acc * item, xs)def concat_(xs):
    return fold("", lambda acc, item: acc + str(item), xs)def dictify_(xs):
    return fold({}, lambda acc, item: {**acc, item[0]: item[1]}, xs)
```

除了 lambdas，我们可以使用任何其他“可调用”对象，只要它的签名符合 reducer 函数的特征:

```
>>> fold(0, add, [1, 2, 3, 4, 5])
15
```

最后，让我们花点心思在初始元素上。这里，我们要回答两个问题:初始元素的类型是什么，初始元素的值是什么？

通常，初始元素的类型与 reducer 函数所期望的第一个参数的类型相同。类型注释有助于可视化以下内容:

```
from typing import Callable, Iterable, TypeVarA = TypeVar("A")
B = TypeVar("B")def fold(
         initial_value: B,
         reducer: Callable[[B, A], B],
         iterable: Iterable[A]
) -> B:
    ...
```

初始元素的值取决于第一次调用 reducer 函数时预期返回的内容。对于算术运算，就归约函数的运算而言，初始元素通常等于“中性元素”(或单位元素)。

例如，加法的中性元素是 0，因为 0 + x = x。乘法的中性元素是 1，因为 1 * x = x。对于字符串连接，它是空字符串("+ "a string" = "a string ")，而对于列表连接，它是空列表([] + [a，b，c] = [a，b，c])。你明白了。

有时我们想从更复杂的东西开始:

```
def add_mapping(d, item):
   return {**d, item.upper(): ord(item.upper())}>>> fold({"@": 64}, add_mapping, ["A", "b", "C"])
{'@': 64, 'A': 65, 'B': 66, 'C': 67}
```

在这个不可否认是人为设计的例子中，我们用一些值预设了初始字典。

下一个问题可能是:有没有这样的情况，我们不需要一个显式的初始值，只需要 iterable 的第一个元素作为初始值？这是一个有效的问题，答案是:是的，这基本上是我们心爱的 **reduce()** 函数的原始想法，它只不过是 fold()的一种特殊形式。

```
from typing import Callable, TypeVar, IterableB = TypeVar("B")def reduce_(reducer: Callable[[B, B], B], iterable: Iterable[B]):
    it = iter(iterable)
    result = next(it)
    for item in it:
        result = reducer(result, item)
    return result>>> reduce_(add, [1, 2, 3, 4, 5])
15
```

查看类型注释，我们注意到 reduce()只处理一个类型 b。例如，减少 List[int]类型的 iterable 将总是产生一个 int 值。

因此，计算总和(或乘积)完全可以在不显式传递中性元素的情况下进行，而从整数列表创建字符串在没有显式初始值的情况下无法进行:

```
>>> reduce_(lambda acc, item: acc + str(item), [1, 2, 3, 4, 5])
...
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

原因是在 lambda 的初始调用中，acc 的类型是 int(序列中的初始 1)，我们试图添加 str(2)。这正是错误消息告诉我们的。

那么，什么时候指定一个专用的初始值呢？每当结果类型不同于 iterable 的元素类型时，您很可能需要一个与结果类型相同的初始值。

注意，在 Python 中，只有 reduce()可以作为标准 functools 包的成员，但是我们没有 fold()。Python 的 reduce()将初始值作为可选参数，默认值为 None。因此，如果您将一个初始值传递给 reduce()，它的行为类似于 fold()。

从现在开始，我将在代码示例中使用 Python 的原生 functools.reduce()。为了节省空间，我将 functools 作为“f”导入。

```
import functools as f>>> f.reduce(lambda acc, item: acc + item, [1, 2, 3, 4, 5])
15>>> f.reduce(lambda acc, item: acc + str(item), [1, 2, 3, 4, 5], "")
'12345'
```

> 就我个人而言，我更喜欢总是传递一个初始值给 reduce()，因为这使得预期的结果类型更加明确。

我们现在可以总结一下 reduce()的基本属性:

*   reduce 本身的实现非常简单，因为它将“reduce”委托给了另一个函数。这使得 **reduce()** 成为一个高阶函数。
*   reducer 函数通过随后将累积状态与可迭代序列的每一项相结合来计算新的状态。
*   reducer 函数的返回类型与其第一个参数的类型相同。
*   标准 reduce()函数中的初始值是可选的。如果省略，该函数将 iterable 的第一项作为初始值。

# 何时使用 reduce()

一般来说，reduce()可以被认为是一种通用的、函数式的编写循环的方法，这种方法旨在通过迭代一个集合来产生一个结果。

我猜，你听说过**地图()**和**滤镜()**。map()接受一个函数和一个 iterable，将函数应用于 iterable 的元素，并返回一个“投影的”iterable。

相反，filter()接受一个“谓词函数”(返回 bool 的函数)和一个 iterable，并返回一个新的 iterable，其中包含谓词函数包含的元素。

现在，为什么我要将 map()和 filter()与 reduce()结合在一起呢？嗯，和 reduce()一样，两者都是以一个函数和一个 iterable 作为输入。听起来熟悉吗？为了演示 reduce()的一般性质，让我展示如何根据 reduce()实现 map()和 filter()。

下面是列表的 **map()** :

```
from typing import ListA = TypeVar("A")
B = TypeVar("B")def map_(f: Callable[[A], B], seq: List[A]) -> List[B]:
    return f.reduce(lambda state, item: state + [f(item)], seq, [])>>> map_(lambda x: x * x, [1, 2, 3, 4, 5])
[1, 4, 9, 16, 25]
```

还有**滤镜()**:

```
def filter_(
    predicate: Callable[[A], bool], seq: List[A]
) -> List[A]: return f.reduce(
        lambda state, item: 
            state + [item] if predicate(item) else state,
        seq, [])>>> filter_(lambda x: len(x) > 2, ["abc", "de", "fghi"])
['abc', 'fghi']
```

> 注意，在这两种情况下，我都使用一个空列表作为初始值，因为我们想要返回一个列表。

使用 reduce 还可以实现更多众所周知的功能。

**max():**

```
import mathdef max_(seq: Iterable[int]) -> int:
    return f.reduce(
        lambda state, item: item if item > state else state,
        seq, -math.inf)>>> max_([10, 20, 30])
30
```

**反转()**:

```
def reversed_(seq: List[A]) -> List[A]:
    return f.reduce(lambda state, item: [item] + state, seq, [])>>> reversed_([10, 20, 30])
[30, 20, 10]
```

> 同样，当我们创建输入列表的反向版本时，我们将中性元素设置为空列表([] + [n] = [n])。

**accumulate():**

示例摘自[https://docs . python . org/3/library/ITER tools . html # ITER tools . accumulate](https://docs.python.org/3/library/itertools.html#itertools.accumulate)

```
import operator as opdef accumulate_(seq: List[A], f=op.add) -> List[A]:
    return f.reduce(
        lambda state, item:
            [item] if not state else state + [f(state[-1], item)],
        seq, [])>>> accumulate_([1, 2, 3, 4, 5])
[1, 3, 6, 10, 15]>>> accumulate_([1, 2, 3, 4, 5], op.mul)
[1, 2, 6, 24, 120]>>> cashflows = [1000, -90, -90, -90, -90]
>>> accumulate_(cashflows, lambda bal, pmt: bal*1.05 + pmt)
[1000, 960.0, 918.0, 873.9000000000001, 827.5950000000001]
```

**功能组成:**

一个更复杂的例子是用 reduce()实现的函数组合:

```
from typing import AnyC = TypeVar("C")def compose2(
    g: Callable[[B], C], f: Callable[[A], B]
) -> Callable[[A], C]:
    return lambda x: g(f(x))def compose(*f: Callable[[Any], Any]) -> Callable[[Any], Any]:
    return f.reduce(compose2, f, lambda x: x)>>> complexlen = compose(str, complex, len)
>>> complexlen("42")
'(2+0j)'
```

首先，我们定义一个函数 **compose2(g，f)** ，它在 f 之后返回一个新的函数(g . f)“g”。然后，我们使用 reduce()为任意数量的函数定义 **compose()** 。

> 这里，中性元素是恒等函数**λx:x**。用 identity 函数组合一个函数返回原始函数:(f . id) = f

## reduce()的其他应用

我假设你听说过 [ReduxJS](https://redux.js.org/) ，它本质上是基于 reduce()的原理。

MapReduce 中的 Reduce 步骤使用 reducer 函数来合并中间结果。

还有更多…

# 结论

你看，fold()和 reduce()的应用无处不在，reduce()和 fold()显然是以函数方式将可迭代集合的元素转换成其他元素的两把瑞士军刀。

以我的经验来看，减速器功能是人们在接触 reduce()和 fold()时最纠结的东西。这肯定需要一些实践(和 CodeKata)。一旦你有了这个想法，这些功能将成为你功能库中不可或缺的工具。

永远记住，reducer 就像任何其他函数一样，被重复应用于一系列值。所以，绝对不需要憎恨或焦虑。

本文到此为止。我希望我能够揭开 fold()/reduce()的神秘面纱，并阐明它的应用程序和功能。如果你觉得这篇文章有用，请留下你的评论并分享。

# 奖励材料:-)

## 嵌套循环呢？

有人问我如何使用 reduce()实现嵌套循环。这里尝试从纯功能的角度来解决这个问题。考虑如下所示的嵌套循环:

```
result = []
for pfx in range(1, 4):
    for code in range(65, 68):
        result.append(str(pfx) + ":" + chr(code))>>> result
['1:A', '1:B', '1:C', '2:A', '2:B', '2:C', '3:A', '3:B', '3:C']
```

除了这样一个事实，你可以通过写…

```
>>> [str(pfx) + ":" + chr(code)
    for pfx in range(1, 4)
    for code in range(65, 68)]
['1:A', '1:B', '1:C', '2:A', '2:B', '2:C', '3:A', '3:B', '3:C']
```

…让我们尝试使用功能。我们从创建 reducer 操作开始，它只是将一个类似“1:A”的字符串追加到一个列表中:

```
def add_prefix(pfx: int, code: int) -> str:
    return str(pfx) + ":" + chr(code)def prefix_reducer(**pfx**):
    return lambda result, code: result + [add_prefix(**pfx**, code)]>>> f.reduce(prefix_reducer(1), range(65, 68), [])
['1:A', '1:B', '1:C']
```

有趣的是，我们必须在 reducer 函数中注入前缀变量‘pfx ’,但是我们不能有一个带 3 个参数的 reducer 函数。记住:一个减速器只能带 2 个；-)

我们利用‘闭包’！我们不返回值，而是返回 reducer 函数(即 lambda ),它从周围的作用域中捕获变量‘pfx’。

这样，我们现在可以创建一个函数，为单个前缀创建子列表:

```
def range_prefixer(pfx, rng):
    return functools.reduce(prefix_reducer(pfx), rng, [])>>> range_prefixer(2, range(65, 68))
['2:A', '2:B', '2:C']
```

最后，我们需要一个缩减器，它从 range_prefixer()调用生成的列表中生成一个列表。

```
def range_reducer(**rng**):
    return lambda state, pfx: state + range_prefixer(pfx, **rng**)>>> functools.reduce(
        range_reducer(range(65, 68)), 
        range(1, 4), 
        [])
['1:A', '1:B', '1:C', '2:A', '2:B', '2:C', '3:A', '3:B', '3:C']
```

乍一看，要将一个 4 行嵌套的 for 循环转换成一些功能性的东西还真不容易，是吧？但是，我们获得了许多可重复使用的(！)就纯函数而言的功能性，每个函数都是一行程序，具有清晰的功能性，很容易推理，并且可以很好地测试。

```
def add_prefix(pfx: int, code: int) -> str:
    return str(pfx) + ":" + chr(code)def prefix_reducer(**pfx**):
    return lambda result, code: result + [add_prefix(**pfx**, code)]def range_prefixer(pfx, rng):
    return f.reduce(prefix_reducer(pfx), rng, [])def range_reducer(**rng**):
    return lambda state, pfx: state + range_prefixer(pfx, **rng**)
```

还要注意，一切都只是表达式，没有语句(除了 return)。

下面是进行嵌套计算的最后一个单行函数:

```
def nested_prefixer(prefix_rng, code_rng):
    return f.reduce(range_reducer(code_rng), prefix_rng, [])>>> nested_prefixer(range(1, 4), range(65, 68))
['1:A', '1:B', '1:C', '2:A', '2:B', '2:C', '3:A', '3:B', '3:C']
```

通过将 add_prefix()函数作为参数注入 nested_prefixer()，我们可以使我们的解决方案更加灵活，这样我们就可以参数化格式，例如“1__A”或“1xA”或其他任何格式。我将此作为读者的一个练习；-)