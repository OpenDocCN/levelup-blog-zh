# Python 类型提示/注释和 Mypy

> 原文：<https://levelup.gitconnected.com/python-type-hints-annotations-and-mypy-3695de841952>

![](img/c0117daa33a3d4a3875816a22f0f89f0.png)

比埃尔·莫罗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

每当用 Python 编写的项目变得足够大时，与其他严格类型语言相比，它就会变得相对笨拙。这部分是由于 Python 的动态类型特性。Python 运行时不检查数据类型的一致性。这意味着变量可以在它的整个生命周期中改变它的数据类型，Python 不会检查被传递的对象的数据类型。当处理大型代码库时，这就成了问题，因为大量的错误都可以追溯到数据类型不匹配。

最新的 Python 原生特性套件可用于为您的 Python 项目提供某种类型的自省和断言。

这篇博文将通过类型提示、数据类和第三方库(如 mypy 和 pyright)介绍一些在项目中强制可选类型规则的技术。

一个常规的 Python 函数是:

```
def greeting(name):
    Return “Hello “ + name
```

使用类型注释，您可以指定参数类型以及函数返回类型

```
# filename : typehints1.pydef greeting(name: str) -> str:
    return “Hello “ + name
```

在上面的例子中，我们指出参数“name”应该是字符串类型，并且 ***- > str*** 语法表示该函数应该返回一个字符串。

注意，参数和返回值的函数注释都是完全可选的。Python 没有赋予注释任何特殊的含义或意义。

所以下面的函数仍然有效，不会产生任何错误。

```
# filename : typehints1.pydef greeting2(name: str) -> str:
    # return a list of string instead of string
    return list(“Hello “ + name)
```

Python 所做的只是让这些注释[在对象](https://www.python.org/dev/peps/pep-3107/#accessing-function-annotations)的 __annotations__ 字典中可用。值得强调的是，类型提示是可选的，不会影响 python 运行时，而且 Python [永远是动态类型语言](https://www.python.org/dev/peps/pep-0484/#non-goals)。因此，注释具有意义的唯一方式是，如果它们被第三方库使用，如 [mypy](https://mypy.readthedocs.io/en/stable/getting_started.html) 或 [pyright](https://github.com/microsoft/pyright) 。

所以让我们试用一下 mypy，看看我们的类型提示是否如预期的那样工作。

我们可以通过 pip 安装 mypy:

```
$ python3 -m pip install mypy
```

安装完成后，使用 mypy 工具对这个 python 文件运行它:

```
# filename : typehints1.pydef greeting(name: str) -> str:
    return “Hello “ + namedef greeting2(name: str) -> str:
    return list(“Hello “ + name)
```

使用 mypy 命令评估 typehints1.py 文件。

```
$ mypy typehints1.py>>> typehints1.py:5: error: Incompatible return value type (got “List[str]”, expected “str”)
Found 1 error in 1 file (checked 1 source file)
```

在上面的简单示例中，我们看到 mypy 已经成功检测到 greeting2 函数中的类型注释违规。接下来，让我们深入了解如何使用类型别名来注释非基元数据类型。

```
# filename: typehints2.pyfrom datetime import datetime, date
from typing import List, TuplePersonAge = Tuple[str, int]
ItemList = List[str]
Gift = Tuple[PersonAge, ItemList]def get_name_age(name: str, birth_date: int) -> PersonAge:
    age = datetime.now().year — birth_date
    return (name, age)def pick_items(profile: PersonAge) -> ItemList:
    return [‘dog’, ‘lego’, ‘blanky’]profile = get_name_age(“Asher”, 2018)def get_gift(person: PersonAge) -> Gift:
    items = pick_items(profile)
    return (person, items)print(pick_items(profile))
```

我们可以使用 Python 的[类型模块](https://docs.python.org/3/library/typing.html)来支持最常见类型的类型提示，如列表、可调用、元组、字典和泛型。在示例 typehints2.py 中，我们使用了**类型。元组**和**类型。List** 为元组和列表参数/返回值提供注释。人物是 stringss 和 int 的元组，物品列表是 string 的向量，而 Gift 是人物和物品列表的元组。

用 mypy 计算 typehints2.py 不会返回任何错误，因为所有函数都符合类型提示。但是从上面的例子中，我们可以立即预测，如果我们的数据对象包含许多属性，单独使用类型模块将会变得非常混乱。解决这个问题的一种方法是使用[数据类(Python = > 3.7)](https://docs.python.org/3/library/dataclasses.html) 。

Dataclass 是主要包含数据的类，它是使用@dataclass 运算符创建的，如下例所示:

```
# filename: typehints3.pyfrom dataclasses import dataclass
from datetime import datetime
from typing import List, Tuple@dataclass
class Profile:
    name: str
    age: intdef get_name_age2(name: str, birth_date: int) -> Profile:
    age = datetime.now().year — birth_date
    profile = Profile(name, age)

    return profileprint(get_name_age2(“Asher”, 2018))
```

在 typehints3.py 中，我们创建了一个配置文件数据类，而不是使用类型。元组来描述我们的数据对象。请注意，dataclass 的属性有类型提示；没有类型注释的字段将不会是 dataclass 的一部分。dataclass 中的强制类型提示使它成为描述我们的数据对象的完美替代品，并且它比使用元组或字典更加一致。

作为一个收获，Python 的注释特性将帮助您实现更加一致的逻辑。它允许你对你的代码应该如何工作有一个更严格的描述，它可以作为对单元测试的一个很好的补充。您也可以在现有项目中逐渐集成类型检查，因为第三方工具只会评估带有注释的文件。