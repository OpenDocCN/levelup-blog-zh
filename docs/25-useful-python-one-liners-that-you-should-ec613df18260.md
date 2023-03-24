# 您应该知道的 25 个有用的 Python 命令行程序

> 原文：<https://levelup.gitconnected.com/25-useful-python-one-liners-that-you-should-ec613df18260>

## 这使得 python 不朽

![](img/b92948832e8a6d510e2dbcce873a62e7.png)

“作者提供的图像”

我用 Python 写第一行代码的那天，我就被它的简单性、流行性和著名的一行程序迷住了。在博客中，我想展示一些 python 一行程序。

## 1.交换两个变量

```
# a = 4 b = 5
**a,b = b,a** # print(a,b) >> 5,4
```

让我们从一个更简单的开始，把两个变量互相交换。这个方法是最简单、最直观的方法之一，您无需使用 temp 变量或应用算术运算就可以编写它。

## 2.多变量赋值

```
a,b,c = 4,5.5,'Hello'
#print(a,b,c) >> 4,5.5,hello
```

您可以使用逗号和变量一次为变量分配多个值。使用这种技术，您可以一次分配多个数据类型 var。您可以使用列表为变量赋值。下面是一个将多个值分配给列表中不同 var 的示例。

```
a,b,*c = [1,2,3,4,5]
print(a,b,c)**> 1 2 [3,4,5]**
```

## 3.列表中偶数的和

有许多方法可以做到这一点，但最好和最简单的方法是使用列表索引和求和函数。

```
a = [1,2,3,4,5,6]
s = sum([num for num in a if num%2 == 0])
print(s)
>> 12
```

## 4.从列表中删除多个元素

`del`是 python 中用来从列表中移除值的关键字。

```
#### Deleting all even
a = [1,2,3,4,5]
del a[1::2]
print(a)>[1, 3, 5]a
```

## 5.读取文件

```
lst = [line.strip() for line in open('data.txt')]
print(lst)
```

这里我们用的是列表理解。首先，我们打开一个文本文件，并使用一个`for`循环，逐行读取。最后，使用`strip`,我们移除了所有不必要的空间。有一种更简单快捷的方法，只使用 list 函数。

```
list(open('data.txt'))*##Using with will also close the file after use* with open("data.txt") as f: lst=[line.strip() for line in f]
print(lst)
```

## 6.将数据写入文件

```
with open("data.txt",'a',newline='\n') as f: f.write("Python is awsome")
```

上面的代码将首先创建一个文件 data.txt(如果还没有的话),然后它将在文件中写入`Python is awsome`。

## 7.创建列表

```
lst = [i for i in range(0,10)]
print(lst)
**>** [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]or lst = list(range(0,10))
print(lst)
```

我们也可以用同样的方法创建一个字符串列表。

```
lst = [("Hello "+i) for i in ['Karl','Abhay','Zen']]
print(lst)
**>** ['Hello Karl', 'Hello Abhay', 'Hello Zen']
```

## 8.映射列表或类型转换整个列表

有时在我们的项目中，我们需要改变列表中所有元素的数据类型。您想到的第一种方法是使用一个循环，然后访问列表中的所有元素，然后一个接一个地更改元素的数据类型。这个方法是针对 python 中的老派的，我们有一个`map`函数可以为我们完成这项工作。

```
list(map(int,['1','2','3']))
**>** [1, 2, 3]list(map(float,[1,2,3]))
**>** [1.0, 2.0, 3.0][float(i) for i in [1,2,3]]
**>** [1.0, 2.0, 3.0]
```

## 9.集合创建

我们用来创建列表的方法也可以用来创建集合。让我们使用包含该范围内所有偶数的平方根的方法创建一个集合。

```
*#### Square of all even numbers in an range*
{x**2 for x in range(10) if x%2==0}**>** {0, 4, 16, 36, 64}
```

## 10.嘶嘶的嗡嗡声

在这个测验中，我们需要编写一个程序来打印从 1 到 100 的数字。但是对于三的倍数，打印“ **Fizz** ”而不是数字，对于五的倍数，打印“ **Buzz** ”。

看起来我们必须使用循环和多个 if-else 语句。如果你尝试用其他语言来做，你可能要写多达 10 行代码，但是用 python，我们只用一行代码就能实现 FizzBuzz。

```
['FizzBuzz' if i%3==0 and i%5==0 else 'Fizz' if i%3==0 else 'Buzz' if i%5==0 else i  for i in range(1,20)]
```

在上面的代码中，我们使用 list comprehension 运行一个从 1 到 20 的循环，然后在循环的每次迭代中，我们检查这个数字是否能被 3 或 5 整除。如果是，那么我们相应地用 Fizz 或 Buzz 替换该数字，否则我们用 FizzBuzz 替换该数字。

## 11.回文

回文是一个数字或字符串，当它被颠倒时看起来是一样的。

```
text = 'level'
ispalindrome = text == text[::-1]
ispalindrome**>** True
```

## 12.用空格将整数分隔成一个列表

```
lis = list(map(int, input().split()))
print(lis)**>** 1 2 3 4 5 6 7 8
[1, 2, 3, 4, 5, 6, 7, 8]
```

## 13.λ函数

