# 揭开 Python 字典的神秘面纱

> 原文：<https://levelup.gitconnected.com/demystifying-python-dictionaries-4c162cace271>

新编码员指南

![](img/67fcc0eeb77cc07b38635ebb062a0d78.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)拍摄

如果你知道如何使用常规字典，你就知道如何使用 Python 字典。(嗯，算是吧。)

在普通词典中，你查一个词来找到它的定义。在 Python 字典中，你通过查找一个“键”(一个单词)来找到它相关的“值”(它的定义)。我们统称这些为“键值对”。

# 那么什么是 Python 字典呢？

Python 字典本质上是一个数据集合(即键值对)，组织在一个[关联数组](https://brilliant.org/wiki/associative-arrays/#:~:text=Associative%20arrays%2C%20also%20called%20maps,the%20number%20is%20the%20key.)中。

字典可以存储各种信息——从数字和字符串到列表和集合(甚至其他字典)。这一点，加上它们可以包含的数据量，使得字典非常适合处理混合数据类型。

## 字典的基本格式如下:

```
**dictionary_name = {key1** : [value1, value2], **key2 :** [value3, value4], **key3 :** [value5, value6]}
```

**例如:**假设我们有一个教授和他们教授的课程的列表，我们想使用这些数据创建一个字典。

以下是将被收入我们词典的信息:

```
***** PROFESSOR COURSE ASSIGNMENTS *******Professor Smith** teaches **ENGS 002** and **ENGS 201****Professor Rasgotra** teaches **SPAN 101** and **SPAN 205****Professor Zhang** teaches **GEOG 002** and **ENVS 281****Professor Martinez** teaches **MATH 051**, **MATH 052**, and **STAT 110**
```

下面是我们完成这本词典的步骤:

1.  创建词典
2.  向字典添加数据/在字典中编辑数据

# 1.让我们创建一个字典

## 要创建空字典:

a)使用空括号{}、**或** b)使用 [dict()方法](https://www.w3schools.com/python/ref_func_dict.asp):

```
**#a) Creating an empty dictionary using brackets:**my_dictionary = {} **#b) Creating an empty dictionary using the dict() method:**my_dictionary = dict()
```

这两条语句都将创建一个名为“my_dictionary”的空字典。

## 要创建字典并同时向其中添加数据，请执行以下操作:

a)使用括号{}包含数据，**或** b)包含数据的 dict()方法:

```
**#a) Creating a dictionary with brackets {data included}**my_dictionary = {key1 : value1, key2 : value2, key3 : value3} **#b) Creating a dictionary using the dict() method (data included)**my_dictionary = dict(key1 = value1, key2 = value2, key3 = value3)
```

这两条语句将创建这个字典:

```
my_dictionary = {key1 : value1,
                 key2 : value2,
                 key3 : value3}
```

因为我们已经知道了一些想要包含在字典中的信息，所以让我们用括号创建一个包含这些数据的字典。

```
professors = {'Smith' : ['ENGS 002', 'ENGS 201'],
              'Rasgotra' : ['SPAN 101', 'SPAN 205'],
              'Zhang' : ['GEOL 002', 'ENVS 281'],
              'Martinez' : ['MATH 051', 'MATH 052', 'STAT 110']}
```

# 2.好了，我们已经创建了字典，现在让我们来编辑它

假设我们想要更新字典以反映以下变化:

*   一位新教授，哈里斯教授，刚刚开始教 HLTH 101，HLTH 102 和 HLTH 110。
*   拉斯哥特拉教授现在正在教授第三门课程:SPAN 001。

## 添加新的键值对

哈里斯教授这学期授课，我们把她加入字典吧。这意味着我们要添加新的关键字“哈里斯”及其关联值[“HLTH 101”、“HLTH 102”、“HLTH 110”]。

向 Python 字典添加新值对的一般格式为:

```
dictionary_name[key] = [value1, value2, value3]
```

