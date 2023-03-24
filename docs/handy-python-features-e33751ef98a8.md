# Python 中的装饰器和函数——方便的 Python 特性(第 1 部分)

> 原文：<https://levelup.gitconnected.com/handy-python-features-e33751ef98a8>

## __ 我们如何使用装饰者 _ _。巴拉圭

![](img/f51614d73d4c4330118717b9a8984de2.png)

Python 是一种强大的语言，有许多方便的内置特性。装修工就是其中之一。您可能见过 Python 代码中的装饰器，如下例所示:

正在使用的装饰器:@staticmethod 是一个装饰器

装饰器是包装其他函数并改变其行为的函数。Python 用 [**PEP-0318**](https://www.python.org/dev/peps/pep-0318/) 描述装饰者。在讨论 decorators 是什么以及如何使用它们之前，我们需要了解 Python 中的函数是什么以及 Python 处理它们的方式。

# 功能

简单地说，一个函数可以接受参数并使用它们完成工作。例如，上面示例中的`print`函数将一个字符串输入作为参数，并将其打印到控制台。也有不接受任何参数的函数。有些函数返回输出，有些不返回。

## 内部函数

Python 看到关键字`def`就定义了一个函数。也允许在函数内部定义函数，如下面的代码所示。这些被称为内部函数。

内部函数:print_greeting()函数中的两个内部函数定义

函数`print_greeting()`有两个内部函数。其中一个在被调用时基于参数`uppercase`被调用。

## 一等品

python 中的函数被视为一级对象。因此，函数可以作为参数传递给另一个函数，就像字符串、整数或任何其他对象一样。下面的示例演示如何将函数传递给函数。

函数是一级对象:print_hi()作为参数传递

函数`run_any_function(function)` 将任意一个函数作为参数并调用它。这里它采用了函数`print_hi()`。

函数也可以作为输出从函数返回:

函数是一级对象:get_capable_function()返回一个函数

函数`get_capable_function(uppercase)` 根据参数`uppercase`返回一个函数。如果参数为真，则返回函数`print_text_uppercase(text)` ，其他情况下返回函数`print_text(text)` 。

现在我们有了讨论装饰者如何工作的背景。

# 装修工

装饰器是包装一个函数并改变其行为的函数。这些函数接受一个函数作为参数。它们定义了一个内部函数，可以调用作为参数的函数来改变其行为。内部函数作为输出返回:

装饰者:print _ hi _ and _ name(name _ only _ function)是一个装饰者

上面的例子展示了一个基本的装饰器。函数`print_hi_and_name(name_only_function)` 以函数`print_name()` 为自变量。它由内部函数`add_hi()`调用，该函数通过打印“Hi”来改变行为。内部函数被返回，因此函数`print_name`可以用装饰函数重新声明。

## @语法

有一种更简洁的方法可以获得相同的结果，而不是像上面例子中的第 12 行那样重新声明函数。

Decorators : print_name()是用@print_hi_and_name 修饰的

## 带参数的函数

函数`print_name()` 打印硬编码值。假设我们需要将一个名字作为参数传递给那个函数，新函数变成了`print_name(name)` 现在是**。但是调用它的内部函数不知道该函数所需的参数。还应相应地更改它，否则会产生错误:**

Decorators : print_name(name)用@print_hi_and_name 装饰

上面示例中的内部函数`add_hi()` 被修改为`add_hi(name)` ，这使得它能够接受参数`name`，然后该参数可以传递给函数`print_name(name)`。

我们还可以将函数`print_hi_and_name(name_only_function)`更改为`print_greeting_and_name(name_only_function, greeting)`，这样就可以采用额外的参数`greeting`，如下所示:

装饰者:用@ print _ greeting _ and _ name(greeting)装饰 print_name(name)

## *args 和**kwargs 形式的参数

假设我们需要修改函数`print_name(name)` 来在名字前打印一个称呼。修改后的函数将接受一个新的参数`salutation`。

以前，我们必须修改内部函数来获取修饰函数所需的参数。新的论点也需要同样的东西。这是不被接受的，也不是好的做法。装饰者不应该知道应该传递给被装饰函数的参数。

我们可以用一个小技巧。窍门是`***args**` 和`****kwargs**` **。**函数`add_greeting(name)` 可以改成`add_greeting(*args,**kwargs)` 然后函数`name_only_function(name)`可以调用为`name_only_function(*args,**kwargs)` 如下。我们现在可以将功能`print_name(name)` 改为`print_name(salutation, name)`:

装饰者:用@ print _ greeting _ and _ name(greeting)装饰 print_name(salutation，name)

# 一份申请

像 Java 这样的语言有访问修饰符，用来控制方法的范围。可以通过使用关键字`private`使方法私有。但是 Python 没有这个概念。

我们可以写一个装饰器，使类中的私有函数不能被外部访问。我们只需要检查私有函数是从类的外部还是内部调用的。

装饰者:@private_function 是一个定制的装饰者

装饰器可用于隔离私有函数，如下所示:

decorator:@ private _ function 用于防止私有方法被外部调用

用`**@**private_function`修饰的函数只允许在类内部调用。如果从外部调用这些函数，则会引发异常。

Decorators:调用私有函数时会引发错误

您可以在以下位置找到 Git 存储库。

[](https://github.com/vishwaefor/handy-python-features) [## vishwaefor/handy-python-特性

### 便捷的 Python 特性。通过在 GitHub 上创建帐户，为 vishwaefor/handy-python-features 开发做出贡献。

github.com](https://github.com/vishwaefor/handy-python-features) 

# **延伸阅读……**

装修工得心应手。它们也可以和类一起使用。单例模式可以使用 decorators 来实现。我建议你多读一些关于那个的文章。