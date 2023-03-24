# Python 中的变量和数据类型

> 原文：<https://levelup.gitconnected.com/variables-and-data-types-in-python-eee80a886a17>

Python 初学者的变量和数据类型完全指南

![](img/9e17eacad3e3c71bf6598f23e408b004.png)

Python 中的变量和数据类型

> ***免责声明:*** 如果你是一名程序员，那么你可以浏览整个章节。我们将在后面深入探讨这一部分。我猜😉

# 变量和命名约定

在每一种编程语言中，在某些时候，我们需要存储一些数据。这些数据存储在我们系统的内存中。为了访问、使用和操作这些数据，我们将它们赋给一个变量。

简单地说，变量是你给计算机内存位置的名字，用来存储实际的数据。在 python 中，我们可以在任何地方声明变量。没有必要让所有变量都位于代码文件的开头。此外，要为变量赋值，我们可以执行以下操作:

```
age = 5
name = "John Doe"
height = 5.9
can_speak_french = False
```

为了编写更好的 python 代码，我们也可以遵循命名约定。基本规则是:

1.  每个变量都应该有适当的含义
2.  尽量为变量使用简短且有意义的名称
3.  如果变量包含多个有意义的单词，请使用下划线(_)来分隔它们。

在编程中，我们有时会存储一些在程序执行或生命周期中不会改变的值。这种数据被称为**常数**。

```
ENVIRONMENT = "DEVELOPMENT"
```

常量遵循与任何普通变量相同的命名约定。但是，有了小小的改变。所有的常量都是大写的。

# 数据类型

数据类型是指哪种数据将被存储到变量或常量中。Python 可以存储和使用许多类型的数据。Python 的内置数据类型分类如下:

1.  数值型:整型、浮点型、复杂型
2.  序列类型:字符串、列表、元组
3.  布尔代数学体系的
4.  一组
5.  词典

# 数字的

Python 支持三种数值数据。我们可以将整数(int)、浮点和复杂数据存储为数字数据类型。

# 整数

在 python 中，整数值可以是任意长度。整数例子有 10、2、29、-20、-150 等。

```
a = 5  
print("The type of a", type(a))# OUTPUT: The type of a <class 'int'>
```

# 浮动

Float 用于存储浮点数，如 1.9、9.902、15.2 等。它精确到小数点后 15 位。

```
b = 40.5  
print("The type of b", type(b)) # OUTPUT: The type of b <class 'float'>
```

# 复杂的

复数包含一个有序对，即 x + iy，其中 x 和 y 分别表示实部和虚部。

```
c = 1+3j  
print("The type of c", type(c))# OUTPUT: The type of c <class 'complex'>
```

# 序列类型

顾名思义，这种数据类型存储值/数据的序列。该数据类型包含以下子类别:

1.  线
2.  目录
3.  元组

# 线

字符串只是一个字符序列。在 python 中，单个字符也被视为字符串。为了存储字符串数据，我们可以使用单引号、双引号或三引号。

```
str1 = 'Use of double quotes'
print(str1)# OUTPUT: Use of double quotes str2 = """A multiline 
string"""  
print(str2)# OUTPUT: A multiline
#          string str3 = "a"  
print(type(str3))# OUTPUT: <class 'str'>
```

# 目录

列表数据类型类似于数组，但不完全相同。在数组中，我们只能存储一种类型的数据。然而，在 python list 中，我们可以将多种类型的数据存储到一个列表中。

```
list1  = [0, "Python", "Program", 1]
print(type(list1))
print(list1)
# OUTPUT: <class 'list'>
# OUTPUT: [0, "Python", "Program", 1]list1[0] = 2
print(list1)
# OUTPUT: [2, "Python", "Program", 1]list2  = list([0, "Python", "Program", 1])
print(type(list2))
print(list2)
# OUTPUT: <class 'list'>
# OUTPUT: [0, "Python", "Program", 1]list3  = [0, "Python", "Program", 1, 2, 3]
print(list3[2])
print(list3[-2])
# OUTPUT: Program
# OUTPUT: 2
```

# 元组

元组数据类型类似于 python 中的列表。列表和元组的主要区别是，一旦我们创建了元组，我们就不能改变它的值。

```
tuple1  = (0, "Python", "Program", 1)
print(type(tuple1))
print(tuple1)# OUTPUT: <class 'tuple'>
# OUTPUT: (0, "Python", "Program", 1, 2, 3) tuple1[1] = 5
# ERROR:# Traceback (most recent call last):
#     File "main.py", line 14, in <module>
#        tuple1[2] = "Python";
# TypeError: 'tuple' object does not support item assignment
```

