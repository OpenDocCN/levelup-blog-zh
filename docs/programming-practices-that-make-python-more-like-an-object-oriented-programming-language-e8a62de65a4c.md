# 使 Python 更像面向对象编程语言的编程实践

> 原文：<https://levelup.gitconnected.com/programming-practices-that-make-python-more-like-an-object-oriented-programming-language-e8a62de65a4c>

![](img/fb97095a5a45120da75c9ef0f413a420.png)

照片由[妮可·德·霍斯](https://burst.shopify.com/@ndekhors?utm_campaign=photo_credit&utm_content=Free+Stock+Photo+of+Developer+Coding+On+Laptop+%E2%80%94+HD+Images&utm_medium=referral&utm_source=credit)从[爆出](https://burst.shopify.com/education?utm_campaign=photo_credit&utm_content=Free+Stock+Photo+of+Developer+Coding+On+Laptop+%E2%80%94+HD+Images&utm_medium=referral&utm_source=credit)

作为一个长期使用 Unity 开发游戏的程序员，我使用的编程语言是 C#。众所周知，C#是一种强类型语言，通常使用面向对象的范式进行编程(当然，它还有其他范式)。最近开始用 Python 编程。距离我上次使用 Python 已经过去 5–6 年了，那时候我用的是 Python 2.7。因此，我的印象是 Python 是一种面向过程的动态编程语言，没有类型。

当然，现在我知道我错了。而且我发现下面的做法可以让我这样使用 C#的程序员也非常习惯使用 Python。

# 类型注释和类型注释

我的第一个尝试是让 Python 成为一种强类型语言，至少让它看起来像一种强类型语言。好消息是，从 Python3.5 开始，Python 通过 [**PEP 484**](https://www.python.org/dev/peps/pep-0484) 引入了**类型注释**。

例如，下面是一个简单的函数，其参数和返回类型在注释中声明:

```
def greeting(name: str) -> str:
    return 'Hello ' + name
```

正如你在上面的例子中看到的，类型注释是通过在变量后添加`: <type>`来完成的。对了，如果函数不返回，返回类型应该是`None`。

```
def retry(url: Url, retry_count: int) -> None: ...
```

但是如果类型更复杂呢？例如，类型是元素为 int 的列表。

Python 3.5 中的**类型化**模块可以用于此目的。本模块为 [**PEP 484**](https://www.python.org/dev/peps/pep-0484) 、 [**PEP 526**](https://www.python.org/dev/peps/pep-0526) 、 [**PEP 544**](https://www.python.org/dev/peps/pep-0544) 、 [**PEP 586**](https://www.python.org/dev/peps/pep-0586) 、 [**PEP 589**](https://www.python.org/dev/peps/pep-0589) 、 [**PEP 591**](https://www.python.org/dev/peps/pep-0591) 指定的类型提示提供运行时支持。最基本的支持包括类型`[Any](https://docs.python.org/3/library/typing.html#typing.Any)`、`[Union](https://docs.python.org/3/library/typing.html#typing.Union)`、`[Tuple](https://docs.python.org/3/library/typing.html#typing.Tuple)`、`[Callable](https://docs.python.org/3/library/typing.html#typing.Callable)`、`[TypeVar](https://docs.python.org/3/library/typing.html#typing.TypeVar)`和`[Generic](https://docs.python.org/3/library/typing.html#typing.Generic)`。

在键入的帮助下，您可以像这样添加类型注释:

```
from typing import **List****def** scale(scalar: float, vector: **List[float]) -> List[float]**:
    **return** [scalar * num **for** num **in** vector]
```

不过， [**PEP 484**](https://www.python.org/dev/peps/pep-0484) 主要关注的是函数注释，在 Python 3.6 中通过 [**PEP 526**](https://www.python.org/dev/peps/pep-0526) **增加了一个用于输入变量的语法。**有两种语法变体，有或没有初始化表达式:

```
from typing import Optional
foo**: Optional[int]**  *# No initializer*
bar**: List[str] = []**  *# Initializer*
```

添加类型提示的另一种方式是 [**类型注释**](https://www.python.org/dev/peps/pep-0484/#type-comments) ，这是 Python 2 代码的一种基于注释的注释语法。有一些例子:

```
x = 1  *# type: int*
x = 1.0  *# type: float*
x = True  *# type: bool*
x = "test"  *# type: str*
x = u"test"  *# type: unicode*
```

类型注释是通过在变量后添加`# type:`来完成的。

# 接口/抽象类

Python 标准库`[**abc**](https://docs.python.org/3/library/abc.html#module-abc)`(**A**B**B**ase**C**lass)模块经常被用来定义和验证接口。它定义了一个元类`ABCMeta`和装饰者`@abstractmethod`和`@abstractproperty`。

```
from abc import **ABCMeta, abstractmethod**class AbstractStrategy(**metaclass=ABCMeta**):    
    **@abstractmethod **   
    def algorithm_interface(self):        
        passclass ConcreteStrategyA(**AbstractStrategy**):    
    def algorithm_interface(self):        
        print("ConcreteStrategyA")
```

而另一个替代方案是使用 [**接口**](https://interface.readthedocs.io/en/latest/index.html) 库。它是一个用于声明接口和静态断言类实现这些接口的库。

```
**from** **interface** **import** implements, Interface**class** **MyInterface**(Interface): **def** method1(self, x):
        **pass** **def** method2(self, x, y):
        **pass** **class** **MyClass**(implements(MyInterface)): **def** method1(self, x):
        **return** x * 2 **def** method2(self, x, y):
        **return** x + y
```

# 静态方法

您可以使用`[@staticmethod](https://docs.python.org/3/library/functions.html#staticmethod)`装饰器来定义一个类中的静态函数。静态方法不接收隐式第一个参数(self)。还有一个例子:

```
**class** **C**:
    **@staticmethod**
    **def** f(arg1, arg2, ...): ...
```

现在静态方法`f()`既可以在类上调用(比如`C.f()`)，也可以在实例上调用(比如`C().f()`)。

# 无商标消费品

通过使用上面提到的**类型化**模块，可以在 Python 中使用泛型。类型模块提供了几个通用集合，比如 **List、Mapping、Dict。它们分别是 list、Mapping、dict 的通用版本。**

```
T = **TypeVar**('T', int, float)**def** vec2(x: T, y: T) -> **List**[T]:
    **return** [x, y]**def** keep_positives(vector: **Sequence**[T]) -> **List**[T]:
    **return** [item **for** item **in** vector **if** item > 0]**def** get_position_in_index(word_list: **Mapping**[str, int], word: str) -> int:
    **return** word_list[word]**def** count_words(text: str) -> **Dict**[str, int]:
    ...
```

用户定义的类也可以定义为泛型类。类只需要继承`Generic`。 [**泛型**](https://docs.python.org/3/library/typing.html#typing.Generic) 是泛型类型的抽象基类。例如，一般用户定义的类可以定义为:

```
from typing import TypeVar, Generic
from logging import LoggerT = TypeVar('T')**class** LoggedVar(**Generic[T]**):
    **def** __init__(self, **value: T**, name: str, logger: Logger) -> **None**:
        self.name = name
        self.logger = logger
        self.value = value
```

类`LoggedVar`采用单一类型参数`T`。

然后可以如下使用`LoggedVar`类:

```
T = TypeVar('T')**def** foo(logged: LoggedVar[T]) -> None:
```

好了，通过以上的编程实践，我一个 C#程序员，可以用熟悉的方式用 Python 语言编程了，耶！

感谢阅读，希望这篇文章有用！