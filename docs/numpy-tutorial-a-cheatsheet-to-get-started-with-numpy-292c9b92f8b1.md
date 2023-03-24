# Numpy 教程:Numpy 入门手册

> 原文：<https://levelup.gitconnected.com/numpy-tutorial-a-cheatsheet-to-get-started-with-numpy-292c9b92f8b1>

![](img/79ae445f9bf7b5a72879fbd5a03b1958.png)

[科学高清照片](https://unsplash.com/@scienceinhd?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 开始使用 NumPy 所需要知道的一切。

Numpy 是 Python 的一个开源数值库。它提供了一个高性能的多维数组对象和工具来处理这些数组。

有时我们需要一个备忘单来浏览，而不是把时间浪费在长文章上。这就是我们为您创建此备忘单的原因。

这里`arr`表示数组。

# 导入数字

在开始使用 numpy 之前，我们必须导入它。

```
import numpy as np
```

# 进口/出口

Numpy 提供了许多用于导出和导入文件的内置方法。

```
np.loadtxt('file.txt')                     Saving from a text file
np.genfromtxt('file.csv',delimiter=',')    Saving from a CSV file
np.savetxt('file.txt',arr,delimiter=' ')   Writes to a text file
np.savetxt('file.csv',arr,delimiter=',')   Writes to a CSV file
```

# 创建数组

NumPy 数组是由相同类型的值组成的网格，由一组非负整数索引。维数是数组的*秩*；数组的*形状*是一个整数元组，给出了数组的大小及其每个维度。

```
np.array([1,2,3])           One-dimensional arraynp.array([(1,2,3),(4,5,6)]) Two-dimensional arraynp.zeros(5)                 1D array of length 5 all values 0np.ones((5, 3))             5x3 array with all values 1np.eye(5)                   5x5 array of 0 with 1 on diagonal (Identity matrix)np.linspace(0,100,6)        Array of 6 evenly divided values from 0 to 100np.arange(0,10,3)           Array of values from 0 to less than 10 with step 3 (eg [0,3,6,9])np.full((2,3),8)            2x3 array with all values 8np.random.rand(4,5)        4x5 an array of random floats between 0–1np.random.rand(6,7)*100    6x7 an array of random floats between 0–100np.random.randint(5,size=(2,3)) 2x3 array with random ints between 0–4
```

# 检查属性

每个 NumPy 数组都是由相同类型的元素组成的网格。Numpy 提供了一组广泛的数字数据类型，您可以使用它们来构造数组。Numpy 试图在创建数组时猜测数据类型，但是构成数组的函数通常还包含一个可选参数来显式指定数据类型。我们还可以检查数组的大小、形状和数据类型。

```
arr.size          Returns number of elements in arr
arr.shape         Returns dimensions of arr (rows,columns)
arr.dtype         Returns type of elements in arr
arr.astype(dtype) Convert arr elements to type dtype
arr.tolist()      Convert arr to a Python list
np.info(np.eye)   View documentation for np.eye
```

# 复制/分类/整形

除了使用数组计算数学函数之外，我们经常需要对数组中的数据进行整形或操作。这种操作最简单的例子是转置一个矩阵；要转置一个矩阵，只需使用数组对象的属性`T`。

```
np.copy(arr)        Copies arr to new memory
arr.view(dtype)     Creates a view of arr elements with type dtype
arr.sort()          Sorts arr
arr.sort(axis=0)    Sorts specific axis of arr
two_d_arr.flatten() Flattens 2D array two_d_arr to 1D
arr.T               Transposes arr (rows become columns and vice versa)
arr.reshape(3,4)    Reshapes arr to 3 rows, 4 columns without changing data
arr.resize((5,6))   Changes arr shape to 5x6 and fills new values with 0
```

# 添加/删除元素

使用 **NumPy** 模块的 **append** ()方法可以**添加**一个 **NumPy 数组元素**，同样对于**删除**可以使用**删除**()，对于**在特定位置插入**一个元素可以使用**插入**()方法。

```
np.append(arr,values)     Appends values to end of arr
np.insert(arr,2,values)   Inserts values into arr before index 2
np.delete(arr,3,axis=0)   Deletes row on index 3 of arr
np.delete(arr,4,axis=1)   Deletes column on index 4 of arr
```

# 组合/拆分

我们可以使用`concatenate`方法在 numpy 中添加两个数组。

函数的作用是:沿着轴参数= 1 分割一个数组。**numpy . hs split**相当于 **split** 且**轴参数= 1** ，数组总是沿第二轴分割，与数组维数无关。

```
np.concatenate((arr1,arr2),axis=0)   Adds arr2 as rows to the end of arr1
np.concatenate((arr1,arr2),axis=1)   Adds arr2 as columns to end of arr1
np.split(arr,3)                      Splits arr into 3 sub-arrays
np.hsplit(arr,5)                     Splits arr horizontally on the                 5th index
```

# 索引/切片/子集化

Numpy 提供了几种方法来索引数组。

**切片:**类似于 Python 列表，NumPy 数组也可以切片。由于数组可能是多维的，因此必须为数组的每个维度指定一个切片。

**整数数组索引:**当使用切片对 NumPy 数组进行索引时，得到的数组视图将始终是原始数组的子数组。相比之下，整数数组索引允许您使用另一个数组中的数据构建任意数组。

**布尔数组索引:**布尔数组索引允许你挑选数组中的任意元素。这种类型的索引通常用于选择满足某些条件的数组元素。这里有一个例子:

```
arr[5]            Returns the element at index 5
arr[2,5]          Returns the 2D array element on index [2][5]
arr[1]=4          Assigns array element on index 1 the value 4
arr[1,3]=10       Assigns array element on index [1][3] the value 10
arr[0:3]            Returns the elements at indices 0,1,2 (On a 2D array: returns rows 0,1,2)
arr[0:3,4]          Returns the elements on rows 0,1,2 at column 4
arr[:2]             Returns the elements at indices 0,1 (On a 2D array: returns rows 0,1)
arr[:,1]            Returns the elements at index 1 on all rows
arr<5               Returns an array with boolean values
(arr1<3) & (arr2>5) Returns an array with boolean values
~arr                Inverts a boolean array
arr[arr<5]          Returns array elements smaller than 5
```

# 标量数学和矢量数学

基本的数学函数对数组进行元素运算，既可以作为运算符重载，也可以作为 NumPy 模块中的函数。

`*`是元素式乘法，不是矩阵乘法。相反，我们使用`dot`函数来计算向量的内积，用一个矩阵乘以一个向量，再乘以矩阵`dot`既可以作为 NumPy 模块中的函数使用，也可以作为数组对象的实例方法使用。

```
np.add(arr,1)        Add 1 to each array element
np.subtract(arr,2)   Subtract 2 from each array element
np.multiply(arr,3)   Multiply each array element by 3
np.divide(arr,4)     Divide each array element by 4 (returns np.nan for division by zero)
np.power(arr,5)      Raise each array element to the 5th powernp.add(arr1,arr2) -> Elementwise add arr2 to arr1
np.subtract(arr1,arr2) -> Elementwise subtract arr2 from arr1
np.multiply(arr1,arr2) -> Elementwise multiply arr1 by arr2
np.divide(arr1,arr2) -> Elementwise divide arr1 by arr2
np.power(arr1,arr2) -> Elementwise raise arr1 raised to the power of arr2
np.array_equal(arr1,arr2) -> Returns True if the arrays have the same elements and shape
np.sqrt(arr) -> Square root of each element in the array
np.sin(arr) -> Sine of each element in the array
np.log(arr) -> Natural log of each element in the array
np.abs(arr) -> Absolute value of each element in the array
np.ceil(arr) -> Rounds up to the nearest int
np.floor(arr) -> Rounds down to the nearest int
np.round(arr) -> Rounds to the nearest int
```

# 统计数字

Numpy 配备了如下所列的强大统计功能:

```
np.mean(arr,axis=0) Returns mean along a specific axis
arr.sum()           Returns the sum of arr
arr.min()           Returns the minimum value of arr
arr.max(axis=0)     Returns maximum value of the specific axis
np.var(arr)         Returns the variance of the array
np.std(arr,axis=1) Returns the standard deviation of a specific axis
arr.corrcoef()     Returns correlation coefficient of array
```

如果您想了解更多关于数组数据结构的知识，请阅读我们的文章:

[](https://medium.com/codeconvention/learn-array-data-structure-2fa01edd21c2) [## 学习数组数据结构

### 让我们探索数组数据结构。

medium.com](https://medium.com/codeconvention/learn-array-data-structure-2fa01edd21c2) 

不要与时间复杂度和大 O 符号混淆，创造更好的算法:

[](https://medium.com/swlh/a-comprehensive-guide-for-time-complexity-and-big-o-notation-4ebef201dfc8) [## 时间复杂性和大 O 符号的综合指南

### 大 O 是一种语言，我们用它来描述一个算法的复杂性，意味着它可以有多快或多慢。不是…

medium.com](https://medium.com/swlh/a-comprehensive-guide-for-time-complexity-and-big-o-notation-4ebef201dfc8)