# Python 中的装饰者:他们是什么以及如何使用他们

> 原文：<https://levelup.gitconnected.com/decorators-in-python-what-they-are-and-how-to-use-them-e315e68ecb08>

## @不仅仅是电子邮件和社交媒体

![](img/8f63837f58efca84a25c10d01ae93728.png)

图片来源:Pexels.com 的奥列格·马尼

## 什么是室内设计师？

在 Python 中，装饰器是一个包装另一个函数的函数。它的目的是为它包装的函数提供额外的功能。

这听起来令人困惑。让我们看一个例子。

假设你写了一个将两个数相加的函数。您还在那里放了一个 doc 字符串，因为您很棒，并且希望帮助任何使用您的函数的人理解它的作用。

```
**def add(a, b):**
    """
    Takes two parameters, a and b, and returns their sum.
    """
    **return** a + b
```

您运行它，看看它是否能正确地将 1 和 3 相加。

```
add(1, 3)**[Out]:** 4
```

是的，所以你决定让我接触`add()`。我没看过你的代码，想知道你的函数是做什么的。我在上面打`help()`。

```
help(add)**[Out]**: Help on function add in module __main__: add(a, b)
          Takes two parameters, a and b, and returns their sum.
```

谢谢你！好的文档对于好的编码是必不可少的。一会儿我将向您展示一个涉及装饰者的有趣用例。

## 让我们为你的功能计时

你决定想知道`add()`跑多快。方便的是，Python 在其内置的`time`库中包含了一个计时器。您需要为您的代码导入它。

```
**from** time **import** perf_counter
```

为了给你的函数`add()`计时，你可以写这样的代码:

```
start = perf_counter()
add(1, 3)
end = perf_counter()
print(f'Run time: {(end — start):0.8f} seconds')**[Out]:** Run time: 0.00011559 seconds
```

那相当快。

注意`start`和`end`环绕`add()`函数。这种布局使这些语句成为装饰器(也称为包装器)的理想选择。

## 让我们做一个室内装潢师

我们编写一个装饰器，就像任何其他函数一样。函数可以将其他函数作为参数，decorators 总是会这样做。对于下面的例子，我将调用这个参数`fn`来提醒我，我的输入是一个函数。我还将在函数中导入`perf_counter`,这样，如果我在其他地方使用这个定时器，就不必记得这么做了。

```
**def** **timer(fn):**
    """
    This is a decorator that returns the time it takes another    function to run.
    """
    **from** time **import** perf_counter **def inner_func(*args, **kwargs):**
        """
        This is the inner function that the timer decorator returns.
        """
        start = perf_counter()
        result = fn(*args, **kwargs)
        end = perf_counter()
        print(f'Run time: {(end — start):0.8f} seconds')

        **return** result **return** inner_func
```

# 让我们解开这里发生了什么

## 结构

装饰者有两层结构。当我们创建一个`timer()`的实例时，外部函数被调用。当我们准备好执行放入`timer()`的函数并计时其运行速度时，内部函数将被调用。这听起来可能有点抽象，但一会儿就应该变得更清楚了。

## 外部功能:`timer`

让我们从`timer()`的输入开始。注意，我去掉了`fn`的括号，尽管它是一个函数。我为什么要这么做？因为当 Python 看到函数名后面有括号时，它会调用该函数。我们不想让`fn`执行，因为它的输出会变成`timer()`的输入。因此`timer(add(1, 3))`实际上会是`timer(4)`，这是完全无用的。我们不想给数字 4 计时；我们要对*函数* `add()`计时。去掉括号。

接下来我们导入`perf_counter`，这样它就可以使用了。

## 内部函数:inner_func

内部函数做的第一件事是捕获我们想要计时的函数`fn`的参数。

但是我们没有放入`fn`的参数！它怎么知道它们是什么？

