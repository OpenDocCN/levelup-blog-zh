# 每个 Python 程序员都应该知道的 7 个 Python 技巧

> 原文：<https://levelup.gitconnected.com/7-python-tips-that-every-python-programmer-should-know-b04b50ad1514>

## Python 提示像专业程序员一样编码

![](img/7ecaaa51fa23a106f7e93b04679a3279.png)

Johan Godínez 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 是一种通用的初学者友好的编程语言，用于从机器学习到 Web 开发的不同领域。由于其简单的语法，它在开发人员中很有名。

在本文中，我将分享和讨论一些 python 技巧和代码片段，它们可以用来解决日常编程问题。我们开始吧！

# 1.循环时移除元素

让我们举一个从列表中删除偶数的例子。我们必须在不使用其他列表的情况下执行此任务。我们可以通过遍历列表的副本来实现。

```
arr = [1,2,3,4,4,7]for element in arr[:]:
    if element%2==0:
        arr.remove(element)print(arr) Output:
[1,3,7]
```

在上面的例子中，我们使用列表切片`arr[:]`创建了一个列表的副本，并使用它修改了原始列表。

## 另一种方式:

```
arr = [1,2,3,4,4,7]arr[:] = [item for item in arr if item%2!=0]
print(arr) Output:
[1,3,7]
```

在上面的代码中，我们创建了一个列表的副本，然后使用列表理解来过滤数字。

# 2.创建嵌套目录

## I .使用 Pathlib 模块

从 Python 3.5 开始，创建嵌套目录最简单的方法就是使用`pathlib`库。这里的`Path`函数采用了嵌套目录的结构。

```
from pathlib import PathPath("./Python/subdir").mkdir(parents=True, exist_ok=True)
```

通过设置 parents= `True`可以根据需要创建任何缺失的目录，如果目录已经存在，exist_ok= `True`可以帮助我们忽略异常。

## 二。使用操作系统模块

另一种创建嵌套目录的方法是使用`os`模块。

```
import osif not os.path.exists("./Python/subdir"):
    os.makedirs("./Python/subdir")
```

# 3.合并两本词典

## I .使用 update()方法

在 python 中，我们可以使用`update()`方法来合并两个字典。在这种方法中，第二个字典被合并到第一个字典中，并且不创建新的字典。

> 语法:dict1.update(dict2)

```
x = {'Name':'John', 'Age':25}
y = {'Age':30, 'City':'New York'}x.update(y)
print(x)Output:
{'Name': 'John', 'Age': 30, 'City': 'New York'}
```

## 二。使用(**)运算符

在`Python 3.5`之后，合并多个词典变得更加容易。我们可以使用`(**)`运算符合并字典。

我们需要传递要在{}中合并的字典以及(**)操作符，它将为您合并字典。

> 语法:{ * *词典 1，* *词典 2}

```
x = {'Name':'John', 'Age':25}
y = {'Age':30, 'City':'New York'}z = {**x, **y}
print(z)Output:
{'Name': 'John', 'Age': 30, 'City': 'New York'}
```

# 4.连接两个列表

## I .使用“+”运算符

我们可以在`‘+’`操作符的帮助下连接两个列表。我们需要写由'+'操作符分隔的列表名称，它将连接列表。

> 语法:列表 1 +列表 2

```
a = [1,2,3]
b = ['a','b','c']c = a+b
print(c) Output:
[1, 2, 3, 'a', 'b', 'c']
```

## 二。使用*运算符

我们可以在`(*)`操作符的帮助下连接多个列表。我们需要将列表传递给`[]`，以及`*`操作符，列表将被连接起来。

> 语法:[*list1，*list2]

```
a = [1,2,3]
b = ['a','b','c']c = [*a,*b]
print(c)Output:
[1, 2, 3, 'a', 'b', 'c']
```

# 5.检查目录或文件是否存在

在操作系统模块的帮助下，我们可以检查文件或目录是否存在。

```
import osif os.path.isfile("filename.py"):
    f = open("filename.py")if os.path.isdir(directory_path):
    #perform operationif os.path.exists(file_path):
    #perform operation
```

# 6.查找项目的索引

我们可以用`index()`的方法，找到列表中某一项的索引。我们需要将想要知道索引的项目名称传递给`index()`方法，它将返回该项目的索引号。

> 语法:listname.index(项目)

```
mylist = ["zero", "one", "two", "three"]try:
    idx = mylist.index("one")
except ValueError:
    idx = -1print(idx)Output:
1
```

## 另一种方式:

```
mylist = ["zero", "one", "two", "three", "one"]idx = [i for(i,e) in enumerate(mylist) if e=="one"]
print(idx)Output:
[1,4]
```

# 7.运行命令

## 一.使用子流程模块

我们可以使用**子流程**模块来执行 Python 中的命令。我们需要将命令参数作为字符串列表传递。

```
import subprocess
subprocess.run(["ls, "-l"])
```

## 二。使用操作系统模块

在 Python 中，我们也可以使用 OS 模块来执行命令。我们需要在 OS 模块的`system()`函数中将命令参数作为单个字符串传递。

```
import os
os.system("ls -l")
```

# 结论

这就是这篇文章的全部内容。在本文中，我们介绍了一些可以在编码时使用的 Python 技巧。这些提示和代码片段使用起来很方便。所以，练习这些技巧，以便更好地动手。

感谢阅读！