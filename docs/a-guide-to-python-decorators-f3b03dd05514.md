# Python 装饰者指南

> 原文：<https://levelup.gitconnected.com/a-guide-to-python-decorators-f3b03dd05514>

## 以及在哪里可以使用它们。

![](img/abe715a4cef5563158a9ed268d2f39e0.png)

由 [Ferenc Almasi](https://unsplash.com/@flowforfrank?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/decorator?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

装饰器是 Python 最酷的特性之一，也是我最喜欢的特性之一。装饰者接受一个函数，添加另一个功能，并返回改进的函数。

但是在我们深入研究它的特性和用途之前，我们必须先了解一些基本的东西。

# 介绍

Python 中的一切都是对象。是的，一切！

函数也不例外。因为它们是对象，所以可以作为参数传递给其他函数，也可以作为结果返回。它们也有一些属性，比如`__name__`和`__doc__`。

函数是执行特定任务的一组语句。它有助于组织我们的代码，避免重复并使代码可重用。

```
def function_name(args):
    '''docstring'''
    statements(s)
```

函数的语法非常简单。它以关键字`def`开头，后面是它唯一的名称、一些参数，最后是一个分号。它可以有一个文档(docstring)来描述这个函数做什么。

后面的所有语句构成了函数体，并且必须有正确的缩进。最后，它还可以包含一个 return 语句。

输出:

```
start of program.
name: basic_function
doc: Basic function doc
basic function: 1
end of program: 2
```

函数也是一个*可调用的*对象。一般来说，可调用对象是可以被调用的东西。您可以使用内置函数`callable()`检查对象是否可调用。

# 装修工

回到装饰师。根据定义，装饰器就是一个简单的函数，它接收一个函数，做一些事情，然后返回另一个函数。

如果您运行此程序:

```
start program
basic function
wrapper function
basic function
end program
```

让我们了解一下这里刚刚发生了什么。`decorator`函数，顾名思义，就是我们的装饰器。它接收一个函数作为参数，称为`func`。是的，非常新颖的名字。在我们的函数内部，我们声明了另一个名为`wrapper`的函数。不用在里面声明，但是很好管理。

这个`wrapper`函数只是调用作为参数传递给装饰器的原始函数，但是您可以添加任何您想添加的功能。

最后返回`wrapper`函数。记住，我们仍然需要一个可调用的对象。现在，可以调用这个新函数了，只需添加我们原来的功能和新代码。

但是 Python 有一个语法来简化这个声明。要装饰一个函数，你可以使用`@`符号和装饰者的名字，并把它放在你想要装饰的函数上面。

输出:

```
decorator func
start of program...
before func wrapped
wrapped func
after func wrapped
end of program
```

您可以将同一个装饰器用于任意数量的函数，也可以用任意装饰器装饰一个函数。

和输出:

```
decorator 2
decorator 1
decorator 1
>> start
before func
before func
basic 1
before func
basic 2
>> end
```

当一个函数有多个装饰器时，它们的调用顺序与声明顺序相反。那就是:

```
@decorator_1
@decorator_2
def wrapped():
```

与以下内容相同:

```
a = decorator_1(decorator_2(wrapped))
```

如果修饰函数返回一个值，而你想保留这个属性，你只需要让包装函数从修饰函数返回这个值。

# 甲级装修工

通过将`__call__`方法添加到一个类中，可以将它变成一个可调用的对象。因为装饰器只是一个函数，因此是一个可调用的对象，所以你可以通过实现函数`__call__`将一个类变成装饰器。

迷惑？让我们看一个例子。

作为输出:

```
> Decorator class __init__
>> start
> before call from class... wrapped
wrapped func
> after call from class
>> end
```

这里的区别在于类是在声明时被实例化的。它应该接收一个函数作为`__init__`方法的参数。这是正在装饰的功能。

当我们调用修饰函数时，我们实际上是在调用类的实例对象。因为对象是可调用的，所以函数`__call__`被调用。

# 带参数的函数

但是如果你试图修饰的函数必须接收一些参数呢？很简单，只需返回一个与你试图修饰的函数具有相同签名的函数。

输出:

```
> decorator args...
> decorator args...
>> start
before calling func add
wrapped 1
after calling func add
ret: 15
before calling func sub
wrapped 2
after calling func sub
ret: 5
>> end
```

关于上课？遵循同样的规则。只需将您想要的签名添加到`__call__`功能中。

输出:

```
> Decorator class __init__
>> start
> before call from class... wrapped
wrapped func: 10 20
> after call from class
>> end
```

如果您不知道签名或者必须接受多种类型的函数，您也可以将`*args`和`**kwargs`用于“包装器”函数。

# 有争论的装饰者

您还可以向装饰器本身传递一个参数。在这种情况下，你必须添加另一个抽象层，即返回另一个包装函数。

这是必要的，因为参数被传递给装饰器。那么这个返回的函数实际上是用来修饰我们想要的函数。同样，这也很容易用一个例子来理解。

带输出:

```
> decorator_with_args: test
>> real decorator for func add
start program
>>> before func add
>>>> add function
>>> after func add
20
end program
```

在类装饰器中，我们必须做类似的调整。现在，类构造函数将接收所有装饰参数。`__call__`方法现在应该返回一个函数，一个实际上执行被修饰函数的包装器。示例:

输出:

```
> Decorator args class __init__: teste
>> start
>>> before wrapper function
add func: 10 20
>>> after wrapper fnction
>> end
```

# 证明文件

函数的一个属性是它的文档字符串(或 docstring)，由`__doc__`属性访问。它是一个字符串常量，被定义为函数定义中的第一条语句。

当修饰时，我们实际上是返回一个新的函数，带有其他属性。但是我们不想改变它们。

在这个例子中，`wrapped`函数实际上是`decorated`函数，因为它已经被它所替代。

```
start of program...
decorated
Decorated function
end of program
```

这就是来自模块`functools`的函数`wraps`来救援的地方。它保留了原始的函数属性。你只需要用它来装饰你的`wrapper`功能。

现在，输出:

```
start of program...
wrapped
Wrapped function
end of program
```

# 应用程序

直到现在，我们还没有在真正的应用程序或真正的用途中真正使用装饰器。但是在哪里可以使用它们呢？让我们看一些例子。

## 时机

一个基本用途是获取函数的处理时间。您可以获得函数调用前后的时间，然后可以根据需要使用这些时间(记录到日志、数据库、调试等等)。

输出:

```
start program
add 10 20 0
>> function add_with_delay processed time (ms): 0.015000000000000001
add 10 20 1
>> function add_with_delay processed time (ms): 1000.7980000000001
end program
```

## 记录

decorator 的另一个常见用途是记录函数的使用。

## 回收

回调函数是在某个事件发生时调用的函数，比如调用了路由、收到了消息或者服务器完成了处理。

然后，您可以添加一个在该事件发生时要执行的函数。例如，这在 HTTP 服务器中用于响应 URL 请求。

## 检查条件

您可以在执行函数之前使用装饰器来检查条件，比如用户是否登录，用户是否有权限使用函数，或者参数是否有效(类型、值等)。

输出:

```
start program
Traceback (most recent call last):
  File "example_permission.py", line 22, in <module>
    do_something()
  File "example_permission.py", line 7, in wrapped_check
    raise ValueError("Not permitted")
ValueError: Not permitted
```

## 创建单例

你可以用一个装饰器来装饰一个类。唯一的区别是装饰器接收的是一个类，而不是一个函数。

singleton 是只有一个实例的类。我们可以将实例保存为包装函数的属性，并在请求时返回它。例如，当我们处理数据库连接时，这是很有用的。

输出:

```
start
140548309536336
140548309536336
end
```

## 错误处理

您可以确保处理一些异常族，而不必为您需要检查每个函数输入一个`try`块。然后，您可以记录或停止程序的执行。

输出:

```
start
5.0
>> Error in func div
0
end
```

# 结论

在本文中，我们看到了 Python 装饰器及其用途。这是一个很好的工具，可以改进您的代码，使您的项目更容易使用和维护。

它们的一些特性:

*   它们可以重复使用。
*   它们可以接收参数并返回值。
*   它们可以存储值。
*   他们可以布置班级。
*   它们可以向其他函数和类添加功能。

Python 也有一些内置的装饰器，比如`classmethod`、`property`和`staticmethod`。

您可以通过其他方法使用 decorators，比如 [dunder](/python-dunder-methods-ea98ceabad15) 方法，来丰富您的类和项目。

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)