# 您可能不知道但应该知道的 Python 特性

> 原文：<https://levelup.gitconnected.com/python-features-you-probably-dont-know-about-but-should-a66c6b30c528>

## 一些鲜为人知但很有用的 python 特性

![](img/109cc89f9fead1c096686d5f01ec6b86.png)

Alex Kotliarskyi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 是开发人员中最流行的语言之一。它以其简单的语法和在各个领域的应用而闻名，尤其是在数据科学领域。它还有一个最活跃的开源开发者社区，不断地在 Python 中添加新特性。

Python 配备了 290，000 多个包，这些包在 Python 包索引(PyPi)中可用。这些软件包被广泛用于不同的目的，如 web 开发、机器学习、科学计算等等。

对 Python 了解得越多，就越了解它在不同领域的特性和应用。今天，我想出了一些不太流行但非常有用的 python 特性。

在本文中，我们将讨论一些有用的 python 特性，您可以在不同的场景中加以利用。我们开始吧！

# 1.命名元组

命名元组是易于创建的轻量级对象类型。它是 Python 的`Collections`模块中可用的工厂函数。如果希望有一个类来管理数据，可以考虑使用命名元组作为替代。

```
Output:location: Coordinate(longitude=90, latitude=37.5)
(90, 37.5)
```

创建一个命名的元组类简单明了，您确实需要为自定义类编写样板代码。

# 2.为...Else 子句

我们都知道我们在使用`if`条件的同时使用了`else`子句，但是您知道我们也可以在`for`循环中使用`else`子句吗？

在 For…else 子句中，如果循环的**迭代完成，则执行 **else** 子句。如果 for 循环中的迭代由于 break 语句而中断，在这种情况下， **else** 子句不会被执行。**

```
Output:
#case 1
f
o
o
All letters have been printed#case 2
f
o
```

这里，在案例 2 中，由于 break 语句的执行，else 语句没有被执行。

# 3.参数解包

当函数调用时，当我们需要传递一些东西给一个有多个参数的函数时，我们通常会传递单独的参数。

在 Python 中，我们可以使用`*`和`**`解包一个列表或字典作为函数参数，在函数调用时传递它。

```
def total(a, b, c):
    return a+b+clist1 = [1,2,3]
print(total(*list1))Output:
6
```

当传递给函数时，Python 本身不解包列表、元组或字典，必须使用`*`和`**`才能成功地将它们传递给函数。

# 4.使用 pprint 打印效果更好

`pprint`是 Python 标准库中一个有用的内置模块。顾名思义，pprint(pretty printer)，这个模块用于以更好的格式打印数据和任意 Python 数据结构，更容易阅读。

该模块以良好的格式和更易读的方式打印数据。使用 **pprint** 功能打印的数据比默认内置的 **print** 功能更具可读性。

```
Output:#print function
[{'status': 200, 'result': [1, 2, 3, 4, 5]}, {'status': 'OK', 'result': ['Hello', 'World']}, {'status': 404, 'result': 'Data not found'}]#pprint function
[{'result': [1, 2, 3, 4, 5], 'status': 200},
 {'result': ['Hello', 'World'], 'status': 'OK'},
 {'result': 'Data not found', 'status': 404}]
```

我们可以看到 pprint 函数的输出格式可读性更好，也更干净。

# 5.用 enum 枚举

在 Python 中，可以使用`enum`模块实现枚举。枚举对于定义一组不可变的相关常量值很有用，这些常量值可能有也可能没有语义意义。这种值的一个例子是一周中的几天(星期日到星期六)。

枚举是使用类创建的，并且具有与其相关联的**名称**和**值**。

```
Output:
summer
2winter
1
```

# 结论

这就是这篇文章的全部内容。在本文中，我们讨论了一些有用的 Python 特性，它们可以用在不同的场景中。您可以在项目中利用这些特性。

通过在实时项目中实践，您可以方便地使用 Python 的这些特性。

感谢阅读！

你可以在这里免费订阅我的时事通讯: [Pralabh 的时事通讯](https://pralabhsaxena.medium.com/subscribe)