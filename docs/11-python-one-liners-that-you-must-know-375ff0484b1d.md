# 你必须知道的 11 个 Python 一行程序

> 原文：<https://levelup.gitconnected.com/11-python-one-liners-that-you-must-know-375ff0484b1d>

## 像专业程序员一样编写 Python 一行程序

![](img/7fb5b56113ff5afec40ed3a0220541e7.png)

照片由[法比奥·卢卡斯](https://unsplash.com/@fabiolucas_foto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Python 是开发人员中最流行的编程语言之一。背后的原因是其简单的语法。

Python 总能用一行代码解决复杂的问题，给我们带来惊喜。有许多复杂的编程相关问题，我们可以使用 Python 在一行代码中解决。

这些一行程序对于竞争性编程也很方便。这些一行程序显示了您对正在使用的编程语言的理解程度。

你练习编程越多，你就越能优化你的代码，用最少的代码行写逻辑。

在本文中，我提出了非常有用的 Python 一行程序。我们开始吧！

# 1.列表中多个空格分隔的输入

在解决编码问题时，我们经常需要在代码中接受多个整数输入。我们可以使用`map()`函数将 input()值转换为整数值，然后将这些整数值添加到列表中。

```
input_list = list(map(int, input().split()))print(input_list)
```

# 2.一个数的位数之和

为了计算一个数的数字总和，我们使用数学运算符，如`%`(模)和`/`除法。但是在 Python 中，我们只用一行代码就可以计算出总和。

这个一行程序对于计算一个数的数字总和很有用。

```
sum_of_digit = lambda x: sum(map(int, str(x)))output = sum_of_digit(123)
print("Sum of the digits is: ", output)Output:
Sum of the digits is: 6
```

# 3.平铺列表列表

我们可以使用列表理解在一行代码中展平列表列表。

```
input_list = [[1], [2,3,4], [5,6], [7,8], [9]]output_list = [item for sublist in input_list for item in sublist]
print(output_list)Output:
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

# 4.反转一根绳子

这段代码对于查找字符串的倒数很有用。我们使用字符串切片在一行代码中找到相反的东西。

```
input_string = "Namaste World!"
reversed_string = input_string[::-1]print("Reversed string is: ", reversed_string)Output:
Reversed string is: !dlroW etsamaN
```

# 5.合并两本词典

我们可以使用`(**)`操作符在一行代码中合并多个字典。我们只需要将字典和`(**)`操作符一起传递给 a {}，它就会为我们合并字典。

```
dictionary1 = {"name": "Joy", "age": 25}
dictionary2 = {"name": "Joy", "city": "New York"} merged_dict = {**dictionary1, **dictionary2} 
print("Merged dictionary:", merged_dict) Output:Merged dictionary: {'name': 'Joy', 'age': 25, 'city': 'New York'}
```

# 6.交换字典中的键和值

这个单行代码对于交换字典的键值对很有用。

```
dict = {'Name': 'Joy', 'Age': 25, 'Language':'Python'}result = {v:k for k, v in dict.items()}
print(result)Output:
{'Joy': 'Name', 25: 'Age', 'Python': 'Language'}
```

# 7.打开并阅读文件

我们可以打开一个文件，读取它的内容，并使用下面的一行代码将它赋给一个变量:

```
text = [line.strip() for line in open(filename)]
```

# 8.印刷图案

为了打印一个模式，我们经常使用嵌套循环并编写逻辑。

```
n = 5
for i in range(n):
    for j in range(i+1):
        print("* ", end="")
    print("\n")
```

有一种替代方法可以用一行代码打印出模式。下面是一行代码的示例:

```
n=5
print('\n'.join("* " * i for i in range(1,n+1))) Output:* 
* * 
* * * 
* * * * 
* * * * *
```

# 9.矩阵的转置

我们可以在`zip()`函数和列表理解的帮助下，只用一行代码转置一个矩阵。我们可以这样做:

```
import numpy as npmat = [[1,2,3], [4,5,6], [7,8,9]]
transpose_matrix = [list(item) for item in zip(*mat)]print(np.maxtix(mat))
print(np.matrix(transposeMatrix)) 
Output:mat: 
[[1 2 3]
 [4 5 6]
 [7 8 9]]transpose_matrix:
[[1 4 7]
 [2 5 8]
 [3 6 9]]
```

# 10.斐波纳契数列

斐波纳契数列是一系列数字，其中每个数字都是前面两个数字的和。我们使用列表理解和 for 循环在一行代码中创建一个斐波那契数列。

```
n=10fib = [0,1]
[fib.append(fib[-2]+fib[-1]) for _ in range(n)]
print(fib)Output:
[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89]
```

# 11.列表中所有数字的平方

我们可以使用列表理解找到列表中所有数字的平方。

```
numbers = [1,2,3,4,5,6,7]
output = [num**2 for num in numbers]print(output)Output:
[1, 4, 9, 16, 25, 36, 49]
```

# 结论

这就是这篇文章的全部内容。在本文中，我们浏览了一些 Python 一行程序。这些一行程序对于竞争性编程和日常编码生活非常有用。因此，要多练习这些一行程序，以便于使用。

感谢阅读！

你可以在这里免费订阅我的简讯: [Pralabh 的简讯](https://pralabhsaxena.medium.com/subscribe)。