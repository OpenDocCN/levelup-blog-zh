# 学习 Python:列表和列表方法(和函数)

> 原文：<https://levelup.gitconnected.com/learning-python-lists-and-list-methods-and-functions-809d57870156>

![](img/3abdd7586812e65370a9861b4af0fb14.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

列表是用于在 Python 程序中存储内存数据的主要容器(数据结构)。在本文中，我将描述如何在 Python 中使用列表，并演示许多可以用来处理列表的内置方法。我还将研究一些 Python 函数，这些函数将列表作为参数，以及一两个可以用于列表的关键字(in 和 not in)。

# 定义并演示了 Python 列表

列表是存储在单个名称下的数据的顺序集合。Python 列表可以与其他语言中的数组和向量相媲美。Python 列表区别于其他语言中类似容器的一个特性是，Python 列表可以在同一个列表中存储多种类型的数据。

通过给变量分配一组空括号来创建列表。以下是创建新列表的语法模板:

`*list-name = []*`

下面是一个创建新列表的代码片段示例:

`numbers = []`

您也可以创建一个已有数据的新列表，如下例所示:

`names = [“Cynthia”, “Jonathan”, “Raymond”, “Danny”]`

据说列表中的元素是按照它们的索引位置存储的。列表的起始索引位置始终是 0，后面是 1、2、3 等等。在上面创建的姓名列表中，“Cynthia”在位置 0，“Jonathan”在位置 1，“Raymond”在位置 2，“Danny”在位置 3。

使用`append`方法将数据添加到列表中。下面是`append`方法的语法模板:

*list-name . append(value)*

下面的代码片段在上面创建的列表中存储数字 1 到 10:

```
for i in range(1, 11):
  numbers.append(i)
```

与其他语言不同，您可以通过使用列表名称调用`print`函数来打印存储在列表中的值，如下面这行代码所示:

`print(numbers)`

它用我已经写好的代码显示了这个:

`[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`

如果您需要将数据插入到现有列表中，您可以使用 `insert`方法来完成。以下是该方法的语法模板:

*list-name.insert(索引-位置，值)*

以下是演示如何使用`insert`方法的完整程序:

```
numbers = [1,3,5]
print(numbers)
numbers.insert(1,2)
print(numbers)
numbers.insert(3,4)
print(numbers)
```

这个程序的输出是:

```
[1, 3, 5]
[1, 2, 3, 5]
[1, 2, 3, 4, 5]
```

既然我们已经看到了如何向列表中插入数据，那么让我们看看如何从列表中移除单个元素(我将在下一节中介绍如何移除一系列元素)。用于删除数据的一种方法是`pop`。`pop`方法获取一个索引位置，并删除存储在该位置的值。该方法还返回移除的值。

下面是`pop`的语法模板:

*返回值列表名称. pop(索引位置)*

下面是一个示例程序:

```
numbers = [1,2,3,4,5]
print(numbers)
removed = numbers.pop(1)
print("The element removed:",removed)
print(numbers)
```

这个程序的输出是:

```
[1, 2, 3, 4, 5]
The element removed: 2
[1, 3, 4, 5]
```

从列表中删除数据的另一种方法是使用`remove`方法。如果在列表中找到某个值，此方法将从列表中移除该值的第一个匹配项。

下面是`remove`的语法模板:

*list-name . remove(value)*

以下是使用此方法从列表中删除值的示例:

```
dmpNames = ["Cynthia", "Brian", "Jonathan"]
print(dmpNames)
dmpNames.remove("Brian")
print(dmpNames)
```

这个程序的输出是:

```
["Cynthia", "Jonathan"]
```

这些是使用列表进行最基本的工作时需要的主要列表方法。现在让我们来看看其他处理列表的方法。

# 其他 Python 列表方法

有时，您会希望将一个列表中的元素添加到另一个列表中。有一个处理这个任务的列表方法——`extend`。extend 方法的语法模板是:

*list 0-name . extend(list 1-name)*

这意味着通过将 *list1* 的元素添加到 *list0* 的末尾来扩展被标识为 *list0* 的列表。

这里有一个程序演示了`extend`方法是如何工作的:

```
itNames = ['Raymond', 'Mike', 'Mayo', 'Danny']
dmpNames = ['Cynthia', 'Jonathan']
itNames.extend(dmpNames)
print(itNames)
```

这个程序的输出是:

`[‘Raymond’, ‘Mike’, ‘Mayo’, ‘Danny’, ‘Cynthia’, ‘Jonathan’]`

来自`dmpNames`的名称被添加到`itNames`的末尾。

有时你想颠倒列表元素的顺序。您可以使用`reverse`方法来实现这一点，该方法就地反转列表的顺序。以下是该方法的语法模板:

*list-name.reverse()*

下面是一个使用`reverse`方法的例子:

```
numbers = [1,2,3,4,5,6,7,8,9,10]
print(numbers)
numbers.reverse()
print(numbers)
```

以下是该程序的输出:

```
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```

我想介绍的最后一个方法是 `sort`。默认情况下，`sort`方法将对列表进行升序排序。以下是该方法的语法模板:

*list-name . sort([reverse = True]，key=func])*

