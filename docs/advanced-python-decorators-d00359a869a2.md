# 高级 Python:decorator

> 原文：<https://levelup.gitconnected.com/advanced-python-decorators-d00359a869a2>

这篇文章将展示我们如何有效地使用 python decorators。

![](img/13f95d64bf7a176cc389993ad562b129.png)

由 [Artturi Jalli](https://unsplash.com/@artturijalli?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/python?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

Python 中的 Decorators 是一种强大的设计模式，它允许我们在不修改 Python 对象的现有结构的情况下应用新的功能。正如我们在本系列的第一篇文章中看到的，我们可以将类作为参数传递，或者它可以从函数中返回，比如 Python 函数也是一等公民，我们也可以对函数执行所有这些操作。

# 到目前为止，这个系列

*   [Python 类、对象和 MRO](https://python.plainenglish.io/advanced-python-classes-objects-and-mro-423bb01521fb)
*   [数据类](/advanced-python-dataclasses-6a1e53bc4d8d)
*   装修工(此职位)

# 深入探讨 Python 函数

在我们理解什么是装饰器之前，我们需要了解更多关于 python 函数及其工作原理的知识。

```
**>>> def print_hello(name: str) -> None:
...     print("Hello " + name)
...
>>> print_hello("Spiderman")
Hello Spiderman
>>>**
```

在这个例子中，我们可以看到函数`**print_hello()**`打印了“Hello < input >”，其中 *input* 是我们传递给函数的参数，最终它没有返回任何值(因为我们没有返回任何值)。

> 有一个叫做副作用的概念，它基本上意味着如果一个函数改变了它的函数定义之外的任何东西，比如改变已经传递的参数的值，改变一个全局变量的值，或者在 stdout 中打印一些东西。在这种情况下，我们的函数不返回任何东西，但是它在 stdout 中打印，所以它有副作用。

## Python 函数也是对象:一级对象

正如我在上一篇文章中提到的，python 函数也是对象，[一级对象](https://stackoverflow.com/questions/245192/what-are-first-class-objects)，这意味着在 python 中，我们可以像使用任何其他对象(int、float、string、list、tuple 等)一样使用函数。)我们可以将它们作为参数传递给另一个函数，或者可以作为值返回。

```
**def greet(func: callable):
    print(func.__name__)
greet(print_hello)** -------output------------
**print_hello**
```

在上面的函数中，我们基本上是传递另一个函数作为它的参数，然后只打印它的名字。我们也可以这样做，

```
**def print_hello(name:str) -> None:
    print('Hello ' + name)

def greet(func: callable) -> None:
    func("Thor")

greet(print_hello)**------------output--------------
**Hello Thor**
```

注意，我们没有在`**print_hello**`后面使用`**()**`，因为我们不想执行它，我们想传递函数，在`**greet**`内部，我们实际上正在调用函数。所以基本上我们将`**print_hello**`作为参数传递给另一个函数，就像任何其他对象一样。

我们也可以从其他函数返回函数。

```
**def talk(name: str) -> Callable[[], None]:
    def greet() -> None:
        print("Hi " + name)

    #Notice no () here
    return greet** **f = talk("Batman")
print(f)
f()**----------output----------
**<function talk.<locals>.greet at 0x000001BBCB7CB5B0>
Hi Batman**
```

这里我们不是将一个函数传递给另一个函数，而是从`**talk()**`接收另一个函数，然后执行它，最后打印`**Hi Batman**`。

## 内部函数

我们也可以在一个函数中定义另一个函数。

```
**def outer_func() -> ():
    message = 'Hi'

    def inner_func():
        print(message)

    return inner_func

outer_func()() # try with single () and see what happens!**-------output----------
**Hi**
```

这里我们可以调用`**outer_func()**`,但是我们不能显式调用`**inner_func()**`,也不能访问该函数中声明的任何变量。

# 装修工

现在我们已经修改了关于 python 函数的重要概念，我们终于可以进入我们的主题了，Python 装饰者。装饰器是返回可调用对象的可调用对象。基本上，装饰者接受一个函数，添加一些功能，然后返回它。

让我们创建一个简单的装饰器。

首先，我们需要创建一个在函数内部的包装器，它看起来类似于上面的函数，一个函数在另一个函数内部。

```
**def my_decorator(func):
    def wrapper():
        print("Inside wrapper()")
        func()
    return wrapper**
```

这里我们取一个函数作为`**my_decorator()**` 函数的自变量。在`**my_decorator()**` 中，我们定义了另一个函数，它将执行传递的函数。现在要使用这个装饰器，我们需要创建另一个函数，把它赋给一个变量，并把它赋给`**my_decorator().**`

```
**def say_something():
    print("Hi!")

talk = my_decorator(say_something)
talk()**-------output----------
Inside wrapper()
Hi!
```

这里到底发生了什么？

基本上，我们是用`**my_decorator()**`来修饰`**say_something()**` ，然后将返回的函数保存为`**talk,**`，但一般情况下，我们会将返回的函数名与原函数保持一致，即`**say_something()**`

为了便于使用，我们有一个选项来重写整个事情如下。

```
@**my_decorator
def say_something():
    print("Hi!")**

**say_something()**-------output----------
Inside wrapper()
Hi!
```

输出保持不变。

## 有争论的装饰者

现在你可能会想，我们可以在任何函数中使用这个装饰器，但这不是真的，如果你的装饰器不能处理传递给被装饰函数的参数，那么它肯定会失败，正如你在下面看到的。

```
@**my_decorator
def say_something(name: str):
    print(f"Hi {name}!")**

**say_something("Iron man")**-------output----------Traceback (most recent call last):
  File "C:\Users\SANDIPAN DUTTA\PycharmProjects\decorators\Decorators.py", line 69, in <module>
    say_something("Iron man")
TypeError: my_decorator.<locals>.wrapper() takes 0 positional arguments but 1 was given
```

这是因为内部函数`**wrapper()**` 不带任何参数。为了克服这一点，我们可以使用`***args**`和`****kwargs**` ，这样它可以接受任意数量的参数。

```
**def my_decorator(func):
    def wrapper(*****args, **kwargs****):
        print("Inside wrapper()")
        func(*****args, **kwargs****)
    return wrapper****@my_decorator
def greet(name: str):
    print(f"Hi {name}!")

greet("Iron man")**-------output----------
Inside wrapper()
Hi Iron man!
```

还有一个问题，在当前的方法中，修饰函数的返回值将被忽略。

```
**@my_decorator
def greet(name: str):
    return f"Hi {name}!"

var = greet("Iron man")
print(var)**-------output----------
Inside wrapper()
None
```

这是因为`**wrapper()**`实际上并没有返回任何东西，为了完成这项工作，我们需要返回修饰函数的值。

```
**def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("Inside wrapper()")
        return func(*args, **kwargs)
    return wrapper

@my_decorator
def greet(name: str):
    return f"Hi {name}!"

var = greet("Iron man")
print(var)**-------output----------
Inside wrapper()
Hi Iron man!
```

## 真实世界的例子

这是一个 decorator 的例子，它决定用户是登录还是使用会话登录 flask 应用程序。

```
**def login_required(f):
    @wraps(f)
    def wrap(*args, **kwargs):
        if 'logged_in' in session:
            return f(*args, **kwargs)
        else:
            flash("You need to login first")
            return redirect(url_for('login_page'))

    return wrap

@app.route("/logout/")
@login_required
def logout():
    session.clear()
    flash("You have been logged out!")
    gc.collect()
    return redirect(url_for('dashboard'))**
```

# 结论

在本教程中，我们学习了如何为我们自己的定制需求创建一个 python 装饰器。在下一篇文章中，我们将学习元类。下一集见。