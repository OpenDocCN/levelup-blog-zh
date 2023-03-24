# 在 Python 中创建和使用函数

> 原文：<https://levelup.gitconnected.com/create-and-use-functions-in-python-19b093f3ba9>

## 了解如何通过 Python 中的几个简单步骤来使用函数

![](img/1ff7efb1a855e1b7db5d78361b98cc93.png)

在 Python 中创建和使用函数

什么是函数？函数是做什么的？顾名思义，函数执行特定的任务。该任务可以重复使用，也可以根据使用案例重复使用一次。函数的主要目的是将逻辑分成不同的块。我们可以创建自己的函数，或者使用 python 中任何可用的内置函数。

**语法:**

```
def function_name(parameters):
    """docstring"""
    statement(s)
    return expression
```

我们可以在语法中看到，要创建一个函数，我们需要在函数名后面使用`def`关键字。形参/实参是我们将数据传递给函数的唯一方式。我们可以添加一个 **docstring** 来解释函数及其参数**的用法。**如果需要，函数可以返回值。归还某物不是强制性的。

一旦我们定义了函数，要使用它，我们需要调用那个函数。假设我已经创建了一个名为`odd_or_even`的函数。要调用这个函数，我可以像`odd_or_even()`那样写。如果这个函数需要一些参数，我可以写成`odd_or_even(2)`。

# 两个数的和

如果我们只想打印两个数的和，或者只想查看两个数的和，我们不需要返回任何东西。如果我们试图把它赋给一个变量，我们得到的值是`None`。

```
def sum_num(num1, num2):
    total = num1 + num2
    print(total)sum_num(5,10)# OUTPUT: 15def sum_num(num1, num2):
    total = num1 + num2
    print(total)temp = sum_num(5,10)
print(temp)""" OUTPUT: 
15
None
"""
```

现在，如果我们想返回总和，我们可以使用下面的代码。

```
def sum_num(num1, num2):
    total = num1 + num2
    return totaltemp = sum_num(5,10)
print(temp)# OUTPUT: 15
```

# Python 中的关键字参数

一般来说，我们需要按照特定的顺序将参数传递给函数。但是，使用关键字参数，我们可以以任何顺序传递参数。我们通过一个除法的例子来理解一下。在下面的例子中，如果我们想要`3/2`，那么我们将写`divide(3,2)`。

```
def divide(num1, num2):
    return num1/num2ans = divide(3,2)
print(ans)# OUTPUT: 1.5
```

但是现在，由于失误，我们写了`divide(2,3)`。这个错误会让整个逻辑出错。

```
def divide(num1, num2):
    return num1/num2ans = divide(2,3)
print(ans)# OUTPUT: 0.6666666666666666
```

为了避免这种错误，我们可以使用关键字参数。这只是一个普通的参数传递，但是我们将传递变量名，而不是仅仅传递值。

```
def divide(num1, num2):
  return num1/num2ans = divide(num1=3, num2=2)
print(ans)# OUTPUT: 1.5
```

所以，现在我们可以以任何顺序传递数据，这样逻辑将保持不变。

```
def divide(num1, num2):
  return num1/num2ans = divide(num2=2, num1=3)
print(ans)# OUTPUT: 1.5
```

> *这是 python 中函数的最佳实践之一。*

# Python 中的可变长度参数

在某些情况下，我们不知道函数中会收到多少个参数。或者我们不想用特定数量的参数来限制函数。在这种情况下，我们可以使用变长参数。有两种类型的变长参数:

1.  *参数(非关键字参数)
2.  **kwargs(关键字参数)

# 1.*参数(非关键字参数)

在非关键字参数中，我们只传递 n 个数据给函数。`*args`不是非关键字参数的固定名称。我们可以用任何名字。使用 ***args 只是一个标准。**

让我们假设，我们要求用户输入他们拥有的水果。注意，每个用户可能有不同数量的水果。为了保存或操作用户输入的所有水果，我们可以使用非关键字参数。

```
def fruit_list_operation(*fruits):
  for fruit in fruits:
    print(fruit, end=",")fruit_list_operation("apple", "banana")print()fruit_list_operation("apple", "banana", "grape", "mango")""" OUTPUT:
apple,banana,
apple,banana,grape,mango,
"""
```

