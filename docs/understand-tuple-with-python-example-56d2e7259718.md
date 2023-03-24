# 用 Python 例子理解 tuple()

> 原文：<https://levelup.gitconnected.com/understand-tuple-with-python-example-56d2e7259718>

## python 中数据结构的便捷概念

![](img/440a7460fec48d054b7875a7c3f5fb17.png)

由 [Pakata Goh](https://unsplash.com/@pakata?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

元组是表示为()的 python 对象的集合，元组中的元素用逗号分隔。元组类似于嵌套对象和索引等主题中的列表。但是与列表相比，元组是不可变的。

> ***创建元组***

创建一个元组与创建一个列表非常相似，只是略有不同。让我们看看如何创建元组的不同方法:

**注意:**一个元组也可以不用括号写。

1.  **创建空元组**

```
tup1 = ()print(tup1)**Output:** ()
```

**2。混合数据类型的元组**

```
tup2 = (1, “python”, 26.4)print(tup2)**Output:** (1, “python”, 26.4)
```

**3。嵌套元组**

```
tup3 = (“python”, (34, 35), [4, 7])print(tup3)**Output:** (“python”, (34, 35), [4, 7])
```

**说明:**以上执行的程序以不同的方式说明了元组的创建。这里，每个创建的元组都在类似“tup1”、“tup2”和“tup3”的变量中。

> ***访问元组***

既然我们已经创建了元组，接下来的需求就是访问它们。有三种访问方式:正索引、负索引和切片。让我们通过一个示例来详细了解这些方法:

1.  **正向分度**

**程序:**

```
tup = (‘p’, ‘y’, ‘t’, ‘h’, ‘o’, ’n’, (4, 5, 6), [56, 47])
        ↑    ↑    ↑    ↑    ↑    ↑       ↑          ↑
        0    1    2    3    4    5       6          7print(tup[4])
print(tup[6][1])
print(tup[7][0])**Output:** o
5
56
```

**解释:**

在正索引中，从左到右访问元组，其中第一个元素的索引总是从“0”开始。让我们了解一下打印语句是如何访问它们的:

*   在第一个 print 语句中，它声明访问索引值为 4 的元素。因此我们得到输出为“0”。
*   在第二个 print 语句中，它声明访问索引值为“6”的元素。我们可以看到在元组中有一个元组。因此，为了打印索引值为“6”的元组的元素，我们将它的位置设为 1。因此，它将输出返回为“5”。该过程也适用于第三张打印报表。

**2。负分度**

**程序:**

```
tup = (‘p’, ‘e’, ‘t’, ‘i’, ‘o’, ‘u’)
        ↑    ↑    ↑    ↑    ↑    ↑
      -6    -5   -4   -3   -2   -1print(tup[-3])**Output:** i
```

**说明:**

在负索引中，从右到左访问元组，其中第一个元素的索引总是以'-1 '开始。在上面的 print 语句中，要求访问一个索引值为“-3”的元素。因此，输出是“I”。

**3。切片**

**程序:**

```
tup = (‘p’, ‘r’, ‘t’, ‘l’, ‘o’, ‘n’)
        ↑    ↑    ↑    ↑    ↑    ↑
        0    1    2    3    4    5print(tup[1:3])
print(tup[2:])
print(tup[:-4])
print(tup[:])**Output:** (‘r’, ‘t’, ‘l’)
(‘t’, ‘l’, ‘o’, ‘n’)
(‘p’, ‘r’)
(‘p’, ‘r’, ‘t’, ‘l’, ‘o’, ‘n’)
```

**解释:**

元组切片允许我们获得定义范围的元素。现在让我们来看看每个打印语句表示什么:

*   在第一个 print 语句中，要求从索引位置 1 到 3 获取元素。因此我们得到的输出是(' r '，' t '，' l ')。
*   在第二个 print 语句中，要求从索引位置 2 开始获取所有元素。因此我们得到的输出是(' t '，' l '，' o '，' n ')。
*   第三条语句表示打印索引位置'-4 '之前的所有语句。因此我们得到的输出为(' p '，' r ')。
*   在第四个 print 语句中，':'运算符用于显示元组的所有元素。

[](https://pub.towardsai.net/become-a-data-scientist-in-2021-with-these-following-steps-5bf70a0fe0a1) [## 按照以下步骤，在 2021 年成为一名数据科学家

### 走上数据科学家之路需要具备的基本点

pub.towardsai.net](https://pub.towardsai.net/become-a-data-scientist-in-2021-with-these-following-steps-5bf70a0fe0a1) [](https://pub.towardsai.net/python-zero-to-hero-with-examples-c7a5dedb968b) [## Python:从零到英雄(带示例)

### python 初学者手册指南

pub.towardsai.net](https://pub.towardsai.net/python-zero-to-hero-with-examples-c7a5dedb968b) 

> ***修改一个元组***

1.  **替换元组中的元素**

**程序**

```
p = (‘p’, ‘y’, ‘t’, ‘h’, ‘o’, ’n’, (4, 5, 6), [56, 47])p[5] = 8
print(p)**Output:** (‘p’, ‘y’, ‘t’, ‘h’, ‘o’, 8, (4, 5, 6), [56, 47])
```

**2。重新分配元组**

```
#Tuples can also be re-assignedp = (‘p’, ‘u’, ‘t’, ‘i’, ‘o’, ‘t’)print(p)**Output:** (‘p’, ‘u’, ‘t’, ‘i’, ‘o’, ‘t’)
```

**说明:**

*   在第一个程序中，我们在索引位置 5 赋值“8”。运行程序后，我们可以看到值“8”被添加到索引位置 5。
*   在第二个程序中，我们将元组“(' p '，' y '，' t '，' h '，' o '，' n '，(4，5，6)，[56，47])”替换为“(' p '，' u '，' t '，' I '，' o '，' t ')”。在执行程序时，我们可以观察到发生的变化。

> ***删除元组***

在元组中，元素不能像列表那样被删除。只能删除整个元组。让我们看一个例子:

**程序:**

```
y = (‘p’, ‘h’, ‘t’, ‘t’, ‘o’, ‘e’)del y
print(y)**Output:** Traceback (most recent call last):
    File “<string>”, line 12, in <module>
NameError: name ‘my_tuple’ is not defined
```

> ***元组方法***

元组中只有 2 种方法可用。让我们用一个编程示例来看看它们:

1.  **计数()**

count 方法返回位于元组中的元素的位置值。(计数值从 1 开始)

**程序:**

```
a = (p’, ‘r’, ‘t’, ‘u’, ‘o’, ‘l’)print(a.count(‘t’))**Output:** 3
```

**解释:**这里的要求是返回元组‘a’中元素‘t’的位置。因此得到的输出是 3。

**2。索引()**

index 方法返回位于元组中的元素的索引值。(索引值从 0 开始)

**程序:**

```
b = (p’, ‘d’, ‘t’, ‘g’, ‘o’, ‘m’)print(a.index(‘t’))**Output:** 2
```

**解释:**

这里的要求是返回元组' a '中元素' t '的索引值。因此得到的输出是 2。

> ***元组成员测试***

元组中的成员资格测试是检查定义的元素是否出现在列表中。

**程序:**

```
member = (p’, ‘e’, ‘t’, ‘f’, ‘o’, ‘c’)print(‘t’ in member)
print(‘b’ in member)**Output:** True
False
```

**说明:**

这里，第一个 print 语句要求检查元素“t”是否出现在元组中。因为元素存在于元组中，所以输出为真。而第二条语句返回 false，因为元组中不存在元素“b”。

> ***迭代使用元组***

元组中的迭代类似于列表中的迭代。“For”循环也用在元组中。让我们看一个例子:

**程序:**

```
for word in (‘apple’, ‘oranges’)
    print(“I like”, word)**Output:** I like apples
I like oranges
```

**解释:**

这里，for 循环打印元组中的每个元素，直到到达列表的末尾。

我希望你喜欢这篇文章。通过我的 [LinkedIn](https://www.linkedin.com/in/data-scientist-95040a1ab/) 和 [twitter](https://twitter.com/amitprius) 联系我。

# 推荐文章

[1。NLP —零到英雄与 Python](https://medium.com/towards-artificial-intelligence/nlp-zero-to-hero-with-python-2df6fcebff6e?sk=2231d868766e96b13d1e9d7db6064df1)
2。 [Python 数据结构数据类型和对象](https://medium.com/towards-artificial-intelligence/python-data-structures-data-types-and-objects-244d0a86c3cf?sk=42f4b462499f3fc3a160b21e2c94dba6)3 .[Python 中的异常处理概念](https://pub.towardsai.net/exception-handling-concepts-in-python-4d5116decac3?source=friends_link&sk=a0ed49d9fdeaa67925eac34ecb55ea30)
4。[为什么 LSTM 在深度学习方面比 RNN 更有用？](https://pub.towardsai.net/deep-learning-88e218b74a14?source=friends_link&sk=540bf9088d31859d50dbddab7524ba35)
5。[神经网络:递归神经网络的兴起](https://pub.towardsai.net/neural-networks-the-rise-of-recurrent-neural-networks-df740252da88?source=friends_link&sk=6844935e3de14e478ce00f0b22e419eb)
6。[用 Python](https://medium.com/towards-artificial-intelligence/fully-explained-linear-regression-with-python-fe2b313f32f3?source=friends_link&sk=53c91a2a51347ec2d93f8222c0e06402)
7 全面讲解了线性回归。[用 Python](https://medium.com/towards-artificial-intelligence/fully-explained-logistic-regression-with-python-f4a16413ddcd?source=friends_link&sk=528181f15a44e48ea38fdd9579241a78)
充分解释了 Logistic 回归 8。[concat()、merge()和 join()与 Python](https://pub.towardsai.net/differences-between-concat-merge-and-join-with-python-1a6541abc08d?source=friends_link&sk=3b37b694fb90db16275059ea752fc16a)
的区别 9。[与 Python 的数据角力—第一部分](https://pub.towardsai.net/data-wrangling-with-python-part-1-969e3cc81d69?source=friends_link&sk=9c3649cf20f31a5c9ead51c50c89ba0b)
10。[机器学习中的混淆矩阵](https://medium.com/analytics-vidhya/confusion-matrix-in-machine-learning-91b6e2b3f9af?source=friends_link&sk=11c6531da0bab7b504d518d02746d4cc)