并没有。我们稍后将把这些参数放入，*在*之后，我们称之为`timer()`。一会儿我将向您展示这是如何工作的。与此同时，我们需要一些东西来代替这些未知的未来参数。我们用`*args`和`**kwargs`吧。

## 什么是`*args` 和**kwargs？

这些是任意数量参数的标准占位符名称，可以附加到我们选择输入到`timer()`的任何函数。一个星号(`*`)告诉 Python 这是一个位置参数。两颗星(`**`)告诉 Python 这是一个关键字参数。我不会深入讨论参数类型，因为参数完全是另外一个话题，但是知道`*args`和`**kwargs`作为我们选择计时的任何函数的总括就足够了，不管这个函数有多少或什么类型的参数。

## inner_func 和之前的代码有什么不同？

我们对计时`add(1, 3)`的原始代码做了两处修改。首先，我们将函数调用赋给了变量`result`，这样我们就可以返回它。这很方便，因为除了查看运行时间之外，它还允许我们获得`add`函数(或者我们选择的任何其他函数)的输出。其次，我们将计时的函数变成了一个通用函数，而不是将代码附加到`add()`上。

## inner_func 如何执行

为了区别于之前的`add()`代码，让我们假设我们也编写了一个名为`multiply().`的函数，它将两个数字作为参数，然后将它们相乘并返回它们的乘积。

我们已经决定要计时这个新函数。我们需要创建一个使用`multiply`作为参数的`timer()`实例，然后将其赋给一个变量。

`m = timer(multiply)`

现在我们已经将这个`timer()`实例存储在变量`m`中，我们可以输入之前未知的参数。我们可以像这样进行函数调用:

`m(2, 3)`

该函数调用将执行`inner_func`。事情是这样的:

*   `perf_counter()`运行并在`start`中存储时间
*   `multiply`替换通用`fn`和 2，3 替换`*args`和`**kwargs`。`multiply(2, 3)`现在执行。
*   `perf_counter()`再次运行，并将时间存储在`end`中
*   打印开始时间和结束时间之间的差异
*   返回`multiply(2, 3)`的输出，这样我们就可以看到它和 print 语句。

## 为什么这很酷

这种内部和外部函数的双层结构使我们能够将`timer`附加到像`multiply`这样的特定函数上，然后任意多次使用这两个函数的组合。我们可以调用`m(4, 5)`或`m(234329, 2343823)`或任何其他两个我们想要的数字，每次我们都会得到运行时间的打印输出以及数字的乘积。

## 如何运行定时器()

因为这是一个嵌套函数，所以它不会给出我们想要的常规函数调用的输出。

```
timer(add(1,3))**[Out]:** <function __main__.timer.<locals>.inner_func(*args, **kwargs)>
```

## 是时候给装修工打电话了

有两种方法可以叫装修工。传统的方式是这样的:

```
add = timer(add)add(1, 3)**[Out]:** Run time: 0.00000065 seconds
       4
```

注意变量赋值不是`a = timer(add)`或`some_other_variable = timer(add)`。使用装饰器，你总是给你的变量起一个和你正在装饰的函数一样的名字。为什么？以便它看起来与原始函数相同。它将接受与原始函数相同的参数，并执行与原始函数相同的操作——但现在它也将拥有装饰函数的属性。

Python 有一个更好的方法来做同样的事情。我们可以使用`@` 符号。

首先，您将编写装饰函数。然后你可以在你想要修饰的函数的定义上使用`@decorator_function_name`把它附加到另一个函数上。确保不要在`@`和装饰函数名之间留空格，也不要在装饰函数名后面使用任何括号。我重写一下`add`:

```
**@timer**
**def** add(a, b):
    """
    Takes two parameters, a and b, and returns their sum.
    """
    **return** a + b
```

现在我们告诉它把 1 和 3 相加:

```
add(1, 3)**[Out]:** Run time: 0.00000063 seconds
       4
```

有用！我们得到了运行时间和总和。