在这里，我们用 for 循环作为`*args`将创建一个序列数据。正如你所看到的，当我们经过 2 个水果循环时，在第二种情况下运行了 2 次和 4 次。

`print(fruit, end=",")`该函数用于通知解释器没有用**新行**结束语句。相反，使用一个**逗号(，)**来结束语句。而当我们使用`print()`函数时，它只是写了一个新行，然后继续前进。

# 2.**kwargs(关键字参数)

变长关键字参数与变长非关键字参数几乎相同。唯一的区别是，当我们不知道将得到多少个关键字参数时，我们使用这个可变长度的关键字参数函数。

```
def my_project(**kwargs):
  for key, value in kwargs.items():
        print("%s == %s" % (key, value))my_project(frontend = "Angular", backend = "Python", database="MongoDB")""" OUTPUT: 
frontend == Angular
backend == Python
database == mongodb
"""
```

# Python 中的默认参数值

如果参数没有传递给函数，当我们想使用默认值时，我们可以使用默认参数。我们可以使用任何数字和任何数据类型作为默认参数。只有一个条件，我们必须使用默认值参数作为函数的最后一个参数。

```
def division(num1, num2=2):
  print(num1/num2)division(16,4)
division(16)""" OUTPUT:
4.0
8.0
"""
```

让我们看看如果将第一个参数作为默认参数会发生什么。

```
def division(num1=2, num2):
  print(num1*num2)division(16,4)
division(16)""" OUTPUT: 
File "functions.py", line 1
    def division(num1=2, num2):
                         ^^^^
SyntaxError: non-default argument follows default argument
"""
```

# Python 中的 Docstring

正如我们在语法中看到的，任何函数中的第一个注释语句都被认为是 docstring。Docstring 只不过是一些注释语句，解释函数的用法、参数和返回表达式。要打印或获取 docstring，我们可以使用下面的语法:`*function_name*.__doc__`。让我们看一个例子来理解 docstring 的概念。

```
def is_odd(num1):
  """Function to check if the number is odd or not. Parameters:
        num1 (float): number to be checked Returns:
        is_odd (boolean): true if number is odd"""
  is_odd = num1 % 2 != 0
  print(is_odd)
  return is_odd is_odd(16)
is_odd(1)
print("-"*15)
print(is_odd.__doc__)""" OUTPUT:
False
True
---------------
Function to check if the number is odd or not. Parameters:
        num1 (float): number to be checked Returns:
        is_odd (boolean): true if number is odd
"""
```

当我们使用第三方库，甚至一些 python 内置库时，我们可以使用 docstring 来理解特定函数的用法。

```
print(range.__doc__)""" OUTPUT:
Return an object that produces a sequence of integers from start (inclusive)
to stop (exclusive) by step.  range(i, j) produces i, i+1, i+2, ..., j-1.
start defaults to 0, and stop is omitted!  range(4) produces 0, 1, 2, 3.
These are exactly the valid indices for a list of 4 elements.
When a step is given, it specifies the increment (or decrement).
"""
```

# 结论

就这样。感谢您的阅读。

如果你需要任何帮助或者想讨论什么，请告诉我。在推特[或 LinkedIn](https://bit.ly/3KjwgZV) 上联系我。请务必在下面的评论中留下你的想法、问题或担忧。我很想看看他们。

> *想了解更多信息？*
> 
> *注册我的* [*简讯*](https://bit.ly/3Menk8Q) *，把最好的文章放进你的收件箱。*

直到下一次👋

> ***从 Python 101 系列探索我的其他博客。***

[](/modules-and-packages-in-python-63df7d952bb8) [## Python 中的模块和包

### 了解模块和包的基础知识以及如何在 python 中使用它们

levelup.gitconnected.com](/modules-and-packages-in-python-63df7d952bb8) [](/conditional-statements-and-loops-in-python-b8ac64f36faa) [## Python 中的条件和循环

### 了解 Python 中的条件语句和循环

levelup.gitconnected.com](/conditional-statements-and-loops-in-python-b8ac64f36faa)