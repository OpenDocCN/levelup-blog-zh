# 数字备忘单

> 原文：<https://levelup.gitconnected.com/numpy-cheatsheets-40a976c3a2b5>

![](img/466f745d5c5bc36d771b2e4c1c2a673d.png)

NumPy 是 Travis Oliphant 在 2005 年为数据科学家创建的最重要的 Python 库之一，另外两个是 pandas 和 matplotlib。

开发 NumPy 是为了帮助 Python 中的科学计算，其速度比普通 Python 计算提高了 50 倍。此外，它简化和缩短了您的代码，从而增加了它的可读性和生产力。

在开始像使用 Python 中的其他库一样使用 NumPy 特性之前，我们需要首先下载 NumPy 库。它可以使用软件包管理器下载，如 pip 或 conda。然而，为了简单起见，我们将使用 Google Colab，这样就消除了安装所有软件包所需的开销，因为 Colab 默认支持 NumPy。

> Colaboratory，简称“ **Colab** ”，是谷歌研究院的产品。 **Colab** 允许任何人通过浏览器编写和执行任意 python 代码，特别适合机器学习、数据分析和教育。[https://research.google.com/colaboratory/faq.html](https://research.google.com/colaboratory/faq.html)

可以使用以下语法在我们的代码中导入 NumPy 库:

> **导入 numpy 作为 np**

在这篇博客中，我们将看看 NumPy 中的一些常见操作，例如:

1.  创建数组，
2.  算术运算，
3.  聚合函数，
4.  文件处理操作，
5.  排序功能

**1。创建数组**

与 Python 不同，在 NumPy 中，我们将列表称为数组。数组是相同类型的值的集合，可以使用非负整数值来访问。数组可以定义为一维数组、二维数组或 n 维数组。

用于创建不同维度的数组:

a.一维数组:

```
array_oned = np.array([1,2,3])#output
[1 2 3]
```

b.二维阵列:

```
array_twod = np.array([(1,2,3),(4,5,6)])
#output
[[1 2 3]  
 [4 5 6]]
```

c.三维阵列:

```
array_threed = np.array([[(1,2,3),(4,5,6)],[(2,3,4),(5,6,7)]])#output
[
  [[1 2 3][4 5 6]]   
  [[2 3 4][5 6 7]]
]
```

初始值为 0、1 或随机数的数组。

a.所有索引都用 0 填充的二维 2x2 数组

```
zero_arr = np.zeros((2,2))
print(zero_arr)#output
[[0\. 0.][0\. 0.]]
```

b.2 维 2x2 数组，所有索引都用 1 填充

```
one_arr = np.ones((2,2))
print(one_arr)#output
[[1\. 1.][1\. 1.]]
```

c.2 维 2x2 阵列，所有索引都用随机数填充。

```
np.random.seed(0)
rand_arr = np.random.randn((2,2))
print(rand_arr)#output
[[0.5488135  0.71518937][0.60276338 0.54488318]]
```

2.**算术运算**

在算术部分，我们将处理数组上的简单算术运算。通过使用 NumPy，我们可以对数组执行向量算术运算，这是一个非常强大的功能，即我们可以直接对数组执行算术运算，而不必通过循环。

例如，在如下所示的 Python 中的两个列表的加法运算中，

```
first_arr = [1,2,3]
second_arr = [4,5,6]sum = first_arr + second_arrprint(sum)#output:
[1,2,3,4,5,6]
```

这里，输出是两个数组的连接，这不是预期的结果。

要执行两个数组的加法，我们需要使用如下所示的循环将 first_array 中的每个元素添加到 second _ aray 中的相应元素。

```
add = []
for i in range(len(a)):
   add.append(a[i]+b[i])
print(add)#output
[5,7,9]
```

这为我们提供了所需的结果。然而，该任务需要多个步骤。我们可以通过使用 NumPy add 函数来显著改进这段代码。

```
import numpy as npfirst_num = [1,2,3,4]
second_num = [5,6,7,8]sum = np.add(first_num, second_num)
print(sum)#output 
[ 6  8 10 12]
```

NumPy 解决方案不仅简单优雅，而且速度非常快。

同样，我们可以使用 NumPy 对数组执行减法、乘法和除法。

```
import numpy as npfirst_num = [1,2,3,4]
second_num = [5,6,7,8]diff = np.subtract(second_num,first_num)
print(diff)mul = np.multiply(second_num,first_num)
print(mul)div = np.divide(second_num,first_num)
print(div)#output 
[4 4 4 4]
[5 12 21 32]
[5\. 3\. 2.33333333 2.]
```

这不是比默认的 Python 方法简单多了吗？

**3。聚合函数**

聚合函数是 NumPy 中的帮助函数，它帮助将数组中的值组合在一起，给出一个汇总值。

Python 中的一些聚合函数有:

a. **sum()** :数组中所有元素的总和

b. **min()** :查找数组中最小的元素

c.max() :查找数组中最大的元素

d. **mean()** :数组的平均值

e. **median()** :数组的中值

f. **corrcoef()** :求相关系数

g. **std()** :求标准差。

```
import numpy as nparr = [1,2,3,4] #To find the aggregate sum of the array
sum = np.sum(a)
print(sum)#output
10#To find the minimum value in an array
min = np.min(a)print(min)
#output
1 **#To find the maximum value in the array**
max = np.max(a)
print(max)*#output
4***#To find the mean value of the array**
mean = np.mean(a)
print(mean)*#output
2.5***#To find the median value of the array.**
med = np.median(a)
print(med)*#output
2.5***#To find the correaltion coefficiant** 
cor = np.corrcoef(a)
print(cor)*#output
1***#To find the standard deviation** 
std = np.std(a)
print(std)*#output
1.1180339887*
```

使用上面的 NumPy 聚合函数，我们可以大大简化寻找聚合和、最小值、最大值、标准偏差等等的操作。

**4。文件处理操作**

Numpy 提供了将文件加载到磁盘或从磁盘保存文件的便利功能。但是，建议使用 panda 来执行此类操作，因为它更高级，功能更丰富。熊猫也可以很容易地与 NumPy 集成。

**a .从文本文件加载数据。**

我们可以使用 np.load()从文本文件中加载数据。

```
data = np.load(“file.txt”)
#load the data of file.txt into data
```

**b .从 csv 文件加载数据。**

```
data_from_csv = np.genfromtxt("file.csv",delimiter=",")
#loading data from csv file
```

**c .将数据保存到文本文件。**

```
np.savetxt(“file.txt”,arr, delimiter=" ")
```

注意:分隔符是文件中保存数据的格式。

**5。分类功能**

排序函数是按照有序的顺序排列数组元素的有用工具。

```
num = [4,1,2,3]
sort_num = np.sort(num)
print(sort_num)#output
[1,2,3,4]
```

类似地，我们可以对不同维度的数组执行排序操作。

```
num = np.array([[3,2,1],[1,3,2]])
sort_num = np.sort(num, axis=0)
print(sort_num)#output
[[1 2 1]  [3 3 2]]num = np.array([[3,2,1],[1,3,2]], )
sort_num = np.sort(num)
print(sort_num)#output
[[1 2 3]  [1 2 3]]
```

当我们设轴= 0 时。我们明确要求程序按第一个轴排序。

而当我们把 axis = 1，那么程序将被一个数组中的每个单独的子数组排序。

**总结**

上面的例子是 NumPy 提供的一些基本功能。还有许多其他特定于科学领域、数据科学、机器学习、可视化等特性。它与许多流行的 Python 库紧密集成，并专门为每个社区用例而构建。正因如此，也就可以理解，为什么 NumPy 的人气被炸了。

编辑:[碧玉樱桃](https://www.linkedin.com/in/jaspercherry/)