# 布尔代数学体系的

布尔被认为是真实的。它只有两个值。

1.  真实的
2.  错误的

> 注意:***T*** *和* ***F*** *中真假必须大写。*

```
a1 = 5
b1 = 5c1 = a1==b1
print(c1)
# OUTPUT: Truea2 = 5
b2 = "5"c2 = a2==b2
print(c2)
# OUTPUT: Falsea3 = True
print(type(a3))
# OUTPUT: <class 'bool'>
```

# 一组

集合可以像列表一样使用。集合和列表的一个区别是，列表可以有重复的值，而集合不能。下面的例子会更清楚。

要创建一个集合，我们必须使用 **set()** 函数或者添加由**花括号{}** 包围的项目。在日常编程中，我并没有看到 set 的太多用途。Set 有非常小且特定的用例。

要创建空集我们必须 **set()** 函数，空花括号{}将不起作用。

```
list1  = [1, "Python", "Program", 2, 1, 2]
print(list1)
# OUTPUT: [1, "Python", "Program", 2, 1, 2]set1  = set([1, "Python", "Program", 2, 1, 2])
print(set1)
# OUTPUT: {1, 2, 'Program', 'Python'}set2  = set("Hello There!")
print(set2)
# OUTPUT: {'h', 'r', 'o', 'H', 'T', 'e', 'l', '!', ' '}set3  = {1, "Python", "Program", 2, 1, 2}
print(set3)
# OUTPUT: {1, 2, 'Program', 'Python'}set4  = {}
print(type(set4))
# OUTPUT: <class 'dict'>set5  = set()
print(type(set5))
# OUTPUT: <class 'set'>
```

# 词典

Dictionary 是以无序方式存储的键值对的集合。如果你来自 Java 背景，你可以把它看作是一种**映射**或**散列表**。字典中的条目用**逗号(，)**分隔，并用**大括号{}** 括起来。

使用示例中所示的两种方法创建字典:

```
dict1 = {'Name': 'Python', 1: [1, 2, 3, 4], 2:"DICT"}  
print(dict1)
# OUTPUT: {'Name': 'Python', 1: [1, 2, 3, 4], 2: 'DICT'}dict2 = dict({'Name': 'Python', 1: [1, 2, 3, 4], 2:"DICT"})
print(dict2)
# OUTPUT: {'Name': 'Python', 1: [1, 2, 3, 4], 2: 'DICT'}dict3 = dict([('Name', 'Python'), (1, [1, 2, 3, 4]), (2,"DICT")])
print(dict3)
# OUTPUT: {'Name': 'Python', 1: [1, 2, 3, 4], 2: 'DICT'}dict3 = {'Name': 'Python', 'Last':'Programming', 1: [1, 2, 3, 4], 2:"DICT"}  
print(dict3['Name'])
print(dict3[1])
print(dict3.get(2))
# OUTPUT: Python
# OUTPUT: [1, 2, 3, 4]
# OUTPUT: "DICT"
```

在字典中，我们通过使用键来获取或提取数据。因此，当我们执行`dict[1]`时，它从最后一个代码中提取带有关键字 **1** 的数据，即`[1, 2, 3, 4]`。

# 结论

介绍本节只是为了展示 python 中可用的不同数据类型。有许多内置方法与这些数据类型相关联。我们将在另一节中看到它们，我将向您展示它们的一些实际用途。

就这样。感谢您的阅读。

如果你喜欢这个，与你想学习 Python 的朋友或同事分享。如果你需要任何帮助或者想讨论什么，请告诉我。通过 [Twitter](https://bit.ly/3KjwgZV) 或 [LinkedIn](https://bit.ly/3JbsPDm) 联系我。

> 想了解更多？
> 
> 注册我的[时事通讯](https://bit.ly/3Menk8Q)，把最好的文章放进你的收件箱。

谢谢你的时间，🧡

[](/string-manipulation-in-python-7f8f62236792) [## Python 中奇妙的字符串数据类型带来的乐趣

### 了解如何在 Python 中使用内置的字符串操作方法

levelup.gitconnected.com](/string-manipulation-in-python-7f8f62236792) [](/dive-into-daunting-lists-and-dictionaries-in-python-2d22daf2c897) [## 深入 Python 中令人生畏的列表和字典

### 理解 Python 中的内置列表和字典方法

levelup.gitconnected.com](/dive-into-daunting-lists-and-dictionaries-in-python-2d22daf2c897)