键是**不可变的**，这意味着一旦创建，它们就不能被更改。虽然键可以是任何[不可变的数据类型](https://www.geeksforgeeks.org/mutable-vs-immutable-objects-in-python/)(例如元组、字符串等)，但是**值** **是可变的**，并且可以是任何数据类型。

下面是我们如何将新数据添加到字典中:

```
professors['Harris'] = 'HLTH 101', 'HLTH 102', 'HLTH 110'
```

## 编辑键值对

拉斯哥特拉教授正在教授一门额外的课程。让我们把这门课添加到我们的字典中已经与她的名字相关联的课程列表中。

好消息是——我们已经知道如何做到这一点。**更改键值对中的值的一般格式与添加新的键值对相同。**

下面是我们如何更新 Rasgotra 教授的键值对:

```
professors['Rasgotra'] = ['SPAN 101', 'SPAN 205', 'SPAN 001']
```

# 现在让我们学习一些字典方法

字典方法是 Python 内置的工具，允许我们操作字典。这里有几个例子。

## **1。get()方法**

这个方法**返回一个键**的值。一般格式如下:

```
variable_name = *dictionary_name.get(key, alternate_message)*
```

本示例包括一条替代消息，如果找不到密钥，将显示该消息。这使得输出看起来更干净。包含备用消息是可选的，但这是处理错误的好方法。

## 2.pop()方法

这个方法**返回一个值，并从字典中删除它**(及其相关的键)。一般格式如下所示:

```
variable_name = *dictionary_name.pop(key)*
```

## 3.items()方法

这个方法**返回一个字典**的所有键和值。与前面的方法不同，在编写这个语句时，不需要分配变量名。格式如下所示:

```
variable_name = dictionary_name.items()
```

**注意:**这是一个极其简略的字典方法列表。关于更全面的方法列表以及如何使用它们，请查看 w3schools 教程。

# 用字典方法练习

## 1.让我们使用 get()方法来查看 Rasgotra 教授这学期在教的课程。

我们将使用关键字“Rasgotra”来查找它的相关值(她的类)。

```
classes = dictionary.get("Rasgotra")
```

该语句将为我们提供以下输出:

```
["SPAN 101", "SPAN 205", "SPAN 101"]
```

## 2.史密斯教授这学期终究不教书了。让我们使用 pop()方法查看 Smith 教授和她的课程，并将其从我们的字典中删除。

我们将使用 key ("Smith ")返回并删除它的键值对。

```
smith_info = professors.pop("Smith")
```

这将产生以下输出:

```
['ENGS 002', 'ENGS 201']
```

现在，字典“professors”不再包含“Smith”键值对:

```
**professors =** {'Rasgotra' : ['SPAN 101', 'SPAN 205', 'SPAN 001'],
              'Zhang' : ['GEOL 002', 'ENVS 281'],
              'Martinez' : ['MATH 051', 'MATH 052', 'STAT 110'],
              'Harris' : ['HLTH 101', 'HLTH 102', 'HLTH 110']}
```

## 3.招生部门想要一份教授及其所教课程的完整名单。让我们使用 items()方法来做到这一点。

```
variable_name = dictionary_name.items()
```

(记住，我们不需要给这个方法传递一个键。)下面是输出的样子:

```
dict_items([('Rasgotra' : ['SPAN 101', 'SPAN 205', 'SPAN 001']),
('Zhang', ['GEOG 002', 'ENVS 281']), ('Martinez', ['MATH 051', 'MATH 052', 'STAT 110']), ('Harris', ['HLTH 101', 'HLTH 102', 'HLTH 110'])])
```

简而言之:Python 字典很有趣，通常很直观，对于数据管理非常有用。如果你想学习更多关于字典的知识，这里有一些很好的资源让你继续学习:

*   一篇精彩、深入的文章:[关于字典你需要知道的一切，作者达里奥·拉德契奇](https://towardsdatascience.com/python-dictionaries-everything-you-need-to-know-9c2159e5ea8a)
*   在 [w3schools 网站上的一个很棒的教程](https://www.w3schools.com/python/python_dictionaries.asp)
*   或者看看这个伟大的代码库[备忘单](https://www.codecademy.com/learn/learn-python-3/modules/learn-python3-dictionaries/cheatsheet)