一个**λ函数**是一个小匿名**函数**。一个 **lambda 函数**可以接受任意数量的参数，但是只能有一个**表达式**。

```
sqr = lambda x: x * x  *##Function that returns square of any number*
sqr(10)
**>** 100
```

## 14.检查列表中的数字是否存在

```
num = 5
if num in [1,2,3,4,5]:
     print('present')
**>** present
```

## 15.印刷图案

模式总是让我着迷。在 python 中，我们可以只用一行代码就画出惊人的图案。

```
n = 5
print('\n'.join('😀' * i for i in range(1, n + 1)))**>** 😀
😀😀
😀😀😀
😀😀😀😀
😀😀😀😀😀
```

## 16.寻找阶乘

阶乘是一个整数和它下面所有整数的乘积。

```
import math
n = 6
math.factorial(n)**>** 720
```

## **17。斐波那契数列**

一系列数字，其中每个数字(*斐波那契数*)是前面两个数字的和。最简单的斐波那契数列 1，1，2，3，5，8，13 等。我们可以使用列表理解和 for 循环来创建一个斐波纳契数列。

```
fibo = [0,1]
[fibo.append(fibo[-2]+fibo[-1]) for i in range(5)]
fibo
**>** [0, 1, 1, 2, 3, 5, 8]
```

## 18.素数

质数是只能被自身和 1 整除的数。例:2，3，5，7 等。要生成一个范围内的质数，我们可以使用带有过滤器和 lambda 的列表函数来生成质数。

```
list(filter(lambda x:all(x % y != 0 for y in range(2, x)), range(2, 13)))**>** [2, 3, 5, 7, 11]
```

## 19.查找最大数量

```
findmax = lambda x,y: x if x > y else y 
findmax(5,14)**>** 14or 
max(5,14)
```

在上面使用 lambda 函数的代码中，我们检查了比较条件，并据此返回最大值。

## 20.线性代数

有时候我们需要把一个列表的元素缩放到 2 倍或者 5 倍。代码解释了如何操作。

```
def scale(lst, x): return [i*x for i in lst] 
scale([2,3,4], 2) ## call**>** [4,6,8]
```

## 21.矩阵的转置

你需要将所有的行转换成列，反之亦然。在 python 中，你可以使用`zip`函数在一行代码中转置一个矩阵。

```
a=[[1,2,3],
   [4,5,6],
   [7,8,9]] 
transpose = [list(i) for i in zip(*a)] 
transpose**>** [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
```

## 22.对模式的出现进行计数

当我们需要知道一个模式在文本中出现的次数时，这是一个重要而有用的用例。在 python 中，我们有`re`库来为您完成这项工作。

```
import re; len(re.findall('python','python is a programming language. python is python.'))**>** 3
```

## 23.用其他文本替换一个文本

```
"python is a programming language.python is python".replace("python",'Java')**>** Java is a programming language. Java is Java
```

## **24。模拟投掷硬币**

它可能不是那么重要，但是当你需要从一组给定的选择中产生一些随机的选择时，它会非常有用。

```
import random; random.choice(['Head',"Tail"])**>** Head
```

## 25.生成组

```
groups = [(a, b) for a in ['a', 'b'] for b in [1, 2, 3]] 
groups**>** [('a', 1), ('a', 2), ('a', 3), ('b', 1), ('b', 2), ('b', 3)]
```

我已经分享了我所知道的所有有用和重要的一行程序。如果你知道更多，请在评论中告诉大家。

> ***感谢阅读😀，跟随*** [***我***](https://parasharabhay13.medium.com/) ***这里为更多***[](https://medium.com/pythoneers)

## *你可能喜欢的其他文章*

*[](https://medium.com/pythoneers/30-basic-machine-learning-questions-answered-692acd10841f) [## 回答了 30 个最常见的机器学习问题

### 检查并增加您的 ML 知识🤔

medium.com](https://medium.com/pythoneers/30-basic-machine-learning-questions-answered-692acd10841f) [](/15-python-packages-you-probably-dont-know-exits-aef0525a965f) [## 15 个你可能不知道的 Python 包出口

### 对你非常有用的东西

levelup.gitconnected.com](/15-python-packages-you-probably-dont-know-exits-aef0525a965f) [](/21-python-mini-projects-with-codes-c4126e4131e4) [## 21 个带代码的 Python 迷你项目

### 学习编程语言的最好方法是用它来构建项目

levelup.gitconnected.com](/21-python-mini-projects-with-codes-c4126e4131e4) [](/50-python-interview-question-and-answers-404e08bc054c) [## 回答了 50 个 Python 基本问题

### 一些检查你的 Python 知识的问题🤔

levelup.gitconnected.com](/50-python-interview-question-and-answers-404e08bc054c) [](/10-python-tips-for-better-code-1bbffde3b44d) [## 提高代码质量的 10 个 Python 技巧

### 你应该开始养成这种习惯

levelup.gitconnected.com](/10-python-tips-for-better-code-1bbffde3b44d) [](/20-python-packages-that-you-must-try-a81862c913f6) [## 你必须尝试的 20 个 Python 包

### 这让你的生活更轻松

levelup.gitconnected.com](/20-python-packages-that-you-must-try-a81862c913f6) 

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)*