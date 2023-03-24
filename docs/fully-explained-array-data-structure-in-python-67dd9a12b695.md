# Python 中完整解释的数组数据结构

> 原文：<https://levelup.gitconnected.com/fully-explained-array-data-structure-in-python-67dd9a12b695>

## 适用于数据科学和机器学习的数据结构概念

![](img/ebc4b42b111ede51e33db491cb26ae3f.png)

戈兰·艾沃斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

> **简介 **

您是否想知道 Python 程序如何存储我们正在加载的数据，这些数据放在哪里，或者我们如何检索它们？所有这些都可以通过数组来实现。数组只不过是存储在相邻内存位置的项目的集合。这种思想的主要概念是将相同数据类型的多个元素存储在一起，其中每个元素的位置可以通过向基值添加偏移量来计算。

换句话说，数组是用来存储和组织数据的。python 允许我们通过提供许多内置函数来检索或更改数据，从而做到这一点。

例如，考虑一个存储在数组中的公司雇员姓名列表。

让我们看一下操作阵列的一些方法:

> ***创建数组***

在 Python 中，可以通过导入数组模块并使用下面的内置函数(语法)来创建数组。

```
**Syntax:** array(data type, value)
```

**程序:**

**创建数据类型为整数的数组**

```
import array as arra = arr.array(‘i’,[1,5,3,6,9])
print (“Integer array: “, end =” “)for i in range (0, 5):
    print (a[i], end =” “)
print()
```

**创建一个数据类型为 float 的数组**

```
f = arr.array(‘d’,[1.2,5.3,3.4,6.5,9.6])print (“Float array: “, end =” “)
for i in range (0, 5):
    print (f[i], end =” “)
print()
```

**输出:**

```
Integer array: 1 5 3 6 9
Float array: 1.2 5.3 3.4 6.5 9.6
```

**解释:**

根据语法，第一个程序创建一个整数数组，第二个程序创建一个小数数组。在这里，我们已经创建了数组。两个程序中提到的字母“I”和“d”分别表示数据类型为整数和小数格式。下一步是“for”循环来显示我们创建的数组。

**注:**

*   确保始终在创建阵列之前导入模块。
*   需要注意的是，我们创建的数组要与数据类型相匹配，否则程序会抛出一个错误。

> ***向数组中添加元素***

当需要向现有阵列添加新数据时，会发生什么情况？Python 提供了使用内置函数向数组添加元素的选项。有两个功能:

*   插入函数允许我们向数组中添加一个或多个元素。在这里，可以在开头、中间、结尾或我们指定的任何索引处插入新元素。

**程序:**

```
import array as arrb = arr.array(‘i’,[1,5,3,6,9])
print (“Before insertion: “, end =” “)for i in range (0, 5):
    print (b[i], end =” “)
print()b.insert(2,8)print(“After insertion:”, end=” “)
for i in (b):
    print(i, end =” “)
print()
```

**输出:**

```
Before insertion: 1 5 3 6 9
After insertion: 1 5 8 3 6 9
```

**解释:**

上面的程序将插入一个索引值为“2”的元素“2”。首先，我们创建了一个数组，并显示它们，如前面的程序所示。在下一步中，我们使用内置函数“insert()”，其中“2”是索引值，“8”是需要插入的元素。最后，在插入元素后显示数组。

*   **append():**append 函数只将数据添加到数组的末尾。

**节目:**

```
import array as arrb = arr.array(‘i’,[1,5,3,6,9])
print (“Before insertion: “, end =” “)
for i in range (0, 5):
    print (b[i], end =” “)
print()b.append(4)
print(“After insertion:”, end=” “)
for i in (b):
    print(i, end =” “)
print()
```

**输出:**

```
Before insertion: 1 5 3 6 9
After insertion: 1 5 3 6 9 4
```

**解释:**

上面的程序将元素“4”添加到给定数组的末尾。该程序与“insert()”程序相同，只有一处变化。这里，我们使用 append()函数，其中“4”是要插入到数组中的元素。现在，您可以在输出中看到值“4”被添加到数组中。

> ***从数组中访问元素***

假设我们需要从数组中访问一个元素。Python 允许我们通过引用索引号来访问数组的元素。

**注意:**索引运算符“[ ]”用于访问数组中的元素。

**程序:**

```
import array as arrc = arr.array(‘i’, [1,5,3,6,9,4])
print(“Element you have accessed:”, c[4])
print(“Element you have accessed:”, c[1])
```

