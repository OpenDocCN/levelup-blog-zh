# Python 中的数据结构排序

> 原文：<https://levelup.gitconnected.com/sorting-data-structures-in-python-ceba201d33d2>

![](img/709cf5d1c2ad0f6806321877696430a1.png)

照片由[卢克·切瑟](https://unsplash.com/@lukechesser?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Python 为您提供了一些非常方便的函数，您可以使用它们高效地进行排序。您甚至可以进行链式排序，这样对于相同的数据，您可以先进行主排序，然后再进行次排序。

所以，事不宜迟，我们开始吧。

# 排序基础

Python 中有两种主要的排序方式:

*   `sort`列表附带的方法
*   `sorted`接受任何迭代的函数

默认情况下，两者都会按升序对数据进行排序。但是这两种方法之间有一个重要的区别:

*   `sort`就地执行排序操作。所以你最终替换了原来的列表。
*   `sorted`执行不适当的分类操作。这意味着，它返回一个新的列表，其中包含您的排序数据，而原始列表保持不变。这样，如果需要的话，两者都可以使用。

你唯一想使用`sort`而不是`sorted`的地方是当你有一个庞大的列表，并且你知道你不需要它的未排序版本的时候。这是因为，通过就地排序，`sort`避免了创建全新列表的内存开销。

只有当您的列表非常大时，它才会对性能产生影响！否则，总是使用`sorted`,因为它更具功能性和确定性。如果不改变现有的数据结构，就不太可能遇到错误。

# 数字或字符串列表

首先，我们要看看最基本的情况，这将是一个数字或字符串的列表。

```
data_of_numbers = [50, 30, 20, 80, 10]
data_of_strings = ['d', 'a', 'c', 'b']data_of_numbers.sort()
sorted_numbers = sorted(data_of_numbers)# Output:
# [10, 20, 30, 50, 80]
# ['a', 'b', 'c', 'd']
```

默认情况下，Python 按升序对数据进行排序。如果你想要降序，你可以传入一个`reverse`参数:

```
sorted_numbers = sorted(data_of_numbers, reverse=True)
sorted_strings = sorted(data_of_strings, reverse=True)
```

# 元组或字典列表

现在，让我们看看稍微复杂一点的列表。如果你的列表包含元组或者字典怎么办？

```
data_points = [(10, 'c'), (40, 'b'), (20, 'a')]
sorted_data = sorted(data_points) # [(10, 'c'), (20, 'a'), (40, 'b')]
```

默认情况下，Python 根据元组中的第一个元素对元组进行排序。所以你才会看到顺序:10，20，40。假设您想按第二个元素排序。你是做什么的？

在这种情况下，您可以使用接受匿名函数的`key`参数(或`lambda`)。

```
data_points = [(10, 'c'), (40, 'b'), (20, 'a')]
sorted_data = sorted(data_points, key=lambda x: x[1])# [(20, 'a'), (40, 'b'), (10, 'c')]
```

lambda 函数接受当前元组作为参数。所以您可以在 tuple 中选择想要排序的项。

字典的工作方式非常相似。假设我们有一个员工列表:

```
employees = [{'name': 'Max', 'id': 30}, {'name': 'John', 'id': 20}, {'name': 'Ben', 'id': 100}]
sorted_employees = sorted(employees)# Errors out. Can't compare dictionary objects directly
```

如您所见，它出错是因为 Python 不知道如何在排序时比较两个字典条目。因此，您必须明确地告诉 Python，您希望根据字典中的哪个属性进行排序:

```
sorted_employees_by_name = sorted(employees, key=lambda x: x['name'])
sorted_employees_by_id = sorted(employees, key=lambda x: x['id'])# [{'name': 'Ben', 'id': 100}, {'name': 'John', 'id': 20}, {'name': 'Max', 'id': 30}]
# [{'name': 'John', 'id': 20}, {'name': 'Max', 'id': 30}, {'name': 'Ben', 'id': 100}]
```

# 自定义对象列表

现在，让我们来看看自定义对象的列表。用 Python 创建类来保存数据是一种很常见的做法。我们可以有一个这些对象的列表，我们可能想把它们按不同的顺序排序。

```
class Car():
	def __init__(self, registration, wheels, color):
		self.registration = registration
		self.wheels = wheels
		self.color = color def __repr__(self):
		return f'{self.registration}, {self.wheels}, {self.color}'car_1 = Car(registration=10, wheels=2, color='blue')
car_2 = Car(registration=100, wheels=4, color='red')
car_3 = Car(registration=70, wheels=1, color='green')
```

对这些对象进行排序非常简单。这个过程和我们刚才讨论的几乎完全一样。您只需使用`key`参数定义想要排序的属性。

```
cars_list = [car_1, car_2, car_3]cars_by_registration = sorted(cars_list, key=lambda x: x.registration)
cars_by_wheels = sorted(cars_list, key=lambda x: x.wheels)
cars_by_color = sorted(cars_list, key=lambda x: x.color)# [10, 2, blue, 70, 1, green, 100, 4, red]
# [70, 1, green, 10, 2, blue, 100, 4, red]
# [10, 2, blue, 70, 1, green, 100, 4, red]
```

我们以三种不同的顺序对相同的数据排序三次。如您所见，我们根据排序键对每个排序列表进行了正确排序。

# 链接排序在一起

到目前为止，无论你学到了什么，都是你在数据结构上做任何排序所需要的基础。然而，Python 有一些实用函数，可以用来使代码更加整洁，排序更加强大。现在让我们来看看。

您可以使用 Python 的`operator`模块更清晰地定义您的排序键，并将多个排序链接在一起。

您可以使用`itemgetter`函数来定义想要从元组中获取哪个元素。这使得代码更加清晰易读。

```
from operator import itemgetterdata_points = [(10, 'c'), (40, 'b'), (20, 'a')]
sorted(data_points, key=itemgetter(2))
```

类似地，如果您正在对一个对象列表进行排序，您可以使用`attrgetter`来定义对象的哪个属性被定义为排序关键字。

```
from operator import attrgetter
cars_list = [car_1, car_2, car_3]
cars_by_registration = sorted(cars_list, key=attrgetter('registration'))
```

同样，如你所见，这样的代码更容易阅读。

使用`operator`模块最酷的好处是，你可以将一系列排序链接在一起，让你的数据按照你想要的方式排列。

比方说，对于汽车数据，您希望首先按注册登记对列表进行排序，然后再按其中的车轮进行排序。让我们看看如何才能轻松做到这一点。

```
car_4 = Car(registration=10, wheels=1, color='yellow')
cars_list = [car_1, car_2, car_3, car_4]cars_by_registration_then_wheels = sorted(cars_list, key=attrgetter('registration', 'wheels'))
```

这种方式链接起来非常简单，所以您可以链接任意多的排序，完全按照您想要的方式获取数据！`operator`模块还确保您以最有效的方式进行多重排序。

这就是 Python 中对数据结构排序的全部内容。现在，您应该能够按照您想要的任何顺序对数据进行排序和呈现。

谢谢你能走到这一步。如果你对这些教程以及其他与编程和生产力相关的东西感兴趣，请跟我来。