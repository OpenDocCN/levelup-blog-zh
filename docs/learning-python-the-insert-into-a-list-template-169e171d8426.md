# 学习 Python:插入列表模板

> 原文：<https://levelup.gitconnected.com/learning-python-the-insert-into-a-list-template-169e171d8426>

![](img/0371bf561fa2c0702030a03e586a8197.png)

由[凯文·卡纳斯](https://unsplash.com/@kvncnls?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

将数据插入数组曾经是编程中最低效的操作之一。这是因为如果数组很大，并且插入的位置靠近数组的开头，则必须将大量数组元素向右移动，以便为插入的数据腾出空间。

现在，有了更现代的数组(Python 中的 list)函数，这个任务就不那么困难了，因为移位是在幕后执行的。在本文中，我将讨论如何使用现有的 Python 方法向 Python 列表中插入新数据。

# 插入到列表模板中

将*插入列表*模板的伪代码非常简单:

*找到你想要插入数据的索引位置
用索引位置和数据调用 insert 方法*

这就是全部了。

# 使用 insert 方法将数据插入列表

`insert`方法有两个参数——索引位置和要插入的数据。以下是 insert 的语法模板:

*list-name.insert(索引，数据)*

现在让我们看一个简单的例子。以下程序将一个数字插入数字列表中的适当位置，以保持数字的排序顺序:

```
numbers = [1,2,4,5]
pos = 0
number = 3
while numbers[pos] < number:
  pos = pos + 1
numbers.insert(pos, number)
print(numbers)
```

现在让我们看一个更复杂的例子。下面的程序创建了一个包含 20 个随机整数的列表，然后对它们进行排序。然后程序提示用户输入一个新值添加到列表中，程序将数字放在正确的位置以保持排序的顺序。程序是这样的:

```
def findBefore(list, value):
  pos = 0
  while list[pos] < value:
    pos = pos + 1
  return posnumbers = []
stop = 20
for i in range(0, stop):
  numbers.append(randint(1,101))
numbers.sort()
print(numbers)
newValue = int(input("Enter a value insert into the list: "))
position = findBefore(numbers, newValue)
numbers.insert(position, newValue)
print(numbers)
```

下面是程序运行一次的输出:

```
[4, 11, 18, 23, 25, 27, 28, 33, 34, 60, 69, 74, 82, 86, 87, 87,
|96, 96, 96, 98]
Enter a value insert into the list: 50
[4, 11, 18, 23, 25, 27, 28, 33, 34, 50, 60, 69, 74, 82, 86, 87,
87, 96, 96, 96, 98]
```

让我们用字符串来试试，以确保代码是灵活的。程序是这样的:

```
def findBefore(list, value):
  pos = 0
  while list[pos] < value:
    pos = pos + 1
  return posgroceries = ["eggs", "bananas", "bread", "milk", "cookies"]
groceries.sort()
print(groceries)
newItem = input("Add food item to list: ")
position = findBefore(groceries, newItem)
groceries.insert(position, newItem)
print(groceries)
```

下面是程序运行一次的输出:

```
['bananas', 'bread', 'cookies', 'eggs', 'milk']
Add food item to list: cream
['bananas', 'bread', 'cookies', 'cream', 'eggs', 'milk']
```

这些例子演示了如何使用 insert 方法在 Python 中实现将*插入到列表*模板中。同样，使用`insert`方法比在其他语言中插入类似列表的结构要容易得多，因为您不必考虑将列表元素向右移动来为插入的数据腾出空间。Python 为您完成了这项任务，这也是为什么 Python 被认为是一种更容易使用但功能强大的计算机编程语言的原因之一。

感谢您的阅读，请发电子邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)告诉我您的意见和建议。