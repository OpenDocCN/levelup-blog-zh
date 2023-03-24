# 帮助你调试 Python 代码的 5 个技巧

> 原文：<https://levelup.gitconnected.com/5-tricks-that-will-help-you-debug-python-code-7ac10f7837a3>

![](img/72c15d6ecf842f7c1e98a2571a353f97.png)

Unsplash 上的 [@joszczepanska](https://unsplash.com/@joszczepanska)

调试是程序员的一项基本技能，它是一门手艺，我们需要练习才能做得更好。在这里，我总结了调试 Python 代码的有用技巧和工具。

# #1 查看函数或对象的源代码

了解实现的细节在调试中至关重要。在调试 Python 代码时，您可能想知道模块、类、方法、函数、回溯、帧或代码对象的源代码。 ***视*** 模块将帮助您:

```
import inspect
def add(x, y):
    return x + yclass Dog:
    kind = 'dog'         # class variable shared by all instances
    def __init__(self, name):
        self.name = name    # instance variable unique to each instance def eat(food):
        print("eating ..." + food)print(inspect.getsource(add))    
print(inspect.getsource(Dog))
print(inspect.getsource(Dog.eat))
```

# #2 将日志打印到文件中

*日志*是我们理解代码运行时最重要的线索。打印日志是跟踪程序执行流程的一种有用方式。

但是，Python 中的 *print* 只输出到一个终端。对于复杂的故障排除，尤其是对于服务器端应用程序来说，这还远远不够理想。

Python 3 中的 *print* 函数要强大得多，因为它可以接受更多的参数，指定一些参数可以将 print 的内容输出到一个日志文件中。

```
with open('test.log', mode='w') as f:
    print('hello, python', file=f, flush=True)
```

另一个有用且灵活的模块是*日志*，我们可以设置输出文件名、日志格式等..

```
import logginglogging.basicConfig(filename='app.log', filemode='w', 
		format='%(name)s - %(levelname)s - %(message)s')
a = 5
b = 0
try:
  c = a / b
except Exception as e:
  logging.exception("Exception occurred")
```

更多用法请参考[日志记录— Python 日志记录工具— Python 3.9.0 文档](https://docs.python.org/3/library/logging.html)

# #3 跟踪执行时间

对于性能问题，我们需要计算函数的运行时间。您可能会这样实现它:

```
import timestart = time.time()
# run the function
end = time.time()print(end-start)
```

有一个名为 *timeit* 的内置模块，我们可以用它来跟踪时间，只需一行代码:

```
import time
import timeitdef run_sleep(second):
    print(second)
    time.sleep(second)print(timeit.timeit(lambda :run_sleep(2), number=1))
```

# #4 交互式调试

REPL 支持增量的、交互式的开发风格，交互式调试是一种更有效的方式。理想的工作流程是编辑-运行的重复过程。

要用交互式会话调用程序，我们需要添加*-I*选项:

```
python -i demo.py
```

为了让您的生活更轻松，*import lib . reload(module)*是避免重新启动交互会话的合适方法。

举个例子，假设我们正在运行一个名为 demo 的函数，为了解决一个问题，我们需要对这个函数进行一些修改，然后 *reload(module)* 将加载修改后的 demo 版本，而无需重启我们的会话:

```
>>> from importlib import reload
>>> import demo from module
>>> demo()
"The result of demo..."# Make some changes to "demo"
>>> demo()
"The result of demo..."  # The Outdated result
>>> reload(module)  # Reload "module" after changes made to "demo"
>>> demo()
"This will be the new result...">>> # repeat the circle of fix-and-retry
```

这将为调试节省大量时间。

# #5 使用 pdb

Python 有一个模块叫做 ***pdb*** 支持交互式源代码调试器。它支持设置断点，在线级单步执行。另外， ***pdb*** 打印检查堆栈帧、变量值、检查源代码等。

要使用调试器启动程序，请添加-m **pdb** 选项来调用脚本:

```
user@coderscat:~/snippets$ python3.7 -m pdb demo.py 
> /home/user/snippets/demo.py(1)<module>()
-> import inspect
(Pdb) l
  1  -> import inspect
  2  
  3     def gcd(a,b):
  4         if(b==0):
  5             return a
  6         else:
  7             return gcd(b,a%b)
  8  
  9  
 10     print(gcd(34, 34))
[EOF]
(Pdb) b demo.py:3
Breakpoint 1 at /home/user/snippets/demo.py:3
(Pdb) c
> /home/user/snippets/demo.py(3)<module>()
-> def gcd(a,b):
(Pdb) step
> /home/user/snippets/demo.py(10)<module>()
-> print(gcd(34, 34))
(Pdb)
```

从正在运行的程序中断调试器的另一个典型用法是插入:

```
import pdb; pdb.set_trace() 
```

下面是一些有用的 *pdb* 命令

*   list(l):显示 Python 解释器当前所在的代码行
*   步骤:继续逐行执行，单步执行函数
*   next(n):继续下一行代码
*   break(b):在当前行设置新的断点
*   继续(c):继续执行，直到下一个断点

在大多数情况下，我们不需要遵循执行中的每一步， *pdb* 是调试 Python 程序的终极工具。

# 结论

当您调试 Python 代码时，尝试一下这些技巧和工具。提高你的调试技巧，熟能生巧！