# Python 排序的终极指南

> 原文：<https://levelup.gitconnected.com/the-ultimate-guide-to-sorting-in-python-d07349fb96d5>

## 了解如何在 Python 中对列表、元组、字符串和字典进行排序

![](img/17db2987de611bdaa388ad17f6436109.png)

[天一马](https://unsplash.com/@tma?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

在本教程中，我们将看看如何根据不同的标准对可重复项进行排序，例如列表、元组、字符串和字典。

[](https://towardsdatascience.com/iterables-and-iterators-in-python-849b1556ce27) [## Python 中的迭代器和迭代器

### Python 中的可迭代对象、迭代器和迭代

towardsdatascience.com](https://towardsdatascience.com/iterables-and-iterators-in-python-849b1556ce27) 

# 对列表进行排序

有两种方法对列表进行排序。我们可以使用 sort()方法或 sorted()函数。sort()方法是一个列表方法，因此只能用于列表。sorted()函数适用于任何 iterable。

## sort()方法

sort()方法是一个列表方法，它就地修改列表并返回 None。换句话说，sort()方法修改或更改它所调用的列表，而不创建新列表。

sort()方法有两个可选参数:key 参数和 reverse 参数。key 参数接受一个函数，该函数接受一个参数并返回一个用于排序的键。默认情况下，sort()方法将按数值对一系列数字进行排序，并按字母顺序对一系列字符串进行排序。reverse 参数接受布尔值 True 或 False。reverse 的默认值为 False，这意味着它按升序排序。要按降序排序，我们应该设置 reverse=True。当我们看下面的一些例子时，这些参数会更有意义。

## 对数字列表进行排序

假设我们有一个数字列表，我们想按升序排序。

```
num_list = [1,-5,3,-9,25,10]num_list.sort()print(num_list)
# [-9,-5,1,3,10,25]
```

所以我们有一个数字列表，或者说 *num_list。*我们在这个列表上调用 sort()方法。请注意，我们没有为 key 参数传入值。因此，它只是按照实际值对这个数字列表进行排序。由于我们没有设置 reverse = True，所以它按升序排序。sort()方法修改了我们的 *num_list* 。

如果我们想根据数字的绝对值对列表进行排序呢？这就是我们需要使用关键参数的时候。key 参数接受一个函数，该函数接受一个参数并返回一个用于排序的键。

```
num_list = [1,-5,3,-9,25,10]def absolute_value(num):
    return abs(num)num_list.sort(key = absolute_value)print(num_list) 
# [1,3,-5,-9,10,25]
```

我们定义了一个函数， *absolute_value* ，它接受一个数字并返回其绝对值。然后，我们将这个函数作为 sort()方法的关键参数传入。因此，在进行比较之前，它通过绝对值函数运行 *num_list* 的每个元素或数字。因此，数字的绝对值用于按升序对列表进行排序(因为 reverse 默认设置为 False)。

## 使用 lambda 表达式

我们可以为 key 参数传入一个 lambda 表达式，如下所示:

```
num_list.sort(key = lambda num: abs(num))
```

*了解 lambda 函数的更多信息:*

[](https://towardsdatascience.com/lambda-expressions-in-python-9ad476c75438) [## Python 中的 Lambda 表达式

### 如何用 python 写匿名函数

towardsdatascience.com](https://towardsdatascience.com/lambda-expressions-in-python-9ad476c75438) 

记住 sort()方法返回 None。因此，如果我们将 sort()方法的输出或返回值设置为一个新变量，我们将得不到任何值，如下所示:

```
new_list = num_list.sort(key = absolute_value)print(new_list)
# None
```

## 使用内置函数

我们可以不像上面那样编写自己的 absolute_value 函数，而是直接为关键参数传入 python 内置的 abs()函数，如下所示:

```
num_list.sort(key = abs)
```

[](https://towardsdatascience.com/two-cool-functions-to-know-in-python-7c36da49f884) [## Python 中需要了解的两个很酷的函数

### 了解如何使用 Python 中的 tqdm 制作表格和显示进度条

towardsdatascience.com](https://towardsdatascience.com/two-cool-functions-to-know-in-python-7c36da49f884) 

## sorted()函数

sorted()函数可以接受三个参数:iterable、key 和 reverse。换句话说，sort()方法只对列表有效，但是 sorted()函数可以对任何可迭代对象有效，比如列表、元组、字典等等。然而，与返回 None 并修改原始列表的 sort()方法不同，sorted()函数返回一个新列表，同时保持原始对象不变。

让我们再次使用绝对值对 *num_list* 进行排序，但是使用 sorted()函数:

```
num_list = [1,-5,3,-9,25,10]new_list = sorted(num_list, key = abs)print(new_list) 
# [1,3,-5,-9,10,25]print(num_list)
# [1,-5,3,-9,25,10]
```

我们将 iterable、 *num_list* 传递给 sorted()函数，并将内置的 *abs* 函数传递给 key 参数。我们将 sorted()函数的输出设置为一个新变量， *new_list* 。请注意 *num_list* 是如何保持不变的，因为 sorted()函数没有修改它所作用的 iterable。

> 注意:无论向 sorted()函数传递什么 iterable，它总是返回一个列表。

## 对元组列表进行排序

假设我们有一个元组列表。列表中的每个元素都是一个包含三个元素的元组:姓名、年龄和薪水。

```
list_of_tuples = [
    ('john', 27, 45000),
    ('jane', 25, 65000),
    ('beth', 31, 70000)
]
```

我们可以按字母顺序、年龄或薪水对这个列表进行排序。我们可以指定我们想要使用的密钥参数。

要按年龄排序，我们可以使用以下代码:

```
sorted_age_list = sorted(list_of_tuples, key = lambda person: person[1])print(sorted_age_list) 
# [('jane', 25, 65000), ('john', 27, 45000), ('beth', 31, 70000)]
```

*list_of_tuples* 的每个元素都作为 *person 参数*传递给 lambda 函数。返回每个元组的索引 1 处的元素。这是用于对列表进行排序的值，即年龄。

要按字母顺序对名称进行排序，我们可以不传递任何键，因为默认情况下，每个元组的第一个元素就是要比较的内容(记住，默认情况下，字符串是按字母顺序排序的):

```
sorted_name_list = sorted(list_of_tuples)print(sorted_name_list) 
# [('beth', 31, 70000), ('jane', 25, 65000), ('john', 27, 45000)]
```

但是，我们可以指定要按每个元组的第一个元素进行排序，如下所示:

```
sorted_name_list = sorted(list_of_tuples, key = lambda person: person[0])print(sorted_name_list) 
# [('beth', 31, 70000), ('jane', 25, 65000), ('john', 27, 45000)]
```

记住，我们可以将一个 lambda 表达式赋给一个变量(类似于使用 def 关键字定义一个函数)。因此，我们可以根据 lambda 表达式用来对列表进行排序的标准来组织它们:

```
name = lambda person: person[0]
age = lambda person: person[1]
salary = lambda person: person[2]# sort by name
sorted(list_of_tuples, key = name)# sort by age
sorted(list_of_tuples, key = age)# sort by salary
sorted(list_of_tuples, key = salary)
```

[](https://towardsdatascience.com/5-useful-things-to-know-in-python-dac0bc585673) [## Python 中需要知道的 5 件有用的事情

### python 中 5 个非常有用的函数和表达式

towardsdatascience.com](https://towardsdatascience.com/5-useful-things-to-know-in-python-dac0bc585673) 

## itemgetter()函数

我们可以使用运算符模块中的 itemgetter()函数，而不是使用 lambda 表达式来访问元组中的姓名、年龄或薪水元素。我们可以通过传入索引来指定要访问元组中的哪个元素。列表中的每个元组都被传递给 itemgetter()函数，并根据指定的索引返回该元组中的特定元素。

```
import operator# sort by name
sorted(list_of_tuples, key = operator.itemgetter(0))# sort by age
sorted(list_of_tuples, key = operator.itemgetter(1))# sort by salary
sorted(list_of_tuples, key = operator.itemgetter(2))
```

itemgetter()函数允许多级排序。例如，假设我们有这样一个元组列表:

```
list_of_tuples = [
 ('john', 27, 45000),
 ('jane', 25, 65000),
 ('joe', 25, 35000),
 ('beth', 31, 70000)
]
```

注意简和乔的年龄是一样的。因此，如果我们想先按年龄，再按薪水对列表进行排序，我们可以向 itemgetter()函数传递两个值:

```
print(sorted(list_of_tuples, key=operator.itemgetter(1,2))# [('joe', 25, 35000), ('jane', 25, 65000), ('john', 27, 45000), ('beth', 31, 70000)]
```

因为 age 的索引是传入的第一个值，所以它将首先用于对元素进行排序。如果年龄相同，将使用薪金对元素进行排序。

注意:operator 模块还有 attrgetter()函数，可用于对具有命名属性的对象进行排序。例如，如果我们编写了自己的类并实例化了该类的对象，我们可以通过使用 attrgetter()函数使用特定的命名属性对这些对象进行排序。我们只需将属性的名称传递给 attrgetter()函数，然后将该函数传递给 sorted()函数的关键参数。例如，要按年龄对对象进行排序，我们可以将以下内容传递给 key 参数:key = attrgetter('age ')。

[](https://towardsdatascience.com/three-functions-to-know-in-python-4f2d27a4d05) [## Python 中需要了解的三个函数

### 了解如何使用 python 中的映射、过滤和归约函数

towardsdatascience.com](https://towardsdatascience.com/three-functions-to-know-in-python-4f2d27a4d05) 

# 排序元组

对元组排序与前面看到的用 sorted()函数对列表排序是一样的。我们不能使用 sort()方法，因为它是一个列表方法。此外，因为元组是不可变的对象，所以我们不能使用 sort()方法是有意义的，因为 sort()方法修改了原始列表。

> 记住，即使我们将一个元组传递给 sorted()函数，返回的也是一个列表。

```
num_tuple = (5,2,53,9,25)sorted_tuple = sorted(num_tuple)print(sorted_tuple)
# [2,5,9,25,53]
```

# 对字符串排序

字符串是可迭代的。因此，也可以使用 sorted()函数对它们进行排序。sorted()函数将逐个字符地遍历字符串。默认情况下，sorted()函数将按字母顺序对字符串进行排序。

```
sorted_string = sorted(‘dinosaur’)print(sorted_string)
# ['a','d','i','n','o','r','s','u']
```

注意 sorted()函数是如何按字母顺序返回字符列表的。

# 整理字典

字典由键:值对组成。因此，可以按键或值对它们进行排序。

假设我们有一个字典，键是名字，值是年龄。

```
dictionary_of_names = {'beth': 37, 
                       'jane': 32,
                       'john': 41, 
                       'mike': 59
}
```

如果我们只是将整个字典作为 iterable 传递给 sorted()函数，我们将得到以下输出:

```
print(sorted(dictionary_of_names))
# ['beth', 'jane', 'john', 'mike']
```

正如我们所看到的，如果我们将整个字典作为 iterable 传递给 sorted()函数，它将返回一个只包含按字母顺序排序的键的列表。

## 使用 items()方法

如果我们想获得整个字典的排序副本，我们需要使用 dictionary items()方法:

```
print(dictionary_of_names.items())
# dict_items([('beth', 37), ('jane', 32), ('john', 41), ('mike', 59)])
```

注意 items()方法如何返回一个 dict_items 对象，它看起来类似于一个元组列表。这个 dict_items 对象是一个 iterable 对象。因此，它可以作为 iterable 传递给 sorted()函数。

我们可以对这个 dict_items 对象进行排序，就像我们对前面看到的元组列表进行排序一样。例如，要按每个元组中的第二个元素(即年龄)进行排序，我们可以使用以下代码:

```
sorted_age = sorted(dictionary_of_names.items(), key = lambda kv: kv[1])print(sorted_age)
# [('jane', 32), ('beth', 37), ('john', 41), ('mike', 59)]
```

注意 sorted()函数是如何返回一个元组列表的，这个列表是按照年龄(或者每个元组中的第二个元素)排序的。要将这个元组列表转换成一个字典，我们可以使用内置的 dict()函数:

```
sorted_dictionary = dict(sorted_age)print(sorted_dictionary)
# {'jane': 32, 'beth': 37, 'john': 41, 'mike': 59}
```

现在我们有了按年龄排序的字典！

如果你喜欢阅读这样的故事，并想支持我成为一名作家，考虑注册成为一名媒体会员。每月 5 美元，你可以无限制地阅读媒体上的故事。如果你用我的 [*链接*](https://lmatalka90.medium.com/membership) *注册，我会赚一小笔佣金。*

[](https://lmatalka90.medium.com/membership) [## 通过我的推荐链接加入媒体——卢艾·马塔尔卡

### 阅读卢艾·马塔尔卡的每一个故事(以及媒体上成千上万的其他作家)。您的会员费直接支持…

lmatalka90.medium.com](https://lmatalka90.medium.com/membership) 

## 结论

在本教程中，我们比较了排序列表时的 sort()方法和 sorted()函数。我们学习了 sort()方法如何修改原始列表，sorted()函数返回一个新列表。我们还了解到 sort()方法只适用于列表，但是 sorted()函数可以适用于任何 iterable。然后，我们学习了如何使用不同的标准对不同类型的可重复项进行排序。