## 让我们再装饰一些

我们可以将`timer`附加到任何新功能的外部。

```
**@timer**
**def** add_more(a, b, c, d):
    """ 
    Takes four parameters, a, b, c, and d, and returns their sum.
    """
    **return** a + b + c + d add_more(1, 3, 4, 6)**[Out]:** Run time: 0.00000081 seconds
       14
```

呜-呼！

## 一个小问题…

还记得帮助文档吗？这会表现得很奇怪。

`@timer`通过执行相同的操作修改了原来的`add`和`add_more`功能，就像我们重新分配了它们的名称一样，如下所示:

```
add = timer(add)
add_more = timer(add_more)
```

重新分配函数名会覆盖那些函数，因此我们不再能够访问它们的元数据。下面是我们调用`help()`时发生的情况。

```
help(add)

**[Out]:** Help on function inner_func in module __main__:

    inner_func(*args, **kwargs)
        This is the inner function that the timer decorator returns.help(add_more)**[Out]:** Help on function inner_func in module __main__: inner_func(*args, **kwargs)
        This is the inner function that the timer decorator returns.
```

啊。调用`help()`现在给我们提供了`timer()`内部函数的文档字符串，这显然没有帮助。

## 有解决办法吗？

为什么是的有。而且很简单。

Python 的标准库包括`functools`，它有一个专门的工具，用于保存修饰函数的元数据。它叫做`wraps`，本身就是一个装饰器。我们可以通过修饰`inner_func`在`timer()`的定义里面使用它。

```
**def** **timer(fn):**
    """
    This is a decorator that returns the time it takes another function to run.
    """
    **from** time **import** perf_counter
    **from** functools **import** wraps **@wraps(fn)**
    **def inner_func(*args, **kwargs):**
        """
        This is the inner function that the timer decorator returns.
        """
        start = perf_counter()
        result = fn(*args, **kwargs)
        end = perf_counter()
        print(f'Run time: {(end — start):0.8f} seconds')

        **return** result **return** inner_func
```

请注意，`wraps()`与`timer()`一样将`fn`作为参数。不要漏掉那一点，否则它不会起作用。

现在我们需要重新定义`add()`和`add_more()`函数，使它们的名字指向这个更新的计时器。

```
**@timer**
**def add(a, b):**
    """
    Takes two parameters, a and b, and returns their sum.
    """
    **return** a + b**@timer
def add_more(a, b, c, d):**
    """
    Takes four parameters, a, b, c, and d, and returns their sum.
    """
    **return** a + b + c + d
```

他们像以前一样工作:

```
add(1, 3)**[Out]:** Run time: 0.00000078 seconds
       4 add_more(1, 3, 4, 6)**[Out]:** Run time: 0.00000070 seconds
       14 
```

但是现在当我们调用`help()`时，我们得到了我们想要的。

```
help(add)**[Out]:** Help on function add in module __main__: add(a, b)
         Takes two parameters, a and b, and returns their sum.

help(add_more)

**[Out]:** Help on function add_more in module __main__: add_more(a, b, c, d)
       Takes four parameters, a, b, c, and d, and returns their sum. 
```

耶！！！

我鼓励你亲自尝试一下。复制粘贴第二个`timer`代码，然后写几个函数来装饰它。如果你知道如何写一些简单的递归函数和循环函数，看看它们的相对速度会很有趣。(提示:一个比另一个快很多。)

点击[这里](https://github.com/LBBL96/Pandas-Web-Scraping-Tutorial/blob/master/Articles/Decorators/WhatsADecorator.ipynb)在 [GitHub](https://github.com/LBBL96) 上下载我的 Jupyter 笔记本，里面有这段代码。

非常感谢 Fred Baptiste 博士，他的[“深入 Python”](https://www.udemy.com/course/python-3-deep-dive-part-1/)帮助我更好地理解了这种语言的内部工作原理。