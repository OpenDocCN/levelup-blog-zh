# 学习 Python:字典

> 原文：<https://levelup.gitconnected.com/learning-python-dictionaries-8e80c64864d4>

![](img/f174978a715f94740d70599542b83af7.png)

[皮斯特亨](https://unsplash.com/@pisitheng?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

字典是仅次于列表的第二个主要 Python 容器。在本文中，我将向您介绍字典，并向您展示使用字典解决 Python 计算问题的不同方法。

# 定义的词典

字典是存储*关联数据*的容器。关联数据是由两部分组成的数据——一个*键*和一个*值*。键就像字典中的单词(因此有了容器名)，值就像定义。例如，当我输入 apple 作为关键字时，会显示与苹果相关的值—蔷薇科中一棵树的圆形果实。

另一种看待字典的方式是把它看作一个存储映射的容器。例如，州议会*映射到其所在的*州，例如加州*映射到*萨克拉门托，或者纽约映射到奥尔巴尼。

字典被用来存储很多不同的东西，从单词和定义，到名字和电话号码，甚至变量名和它们的值。另一方面，字典不用于存储本质上是连续的数据，例如雇员姓名列表或考试成绩列表。顺序数据最好存储在列表中。

# 创建一个空字典并用值初始化字典

为了创建新的字典，Python 提供了函数 dict。当你想声明一个新的字典时，这个函数被单独调用。该函数的语法模板是:

*字典名=字典()*

如果我想创建一个姓名和电话号码的字典，我可以写这行代码:

```
phoneNumbers = dict()
```

如果我想创建一个新的字典并用一些映射初始化它，我可以这样做:

```
phoneNumbers = {'Brown': '2000', 'Smith': '2001',
                'Jones': '2002', 'Green': '2003'}
```

# 访问字典值

通过使用数组符号或下标符号提供键来访问字典值。以下是访问字典值的语法模板:

*字典名称【关键字】*

关键字放在紧跟在字典名称后面的括号内。

以下程序设置了以前的电话号码字典，并通过名称显示每个号码:

```
phoneNumbers = {'Brown': '2000', 'Smith': '2001',
                'Jones': '2002', 'Green': '2003'}
print("Brown's number is",phoneNumbers['Brown'])
print("Smith's number is",phoneNumbers['Smith'])
print("Jones' number is",phoneNumbers['Jones'])
print("Green's number is",phoneNumbers['Green'])
```

这个程序的输出是:

```
Brown's number is 2000
Smith's number is 2001
Jones' number is 2002
Green's number is 2003
```

# 字典功能和方法

有几个 Python 函数可以用于字典。第一个是`len`，它返回存储在字典中的键/值对的数量。下面是该功能的一个工作示例:

```
phoneNumbers = {'Brown': '2000', 'Smith': '2001',
                'Jones': '2002', 'Green': '2003'}
print("There are",len(phoneNumbers)," numbers stored in 
      phoneNumbers.")
```

输出是:

```
There are 4 numbers stored in phoneNumbers.:
```

`keys`方法可以用来创建一个字典中所有键的列表，您可以用它来遍历字典以访问它的值。这是如何工作的:

```
phoneNumbers = {'Brown': '2000', 'Smith': '2001',|
                'Jones': '2002', 'Green': '2003'}
keys = phoneNumbers.keys()
for key in keys:
  print(key,": ",phoneNumbers[key])
```

这个程序的输出是:

```
Brown :  2000
Smith :  2001
Jones :  2002
Green :  2003
```

`in`函数可以用来确定是否在字典中找到了一个键。这里有一个例子:

```
phoneNumbers = {'Brown': '2000', 'Smith': '2001',
                'Jones': '2002', 'Green': '2003'}
key = input("Enter a name: ")
if key in phoneNumbers:
  print(key,":",phoneNumbers[key])
else:
  print(key,"does not have a number in this dictionary.")
```

这个程序运行了两次:

```
C:\python>test.py
Enter a name: Smith
Smith : 2001C:\python>test.py
Enter a name: Bonner
Bonner does not have a number in this dictionary.
```

您不能使用`in`函数在字典中查找值，而是可以使用`values`方法创建一个值列表，然后在列表中使用`in`。这里有一个例子:

```
phoneNumbers = {'Brown': '2000', 'Smith': '2001',
                'Jones': '2002', 'Green': '2003'}
vals = phoneNumbers.values()
number = input("Enter a phone number: ")
if number in vals:
  print(number,"is in the dictionary.")
else:
  print(number,"is not in the dictionary.")
```

以下是该程序两次运行的输出:

```
C:\python>test.py
Enter a phone number: 2003
2003 is in the dictionary.C:\python>test.py
Enter a phone number: 2010
2010 is not in the dictionary.
```

`get`方法接受一个键和一个默认值，并返回与该键相关联的值或默认值。下面是如何使用此方法检索存储在字典中的值或告诉用户键不在字典中的默认值的示例:

```
phoneNumbers = {'Brown': '2000', 'Smith': '2001',
                'Jones': '2002', 'Green': '2003'}
for i in range(1,4):
  name = input("Name to search for: ")
number = phoneNumbers.get(name, "Number not in dictionary")
print(name,number)
```

下面是这个程序运行一次的输出:

```
Name to search for: Brown
Brown 2000
Name to search for: Jones
Jones 2002
Name to search for: Doe
Doe Number not in dictionary
```

`pop`方法根据传递给它的键从字典中删除一个键/值对。下面是一个演示`pop`如何工作的程序:

```
phoneNumbers = {'Brown': '2000', 'Smith': '2001',
                'Jones': '2002', 'Green': '2003'}
for key in phoneNumbers.keys():
  print(key,":",phoneNumbers[key])
name = input("Enter name to remove: ")
phoneNumbers.pop(name)
print()
for key in phoneNumbers.keys():
  print(key,":",phoneNumbers[key])
```

这是一个 rBrown : 2000 的输出

```
Brown : 2000
Smith : 2001
Jones : 2002
Green : 2003
Enter name to remove: JonesBrown : 2000
Smith : 2001
Green : 2003
```

一个相关的方法`popitem`从字典中删除最后插入的键/值对。下面是一个示例程序:

```
phoneNumbers = {'Brown': '2000', 'Smith': '2001',
                'Jones': '2002', 'Green': '2003'}
for key in phoneNumbers.
print()
for key in phoneNumbers.keys():
  print(key,":",phoneNumbers[key])
```

以下是该程序的输出:

```
Brown : 2000
Smith : 2001
Jones : 2002
Green : 2003Brown : 2000
Smith : 2001
Jones : 2002
```

`setdefault`方法将返回给定键的值，或者如果键不在字典中，则将键和作为第二个参数提供的值放入字典。方法返回字典中已存储的键值，或者方法返回与新键一起存储的新值。

下面是一个演示`setdefault`如何工作的程序:

```
phoneNumbers = {'Brown': '2000', 'Smith': '2001',
                'Jones': '2002', 'Green': '2003'}
for key in phoneNumbers.keys():
  print(key,":",phoneNumbers[key])
name = input("Enter name: ")
number = input("Enter number: ")
value = phoneNumbers.setdefault(name, number)
for key in phoneNumbers.keys():
  print(key,":",phoneNumbers[key])
```

这个程序运行了两次，第一次输入一个新的键，第二次输入字典中已经存在的键:

```
C:\python>test.py
Brown : 2000
Smith : 2001
Jones : 2002
Green : 2003
Enter name: Doe
Enter number: 2004Brown : 2000
Smith : 2001
Jones : 2002
Green : 2003
Doe : 2004c:\python>test.py
Brown : 2000
Smith : 2001
Jones : 2002
Green : 2003
Enter name: Jones
Enter number: 2002
Brown : 2000
Smith : 2001
Jones : 2002
Green : 2003
```

# 用 Python 做字典反向查找

反向查找是基于一个值查找一个键。为此，您需要遍历字典中的所有键，并查看该键返回您正在搜索的值。下面是一个演示如何进行反向查找的简短程序:

```
phoneNumbers = {'Brown': '2000', 'Smith': '2001',
                'Jones': '2002', 'Green': '2003'}
value = input("Value to search for: ")
for key in phoneNumbers:
  if phoneNumbers[key] == value:
    print(value,"is in phoneNumbers.")
```

以下是该程序的一次运行输出:

```
Value to search for: 2001
2001 is in phoneNumbers.
```

不过，最好将这项任务放入一个函数中。这看起来是这样的:

```
def revLookUp(dict, value):
  for key in dict:
    if dict[key] == value:
      return True
  return FalsephoneNumbers = {'Brown': '2000', 'Smith': '2001',
                'Jones': '2002', 'Green': '2003'}
value = input("Value to search for: ")
if (revLookUp(phoneNumbers, value)):
  print(value,"is in phoneNumbers.")
else:
  print(value,"is not in phoneNumbers.")
```

下面是该程序的两次运行:

```
C:\python>test.py
Value to search for: 2002
2002 is in phoneNumbers.C:\python>test.py
Value to search for: 2010
2010 is not in phoneNumbers.
```

# 一个字典应用程序—计算字符串中字母的出现次数

我将用一个字典应用的例子来结束这篇文章。您可以使用字典来构建直方图，直方图可以存储某个集合中单个项目的计数。我将用 Allen Downey 的教科书 *Think Python* 中的一个例子来演示这一点。

我要构建的直方图统计了单词或句子中字母的出现次数。我将把它实现为一个函数，它构建一个空字典，然后检查每个连续的字母是否在字典中。如果不是，该函数将该字母作为键添加到字典中，并将值指定为 1。如果该字母已经在字典中，则该值增加 1。

下面是函数定义和测试函数的程序:

```
def histo(str):
  letters = dict()
  for letter in str:
    if letter not in letters:
      letters[letter] = 1
    else:
  return lettersword = input("Enter a word: ")
print(word)
letterCount = histo(word)
for letter in letterCount:
  print(letter,letterCount[letter])
```

下面是该程序运行一次的输出:

```
Enter a word: distribution
distribution
d 1
i 3
s 1
t 2
r 1
b 1
u 1
o 1
n 1
```

感谢您的阅读，请发邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)告诉我您的意见和建议。