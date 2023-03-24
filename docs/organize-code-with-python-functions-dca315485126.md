# 用 Python 函数组织代码

> 原文：<https://levelup.gitconnected.com/organize-code-with-python-functions-dca315485126>

![](img/90eb61f77508c43d628e674832a85e5d.png)

杰夫·谢尔登在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 是一种方便的语言，通常用于脚本编写、数据科学和 web 开发。

在本文中，我们将了解如何定义和使用 Python 函数。

# 功能

函数是我们可以在不同地方重复调用的一段代码。

用关键字`def`定义如下:

```
def greet(name):
  print('Hello', name)
```

上面的`greet`函数有一个参数。我们可以传入一个参数，在调用时设置参数的值。

函数总是以`def`关键字开始，然后是函数名，然后是括号，里面有零个或多个参数。然后第一行以冒号结尾。

函数的代码缩进在函数块中。

例如，我们可以如下调用`greet`:

```
greet('Joe')
```

然后我们看到:

```
Hello Joe
```

显示在屏幕上，因为我们把`'Joe'`作为`greet`函数调用的参数。

函数可以调用其他函数。例如，我们可以编写以下代码，让我们的`greet`函数调用另一个函数:

```
def greet(first_name, last_name):
  print('Hello', full_name(first_name, last_name))def full_name(first_name, last_name):
  return '%s %s' % (first_name, last_name)greet('Jane', 'Smith')
```

在上面的代码中，我们的`greet`函数调用了`full_name`函数，该函数返回由`first_name`和`last_name`组合而成的全名。

在`full_name`函数中，我们使用`return`关键字返回将`first_name`和`last_name`参数组合成一个字符串的计算结果。

每当我们使用`return`关键字时，该函数就会结束。它下面什么都不会跑。

因此，我们可以如下使用它，通过使用多个`return`语句返回我们想要有条件返回的值，如下所示:

```
import randomdef crystal_ball(num):
  if num == 1:
    return 'It is a great day'
  elif num == 2:
    return 'It is a lucky day'
  elif num == 3:
    return 'It is an auspicious day'r = random.randint(1, 4)
fortune = crystal_ball(r)
print(fortune)
```

在上面的代码中，每当`num`的值为 1、2 或 3 时，我们都有`if`语句返回一些东西。

如果`num`为 1，那么`crystal_ball`返回`'It is a great day'`。

如果`num`为 2，则`crystal_ball`返回`'It is a lucky day'`。

如果`num`是 3，那么`crystal_ball`返回`‘It is an auspicious day’`。

一旦有东西返回，`crystal_ball`函数就停止运行。

然后，我们可以将返回值赋给另一个变量进行存储，就像我们写的那样:

```
fortune = crystal_ball(r)
```

然后我们打印出`fortune`的值。

# 无值

在 Python 中，我们用值`None`来表示没有值。`None`有类型`NoneType`。

`None`有资本`N`。

我们可以用它作为一个不应该有值的东西的返回值。

例如，`print`返回`None`,因为没有任何东西可以返回。它只是在屏幕上打印一个值。

如果我们写:

```
foo = print('Hello!')
print(None == foo)
```

在上面的代码中，我们应该看到:

```
Hello!
True
```

因为`print`返回`None`，所以分配给`foo`的值将是`None`。

因此，`None == foo`返回`True`。

# 关键字参数

Python 函数可以有命名参数。这样，当我们传递参数时，我们就知道参数设置了什么值。

例如，我们可以按如下方式传入命名参数:

```
def full_name(first_name, last_name):
  return '%s %s' % (first_name, last_name)print(full_name(first_name='Jane', last_name='Smith'))
```

在代码中，我们通过编写来调用`full_name`:

```
full_name(first_name='Jane', last_name='Smith')
```

现在我们知道我们正在传递`'Jane'`作为`first_name`参数的值，传递`'Smith'`作为`last_name`参数的值。

![](img/f2b7c71c6f27ac7da756d7335ab5a734.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 调用堆栈

调用栈是一种数据结构，它告诉我们按照调用的顺序调用了什么函数。

最早调用的函数在栈底，后面调用的在栈顶。

例如，在我们前面的例子中:

```
def greet(first_name, last_name):
  print('Hello', full_name(first_name, last_name))def full_name(first_name, last_name):
  return '%s %s' % (first_name, last_name)greet('Jane', 'Smith')
```

我们的调用堆栈将在底部有`greet`函数，在顶部有`full_name`函数。

# 本地和全球范围

函数内部的变量有一个局部范围。它只在函数和嵌套函数中可用。

它们不能在函数之外被引用。

Python 文件顶层的任何东西都有全局范围。

具有全局作用域的变量可以在位于较低函数(如块或函数)中的任何地方被访问。

例如，如果我们有:

```
x = 1def show_x():
  print(x)show_x()
```

然后我们可以在`show_x`函数中引用`x`，因为`x`具有全局范围。

另一方面，如果我们有:

```
def show_x():
  x = 1print(x)
```

我们会得到一个错误，说`x`没有被定义。

有了作用域，我们就可以缩小可能导致 bug 的代码范围。我们不必搜索整个程序来解决所有问题。

# 结论

函数用于将代码组织成可重用的片段。它们带参数，我们通过传入参数来调用它们。

他们可以返回带有`return`关键字的东西。函数的返回值可以赋给其他变量。

函数内部的变量有一个局部范围。不能从函数外部访问它们。

另一方面，顶层的变量具有全局范围，可以在函数内部访问。