`sort`函数参数在括号中，因为它们是可选的。我将在下面提供两个可选参数的例子。不过，首先让我们看看 sort 方法没有参数的默认情况。

下面的程序创建一个由十个随机生成的数字组成的列表，显示这些数字，对列表进行排序，最后显示排序后的列表。程序如下:

```
from random import seed
from random import randintseed(1)
numbers = []
for i in range(1, 11):
  numbers.append(randint(1, 100))
print(numbers)
numbers.sort()
print(numbers)
```

以下是该程序的输出:

```
[18, 73, 98, 9, 33, 16, 64, 98, 58, 61]
[9, 16, 18, 33, 58, 61, 64, 73, 98, 98]
```

我们可以通过向函数提供参数`reverse`来对列表进行逆序排序，如下例所示:

```
from random import seed
from random import randintseed(1)
numbers = []
for i in range(1, 11):
  numbers.append(randint(1, 100))
print(numbers)
numbers.sort(reverse=True)
print(numbers)
```

这个程序的输出是:

```
[18, 73, 98, 9, 33, 16, 64, 98, 58, 61]
[98, 98, 73, 64, 61, 58, 33, 18, 16, 9]
```

如果我们有一个字符串列表，想按长度排序，怎么办？为此，我们需要编写一个函数，为 sort 方法提供一个字符串长度，该方法将在遍历列表时应用这个函数。

这里有一个程序可以做到这一点:

```
def strLen(str):
  return len(str)names = ["Meredith", "Mason", "Allison", "Terri"]
print(names)
names.sort(key=strLen)
print(names)
```

这个程序的输出是:

```
['Meredith', 'Mason', 'Allison', 'Terri']
['Mason', 'Terri', 'Allison', 'Meredith']
```

该函数没有使用`<`操作符，而是根据`strLen`函数的输出对字符串进行排序。因此，字符串从最短到最长排序。

您可以使用`clear`方法移除列表中的所有元素。以下是该方法的语法模板:

*list-name.clear()*

下面是一个使用`clear`方法移除列表中所有元素的例子:

```
numbers = [1,2,3,4,5]
print(numbers)
numbers.clear()
print(numbers)
```

以下是该程序的输出:

```
[1, 2, 3, 4, 5]
[]
```

这包含了您可以在 Python 中使用的一组列表方法。现在让我们来看一组使用列表作为参数的函数。

# 使用列表的函数

我要介绍的第一个功能是`len`。这个函数获取一个列表并返回列表中元素的数量。该函数的一个用途是在遍历列表元素时，用它来查找 range 函数的上限参数。

下面是`len`函数的语法模板:

*数值返回值 len(列表名)*

下面的程序演示了如何使用`len`函数来遍历列表中的元素:

```
numbers = [1,2,3,4,5]
for i in range(0, len(numbers)):
  print(numbers[i], end = " ")
```

这个程序的输出是:

`1 2 3 4 5`

我要介绍的下一个函数是`sum`。如果你想把一个数字列表中的所有元素加在一起得到元素的和，你应该使用这个方法。`sum`函数的语法模板是:

*返回值总和(列表)*

该函数将一个列表作为参数，并返回所有列表元素相加的总和。下面是一个使用`sum`函数以及程序输出的例子:

```
numbers = [1,2,3,4,5]
total = sum(numbers)
print("The total is",total)The total is 15
```

Python 有两个函数可用于查找列表中的最小值和最大值。这些功能是`min`和`max`。以下是这些函数的语法模板:

*最小值 min(列表)
最大值 max(列表)*

下面是一个演示这些函数如何工作的程序:

```
from random import seed
from random import randintseed(1)
numbers = []
for i in range(1,21):
  numbers.append(randint(1,100))
print(numbers)
minVal = min(numbers)
maxVal = max(numbers)
print("Mininum value:",minVal)
print("Maximum value:",maxVal)
```

这个程序运行一次的输出是:

```
[18, 73, 98, 9, 33, 16, 64, 98, 58, 61, 84, 49, 27, 13, 63, 4, 50, 56, 78, 98]
Mininum value: 4
Maximum value: 98
```

如果您想从列表中删除某个范围的值，您可以使用`del`函数。此函数将列表和范围作为参数，并删除范围指定的元素。下面是`del`的语法模板:

*删除列表名称【范围开始:范围结束】*

如果您不熟悉 Python 范围，范围会在指定为范围结尾的数字前一位结束。例如，如果列出的范围是 1:3，则引用的最后一个元素将是索引位置 2 处的元素。

下面是一个使用`del` 函数从列表中删除一系列元素的例子:

```
people = ["Cynthia", "Jonathan", "Raymond", "Mayo", "Danny"]
print(people)
del people[2:4]
print(people)
```

> range 参数指定要删除的元素位于位置 2 和 3。以下是该程序的输出:

```
['Cynthia', 'Jonathan', 'Raymond', 'Mayo', 'Danny']
['Cynthia', 'Jonathan', 'Danny']
```

# in 和 not in 关键字

我将通过讨论如何使用`in`和`not in`来结束这次 Python 列表之旅。`in`关键字查看一个值是否在列表中，如果在列表中，则返回`True`，如果没有找到，则返回`False`。以下是 in 的语法模板:

*列表中的值*

您通常会在一个`if`语句中使用`in`关键字。下面是一个演示这种用法的程序:

```
from random import seed
from random import randintnumbers = []
for i in range(1, 21):
  numbers.append(randint(1,100))
print(numbers)
if 50 in numbers:
  print("50 is in numbers.")
else:
print("50 is not in numbers.")
```

该程序两次运行的输出是:

```
C:\python>test.py
[43, 64, 12, 41, 32, 73, 54, 49, 34, 42, 51, 69, 68, 7, 73, 59, 33, 58, 54, 8]
50 is in numbers.C:\python>test.py
[98, 65, 25, 56, 39, 22, 41, 68, 89, 53, 14, 43, 16, 80, 85, 92, 66, 56, 5, 36]
50 is not in numbers.
```

`not in`的语法模板是:

*值不在列表中*

下面是一个使用 `not in`的程序，以及该程序两次运行的输出:

```
from random import seed
from random import randintnumbers = []
for i in range(1, 21):
  numbers.append(randint(1,100))
print(numbers)
if 50 not in numbers:
  print("50 is not in numbers.")
else:
print("50 is in numbers.")C:\python>test.py
[68, 40, 21, 87, 93, 89, 74, 7, 1, 70, 35, 86, 52, 5, 40, 57, 60, 54, 17, 76]
50 is not in numbers.C:\python>test.py
[63, 50, 30, 24, 27, 35, 86, 50, 52, 16, 27, 37, 58, 19, 1, 42, 3, 11, 12, 54]
50 is in numbers.
```

这就结束了我对列表容器及其包含的方法和使用列表作为参数的函数的介绍。在我的下一篇文章中，我将讨论下一个基本的 Python 容器——字典。字典存储关联数据，例如单词和定义或姓名和电话号码。

感谢您的阅读，请将您的意见和建议发邮件至 mmmcmillan1@att.net[给我。](mailto:mmmcmillan1@att.net)