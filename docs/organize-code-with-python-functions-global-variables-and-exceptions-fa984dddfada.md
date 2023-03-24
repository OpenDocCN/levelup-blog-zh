# 使用 Python 函数组织代码-全局变量和异常

> 原文：<https://levelup.gitconnected.com/organize-code-with-python-functions-global-variables-and-exceptions-fa984dddfada>

![](img/45dcb9d2fd698dae9b3e9da1ed81f750.png)

由[Louis Hansel @ shotsoflouis](https://unsplash.com/@louishansel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 是一种方便的语言，通常用于脚本编写、数据科学和 web 开发。

在本文中，我们将看看如何用关键字`global`在函数中定义全局变量并处理异常。

# 全球声明

关键字`global`让我们可以在任何地方定义全局变量。例如，我们可以如下使用它:

```
def foo():
  global x
  x = 1
foo()
print(x)
```

上面的代码有`foo`函数，用`global x`行定义全局变量`x`。

然后我们把`x`赋值为 1。

然后在我们调用`foo`之后，我们可以访问最后一行的`x`，因为我们用函数调用声明了全局变量`x`。

它们在被定义后必须被引用。

所以如下:

```
print(x)
def foo():
  global x
  x = 1
foo()
```

会给我们一个错误，说`'x'`未定义。

# 异常处理

我们可以一起使用`try`和`except`关键字来处理由某些代码段引发的错误。

我们所要做的就是用`try`和`except`包围一段引发错误的代码。然后在`except`之后，我们运行代码来处理错误。

例如，我们可以用它来捕捉除以零的错误，如下所示:

```
def divide(dividend, divisor):
  try:
    return dividend / divisor
  except ZeroDivisionError:
    print("Can't divide by 0")divide(1, 0)
```

当我们在`divisor`设置为 0 的情况下调用`divide`函数时，我们会看到`'Can't divide by 0'`消息，而不是返回被`divisor.`除的`dividend`

如果我们用一个非零除数调用它，我们得到的是`divisor`除以`dividend`。

我们还可以使用`except`块来捕捉多种错误。

例如，我们可以这样做:

```
try:
  num = int(input("Please enter a number: "))
except (RuntimeError, TypeError, NameError, ValueError):
  print("Not a number")
```

上面的代码捕捉到了`RuntimeError`、`TypeError`、`NameError`和`ValueError`。

# 引发异常

我们也可以用关键字`raise`在代码中引发异常。

例如，我们可以编写以下代码来引发代码中的异常:

```
try:
  name = input("Please enter a name: ")
  if name != 'Jane':
    raise ValueError
except ValueError:
  print("You're not Jane")
```

如果`name`的值不是`'Jane'`，代码使用`raise`关键字来引发一个`ValueError`。

然后我们在`except`街区抓住它。

![](img/f2f415ec47a9fa890f38bba4b2318971.png)

照片由[艾莉·史密斯](https://unsplash.com/@creativegangsters?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 创造我们自己的例外

我们可以通过扩展`Exception`类来创建自己的异常类。

如果一个异常类与给定的异常类是同一个类或基类，那么它就是兼容的。

例如，我们可以创建自己的`FooError`类，如下所示:

```
class FooError(Exception):
  passtry:
  raise FooError     
except FooError:
  print("Foo error")
```

在上面的代码中，我们创建了一个从`Exception`类派生的`FooError`类。

然后我们像处理内置错误一样使用它。

# 定义清理活动

我们可以使用`finally`块来运行任何清理代码，不管是否抛出异常。

例如，我们可以编写以下代码，在出现异常后打印一条消息:

```
try:
  raise ValueError
finally:
  print('Goodbye, world!')
```

我们看到`‘Goodbye, world!’`显示，即使在`ValueError`升高之后。

# 预定义的清理操作

一些对象有标准的清理动作。我们可以用关键字`with`运行这些函数。

例如，打开文件的`open`函数具有预定义的清除动作。

例如，我们可以如下调用`open`函数:

```
with open("foo.txt") as f:
  for line in f:
    print(line, end="")
```

那我们就不用写什么清理代码了。

# 结论

我们可以用`global`关键字在任何地方定义全局变量。

为了捕捉异常，我们用`try`和`except`包围可能引发异常的代码。

我们可以使用`raise`关键字引发异常。为了创建我们自己的类，我们可以创建一个继承自`Exception`的类。

为了运行清理代码而不管是否抛出异常，我们可以使用`finally`块。

像`open`这样的功能已经预定义了清理动作。我们可以使用`with`语句在没有`finally`的情况下运行带有清理动作的那些函数。