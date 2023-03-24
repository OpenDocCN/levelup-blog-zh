# 使用装饰器测量 Python 中函数的执行时间

> 原文：<https://levelup.gitconnected.com/using-a-decorator-to-measure-execution-time-of-a-function-in-python-9109ce8ff870>

## 分析您的应用程序

假设我们的函数是:

```
def my_func(a, b):
    retval = a+b
    return retval
```

现在，我们想测量这个函数所用的时间。简单地说，我们添加一些行来实现这一点。

```
import time

def my_func(a, b):
    start_time = time.time();
    retval = a+b
    print("the function ends in ", time.time()-start_time, "secs")
    return retval
```

将当前时间存储在`start_time`变量中，然后我让方法执行，然后从当前时间中减去`start_time`以获得实际的方法执行时间。

输出:

```
my_func(1, 2)
the function ends in  7.152557373046875e-07 secs
```

但是我们可以更好地使用装饰来包装任何功能。

```
from functools import wraps
import time

def timer(func):
    @wraps(func)
    def wrapper(a, b):
        start_time = time.time();
        retval = func(a, b)
        print("the function ends in ", time.time()-start_time, "secs")
        return retval
    return wrapper
```

现在我们可以使用这个`timer`装饰器来装饰`my_func`函数。

```
@timer
def my_func(a, b):
    retval = a+b
    return retval
```

当调用这个函数时，我们得到下面的结果。

```
my_func(1, 2) 
the function ends in 1.1920928955078125e-06 secs
```

如果函数接受两个以上的变量，因为我们不知道需要多少个参数，那该怎么办？

简单地说，我们使用`*args`允许我们向 Python 函数传递可变数量的非关键字参数。

```
def timer(func):
    @wraps(func)
    def wrapper(*args):
        start_time = time.time();
        retval = func(*args)
        print("the function ends in ", time.time()-start_time, "secs")
        return retval
    return wrapper
```

我们可以将`@timer`添加到我们想要测量的每个函数中。

```
@timer
def my_func(a, b, c):
    retval = a+b+c
    return retval
```

输出:

```
my_func(1,2, 3)
the function ends in  1.6689300537109375e-06 secs
```

很简单，对吧？