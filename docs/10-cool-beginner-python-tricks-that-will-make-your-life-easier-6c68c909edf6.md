# 10 个很酷的初学 Python 技巧，让你的生活更轻松

> 原文：<https://levelup.gitconnected.com/10-cool-beginner-python-tricks-that-will-make-your-life-easier-6c68c909edf6>

## 给每个 python 爱好者的简单而有效的提示

![](img/3429270999517254d65f5924d2f13d64.png)

照片由 [**芈莎少女**](https://www.pexels.com/@miphotography?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 发自 [**Pexels**](https://www.pexels.com/photo/pineapple-with-brown-sunglasses-459601/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

Python 的紧凑性使得开发人员在编写一行又一行代码时更加轻松。但是有一些鲜为人知的 Python 技巧，它们惊人的能力会让你大吃一惊。

在今天的文章中，我将讨论 10 个 Python 技巧和窍门，它们将真正有助于初学者编写更紧凑的代码。了解这些技巧和窍门肯定会为你节省一些宝贵的时间。

# 1.海象操作员

`**Walrus**` **或** `**:=**`操作符是 python 3.8 最新增加的操作符之一。它是一个赋值操作符，允许你在一个表达式中给一个变量赋值，比如条件语句、循环等等。

> **示例**

如果我们想检查和打印一个列表的长度:

```
Mylist = [1,2,3]if(l := len(mylist) > 2)print(l)
```

> **输出**

```
3
```

# **2。** **拆分字符串**

如果您想将一个字符串的组成部分拆分成一个列表，您可以使用 python 中的 split()函数轻松完成。这将使字符串操作容易得多！

> **例子**

```
string = “hello world”string.split()
```

> **输出**

```
[‘hello’, ‘world’]
```

# **3。** **反转一根弦**

如果您想要反转一个给定的字符串，您只需要一行代码就可以使用字符串的负索引。

> **例子**

```
str=”hello world!”a=str[::-1]print(a)
```

> **输出**

```
!dlrow olleh
```

# **4。** **合并两本词典**

这个惊人的技巧将帮助你用一行代码合并两个字典。我们只需要在两个字典的名称前面用**就像下面两个一样把它们合并成一个字典:

> **示例**

```
d1 = {“a”: 10, “b”:20}d2 = {“c”: 30, “d”:40}d3 = {**d1, **d2}print(d3)
```

> **输出**

```
{‘a’: 10, ‘b’: 20, ‘c’: 30, ‘d’: 40}
```

> 直接从行业专家那里获得 Python 和数据科学的优质内容——使用我的推荐链接成为中级会员以解锁内容:[https://pranjalai.medium.com/membership](https://pranjalai.medium.com/membership)

# **5。** T **he zip()函数**

python 中的 **zip()** 函数可以让您在处理列表和字典时更加轻松。它用于合并几个相同长度的列表。

> **举例**

```
colour = [“red”, “yellow”, “green”]fruits = [‘apple’, ‘banana’, ‘mango’]for colour, fruits in zip(colour, fruits):print(colour, fruits)
```

> **输出**

```
red appleyellow bananagreen mango
```

zip()函数也可以用于将两个列表合并成一个字典。这种方法在对列表中的数据进行分组时非常有用。

> **例子**

```
students = [“Rajesh”, “kumar”, “Kriti”]marks = [87, 90, 88]dictionary = dict(zip(students, marks))print(dictionary)
```

> **输出**

```
{‘Rajesh’: 87, ‘kumar’: 90, ‘Kriti’: 88}
```

# **6。** **给一个变量分配多个列表值**

如果要将列表中的某些特定值赋给一个变量，而将所有剩余值赋给列表格式中的另一个变量，可以使用以下方法:

> **示例**

```
mylist = [1,2,3,4,5]a,*b = mylistprint(f”a =”,a)print(f”b =”,b)
```

> **输出**

```
a = 1b = [2, 3, 4, 5]
```

这个过程也叫做列表解包，你也可以对 2 个以上的变量应用这个方法！

# **7。** **删除重复列表项**

您的列表中是否有要删除的重复项目？使用 **set()** 函数，只需一行代码就可以做到这一点。

> **示例**

```
mylist = [1,1,1,2,2,3,3,4,4,5,6,7,7,8,9]newlist = set(mylist)print(newlist)
```

> **输出**

```
{1, 2, 3, 4, 5, 6, 7, 8, 9}
```

# 8。**λ函数**

如果你需要一个不是很复杂的函数，使用 **lambda** 可以在一行中轻松完成。它们也被称为匿名函数，在数据科学和 web 开发中大量使用。

> **示例**

假设你想写一个函数来乘两个数。不用编写传统的函数，您可以在一行代码中使用:

```
mul = lambda a,b: a*bmul(5,6)
```

> **输出**

```
30
```

# 9.交换变量值

当我们学习变量的时候，我们学习的第一个程序是交换两个变量的值。在 python 中，您可以通过一行代码实现这一点:

> **例如:**

```
a = 100b = 200a,b = b,aprint(f’a = ‘,a)print(f’b = ‘,b)
```

> **输出:**

```
a = 200b = 100
```

# **10。** **在你的代码中使用密码**

这个 python 技巧对于用密码保护您的代码来说是惊人的。我们将使用来自**库 getpass** 的 **getpass()函数**，它对您的输入进行编码。这将防止任何人在没有密码的情况下运行代码。是不是很酷！

> **示例**

```
from getpass import getpasspassword = getpass(“password: “)if password == “abcd”: print(“welcome strnger!”)else: print(“wrong password”)
```

> **输出**

```
password: **** [abcd]**Welcome stranger!**Password: **** [abdc]Wrong password
```

> 这里有一本[关于 **Python 编程**的书](https://amzn.to/3fxdkJE)，我一定会推荐给所有**初学者**。

# 结论

这些是一些令人惊奇的 Python 技巧和窍门，它们将使您的工作在编码时变得容易得多。还有很多类似的快捷方式，你可以从官方文档或任何其他网站上找到。

***注:*** *本文包含附属链接。这意味着，如果你点击它，并选择购买我上面链接的资源，你的订阅费的一小部分将归我所有。*

*然而，推荐的资源是我亲身经历的，并在我的数据科学职业生涯中对我有所帮助。*

> *在你走之前……*

如果你喜欢这篇文章，并且想继续关注关于 **Python &数据科学**的更多**精彩文章**——请点击这里[https://pranjalai.medium.com/membership](https://pranjalai.medium.com/membership)考虑成为一名中级会员。

请考虑使用[我的推荐链接](https://pranjalai.medium.com/membership)注册。通过这种方式，会员费的一部分归我，这激励我写更多关于 Python 和数据科学的令人兴奋的东西。

还有，可以随时订阅我的免费简讯: [**Pranjal 的简讯**](https://pranjalai.medium.com/subscribe) 。