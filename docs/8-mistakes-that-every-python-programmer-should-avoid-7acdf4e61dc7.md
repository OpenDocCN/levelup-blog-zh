# 每个 Python 程序员都应该避免的 8 个错误

> 原文：<https://levelup.gitconnected.com/8-mistakes-that-every-python-programmer-should-avoid-7acdf4e61dc7>

## 需要注意的 Python 错误

![](img/97d93e20d59a238b382ff3245d28d1c8.png)

照片由[拉杜·弗罗林](https://unsplash.com/@raduflorin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Python 是一种简单易学的编程语言。许多初级程序员选择 python 作为他们的第一编程语言，因为它的语法简单，易于使用和应用。

虽然 python 是一种简单且用户友好的编程语言，但 python 程序员在编写代码时会犯一些常见的错误。这种常见的错误有时可能会破坏您的代码，因此最好提前了解这些错误。

在本文中，我总结了许多 python 程序员常犯的一些错误。我们开始吧！

## 1.迭代时修改列表

迭代时修改列表/集合是一个非常糟糕的主意。在迭代时从列表中删除一个条目是许多程序员经常犯的错误。

这里有一个例子:

```
#Program to remove odd numbers from list
odd = lambda x : bool(x % 2)numbers = [i for i in range(10)]
for i in range(len(numbers)):
   if odd(numbers[i]):
      del numbers[i]    #deleting odd number while iteration
```

错误如下:

```
Traceback (most recent call last):
  File "<string>", line 7, in <module>
IndexError: list index out of range
```

**解决方案:**

这个错误可以通过使用列表理解来避免:

```
odd = lambda x : bool(x % 2)nums = [i for i in range(10)]
nums[:] = [i for i in nums if not odd(i)]
print(nums)
```

## 2.模块名称冲突

Python 在包和库方面非常丰富。因此，您可能会遇到 python 模块的名称与 Python 标准库中同名模块的名称冲突。

例如，您的代码中可能有一个名为`math.py`或`email.py`的模块，这可能会与同名的标准库模块冲突。

这可能会导致一些具有挑战性的问题，比如假设您正在导入一个库，而这个库又试图导入模块的 Python 标准库版本。现在，由于您也有一个同名的模块，包可能会错误地导入您的模块版本，而不是 Python 标准库中的版本。

因此，您应该始终避免使用 Python 标准库中的模块名称，以避免任何歧义和冲突。

## 3.不关闭以前打开的文件

在 python 中，一旦对文件执行了所有特定的操作，并且现在不再需要对其进行操作，关闭打开的文件总是一个好的做法。

请记住，打开的文件会占用您的系统资源，并且可能会被锁定，如果您在使用这些文件后不关闭它们，它们将继续使用系统资源，并可能会阻止其他程序使用这些资源。

为了避免这种情况，在读取文件时使用`with`总是一个好的做法。它总是会帮你关闭文件。

**举例:**

而不是:

```
file = open('filename.txt', 'w')file.write('new_data')file.close()
```

用这个:

```
**with** open('filename.txt', 'w') **as** file:
    file.write('new_data')
```

## 4.误解 Python 函数

Python 有大量的内置函数。他们中的一些人可能做着同样的工作，但执行的方式不同。

作为程序员，我们需要理解一个特定的函数是如何工作的，因为使用错误的函数可能不会给我们预期的结果。

例如，在 python 中，为了对集合进行排序，我们有两个函数`sort()`和`sorted()`。这两个函数具有相同的功能，即对集合进行排序。但是这两个功能的工作方式不同。

**示例:**

```
list1 = [6, 1, 7, 2, 9, 5]
print(list1.sort())list2 = [6, 2, 8, 5, 3, 11]
print(sorted(list2))
```

**输出:**

```
**None****[2, 3, 5, 6, 8, 11]**
```

现在，刚刚发生了什么？这两个函数都用于排序，但是`sort()`的输出是 None，而`sorted()`打印排序后的列表。

在这里，原因是`sort()`执行就地排序(对原始序列进行更改)并返回 none。而`sorted()`总是返回一个排序后的列表，不对原始序列做任何改变。

## 5.误用 _init_ 方法

在 python 中，`_init_`方法是一个保留的方法，在 Python 类中用作构造函数。当 python 为一个新的类对象分配内存时，它被调用，并允许该对象初始化该类的属性。

此方法的目的是在创建类的对象时初始化该类的数据成员的值。但是，程序员有时使用这个方法返回值，这不是这个`_init_`方法的目的，并且偏离了它的实际目的。

## 6.使用导入*

每当感觉懒惰时，许多程序员通常使用`import *`从包中导入所有东西。

这种惰性导入被认为是一种不好的做法，原因有很多，比如:

*   它降低了代码的可读性，因为我们不知道导入的是什么，因此我们不能容易地识别某个函数是从哪个模块导入的。
*   由于`import *`，覆盖某个变量/函数的风险始终存在。
*   污染名称空间，因为`import *`导入名称空间中所有可能与用户定义的函数和类冲突的函数和类。

**例如:**

使用`import *`:

```
**from** math **import** *print(floor(3.14))
print(ceil(3.14))
print(pi)
```

> 更好的方法:

```
**import** math
**from** math **import** pi print(math.floor(3.14))
print(math.ceil(3.14))
print(pi)
```

## 7.不正确的缩进

就代码中的缩进而言，Python 是一种非常严格的编程语言，因为它不使用或依赖括号来分隔代码块。与其他编程语言不同，在 Python 中，缩进比仅仅让代码看起来干净有用得多。

因此，正确的缩进是必要的，否则即使一个缩进错误也会破坏你的代码并给你带来意想不到的结果。

当您在代码中使用多个用户定义的函数时，正确的缩进变得更加重要，有时它可能不会给您带来缩进错误，但可能会导致代码中出现严重的 bug。

## 8.未使用的库导入

通常，程序员在 python 文件的开头导入库，但从不在代码的后面使用它们。

这种未使用的导入创建了一个不需要存在于代码中的依赖，并且它也使得理解代码变得困难，因为我们不知道这些导入的库在哪里被使用。

## 结论

这就是这篇文章的全部内容。在本文中，我们讨论了 python 程序员会犯的一些常见错误。实践和动手处理这些错误，让你的编码生活变得简单和没有错误。

感谢阅读！

> *在你走之前……*

如果你喜欢这篇文章，并希望**继续关注**更多**精彩**文章，请考虑使用我的推荐链接[https://pralabhsaxena.medium.com/membership](https://pralabhsaxena.medium.com/membership)成为一名中级会员。

另外，你可以在这里免费订阅我的时事通讯: [Pralabh 的时事通讯](https://pralabhsaxena.medium.com/subscribe)。