**输出:**

```
Element you have accessed: 9
Element you have accessed: 5
```

**说明:**

上面的程序是从给定的数组中访问元素。假设我们想要访问索引值为“4”和“1”的元素。你要做的就是，用索引操作符调用数组:c[4]，c[1]。现在，您可以在输出中看到从数组中访问的元素。

[](https://medium.com/pythoneers/step-by-step-depth-introduction-of-matplotlib-with-python-8386d75b361d) [## 用 Python 逐步深入介绍 Matplotlib

### 数据科学和机器学习项目的一些有用的例子

medium.com](https://medium.com/pythoneers/step-by-step-depth-introduction-of-matplotlib-with-python-8386d75b361d) [](https://medium.com/pythoneers/algorithms-and-the-need-for-analysis-in-programming-languages-3ffd65ca4bac) [## 程序设计语言中的算法和分析需求

### 理解计算机科学的概念

medium.com](https://medium.com/pythoneers/algorithms-and-the-need-for-analysis-in-programming-languages-3ffd65ca4bac) 

> ***从数组中删除元素***

现在让我们考虑一下，我们之前添加到数组中的元素不再需要了，这意味着该元素需要被移除。Python 程序允许我们使用两个内置函数从数组中删除所需的元素:

*   **remove():**remove()函数允许我们一次只删除一个元素。此外，该函数仅通过元素的第一次出现来移除元素。

**注意:**如果元素不存在，程序会抛出一个错误信息。

**程序:**

```
import arrayd = array.array(‘i’, [1,5,3,6,9,4])
print (“Display Array: “, end =””)
for i in range (0, 6):
    print (d[i], end =” “)
print(“\r”)d.remove(1)
print (“After removing element: “, end =””)
for i in range (0, 5):
    print (d[i], end =” “)
```

**输出:**

```
Display Array: 1 5 3 6 9 4
After removing element: 5 3 6 9 4
```

**解释:**

上面的程序是从给定的数组中删除元素“1”。最初，我们已经创建了一个数组，并显示它们，如前所示。在下一步中，我们使用内置函数“remove()”，其中“1”是要从数组中删除的元素。最后，我们是更新后的数组。

*   **pop():**pop()函数也是一次只删除一个元素。也可以移除使用元素索引指定的元素。

**注意:**默认情况下，该函数从数组末尾移除元素。

**程序:**

```
import arrayd = array.array(‘i’, [1,5,3,6,9,4])
print (“Display Array: “, end =””)
for i in range (0, 6):
    print (d[i], end =” “)print(“\r”)
print (“Popped element: “, end =””)
print (d.pop(1))print (“After popping: “, end =””)
for i in range (0, 4):
    print (d[i], end =” “)
```

**输出:**

```
Display Array: 1 5 3 6 9 4
Popped element: 5
The array after popping is : 1 3 6 9
```

**解释:**

上面的程序是弹出索引值为“1”的元素“5”。如前所述，我们首先创建了一个数组并显示它们。下一步，我们使用内置函数“pop()”，其中“1”是索引值，并打印弹出的元素。最后，我们在元素弹出后显示数组。

> ***切片数组***

让我们考虑我们只需要显示特定范围的元素。Python 允许我们使用切片操作来做到这一点。执行切片操作，使用索引值，只需提及开始和结束索引，就可以显示所需的元素范围。

```
**Syntax:** array[start_index:end_index]
```

**程序:**

```
import arrayd = array.array(‘i’, [1,5,3,6,9,4])
print (“Display Array: “, end =””)for i in range (0, 6):
    print (d[i], end =” “)
array_sliced = d[1:4]
print(“\nSlicing elements from 1–4: “, array_sliced)
```

**输出:**

```
Display Array: 1 5 3 6 9 4
Slicing elements from 1–4: array(‘i’, [5, 3, 6])
```

**说明:**

上述程序显示一系列元素，即从索引值“1”到“4”。如前所述，我们首先创建了一个数组并显示它们。在下一步中，我们调用了数组，起始索引值为“1”，结束索引值为“4”，由切片运算符[:]分隔。该操作存储在一个变量中，并显示输出。

> ***查找数组中的元素***

Python 还允许我们使用内置函数 index()搜索元素。你所要做的就是输入你需要搜索的索引值，函数将返回指定索引的元素。下面的程序告诉你如何让它工作。

**程序:**

```
import arrayd = array.array(‘i’, [1,5,3,6,9,4])
print (“Display Array: “, end =””)
for i in range (0, 6):
    print (d[i], end =” “)print(“\r”)
print (“The occurrence of 6 has an index value: “, end =””)
print (d.index(6))print (“The occurrence of 9 has an index value: “, end =””)
print (d.index(9))
```

**输出:**

```
Display Array: 1 5 3 6 9 4
The occurrence of 6 has an index value: 3
The occurrence of 9 has an index value: 4
```

**解释:**

上面的程序是搜索元素' 6 '和' 9 '是否出现在数组中。此外，为了显示索引值。如前所述，我们首先创建了一个数组并显示它们。在下一步中，使用内置函数“index()”，其中“6”是要在数组中搜索的元素。当找到元素时，函数返回元素的位置，最后，我们显示输出。

> ***更新数组中的元素***

现在让我们考虑一下，我们需要更新现有的数组。你所要做的就是给所需的索引重新赋值。下面的程序告诉你如何让它工作。

**程序:**

```
import arrayd = array.array(‘i’, [1,5,3,6,9,4])
print (“Display Array: “, end =””)
for i in range (0, 6):
    print (d[i], end =” “)print(“\r”)
d[2] = 6
print(“After updating array: “, end =””)
for i in range (0, 6):
    print (d[i], end =” “)
print()
```

**输出:**

```
Display Array: 1 5 3 6 9 4
After updating array: 1 5 6 6 9 4
```

**解释:**

上面的程序是更新一个数组。这里，我们需要在索引值“2”中插入元素“6”。如前所述，我们首先创建了一个数组并显示它们。在下一步中，我们将元素“6”分配给一个索引值为“2”(d[2]= 6)的数组。最后，我们显示输出。

> ***结论***

总之，本文涵盖了所有关于数组的主题，包括处理数组上的操作。我强烈建议参考更多的内容，并使用 python 程序应用这些概念。您还可以用几个函数和概念来试验这个主题。

我希望你喜欢这篇文章。通过我的 [LinkedIn](https://www.linkedin.com/in/data-scientist-95040a1ab/) 和 [twitter](https://twitter.com/amitprius) 联系我。

# 推荐文章

1.[8 Python 的主动学习见解收集模块](https://pub.towardsai.net/8-active-learning-insights-of-python-collection-module-6c9e0cc16f6b)
2。 [NumPy:图像上的线性代数](https://pub.towardsai.net/numpy-linear-algebra-on-images-ed3180978cdb?source=friends_link&sk=d9afa4a1206971f9b1f64862f6291ac0)3。[Python 中的异常处理概念](https://pub.towardsai.net/exception-handling-concepts-in-python-4d5116decac3?source=friends_link&sk=a0ed49d9fdeaa67925eac34ecb55ea30)
4。[熊猫:处理分类数据](https://pub.towardsai.net/pandas-dealing-with-categorical-data-7547305582ff?source=friends_link&sk=11c6809f6623dd4f6dd74d43727297cf)
5。[超参数:机器学习中的 RandomSeachCV 和 GridSearchCV](https://pub.towardsai.net/hyper-parameters-randomseachcv-and-gridsearchcv-in-machine-learning-b7d091cf56f4?source=friends_link&sk=cab337083fb09601114a6e466ec59689)
6。[用 Python](https://medium.com/towards-artificial-intelligence/fully-explained-linear-regression-with-python-fe2b313f32f3?source=friends_link&sk=53c91a2a51347ec2d93f8222c0e06402)
7 全面讲解了线性回归。[用 Python](https://medium.com/towards-artificial-intelligence/fully-explained-logistic-regression-with-python-f4a16413ddcd?source=friends_link&sk=528181f15a44e48ea38fdd9579241a78)
充分解释了 Logistic 回归 8。[数据分发使用 Numpy 与 Python](https://pub.towardsai.net/data-distribution-using-numpy-with-python-3b64aae6f9d6?source=friends_link&sk=809e75802cbd25ddceb5f0f6496c9803)
9。[机器学习中的决策树 vs 随机森林](https://pub.towardsai.net/decision-trees-vs-random-forests-in-machine-learning-be56c093b0f?source=friends_link&sk=91377248a43b62fe7aeb89a69e590860)
10。[用 Python 实现数据预处理的标准化](https://pub.towardsai.net/standardization-in-data-preprocessing-with-python-96ae89d2f658?source=friends_link&sk=f348435582e8fbb47407e9b359787e41)