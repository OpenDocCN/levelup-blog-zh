# Python 重构技巧以 python 方式编写代码

> 原文：<https://levelup.gitconnected.com/python-refactoring-tips-to-write-code-in-pythonic-way-6b8ff4375b3a>

## 更简洁代码的 Python 重构技巧

![](img/63958cd7fb120dcc0fd2fdb13a953dbc.png)

乔尔·莫特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

代码重构是代码中最有趣也是最重要的部分之一。重构代码不仅使代码更具可读性，还减少了不必要的行数。

无论是软件开发还是一些编码竞赛，到处都需要代码重构。你有责任让你的代码更具可读性，这样其他人就可以容易地阅读它，并且理解你实现了什么逻辑来解决任何特定的问题。

在本文中，我们将介绍一些代码重构技巧，这些技巧将帮助您以更 Python 化的方式编写 Python 代码，并使其可读。我们开始吧！

# 1.从循环中提取语句

很多时候，我们在一个循环中定义了一个变量，而这个变量的值永远不会改变。下面是同样的例子:

```
for address in addresses:
    city = "New York"
    location.append(address.street_address, city)
```

在上面的例子中，没有必要在循环中声明变量，因为变量的值永远不会改变。

相反，我们可以在循环之外声明变量。这样，我们只需要创建一次变量。

```
city = "New York"
for address in addresses:
    location.append(address.street_address, city)
```

这样，我们不需要在每次循环迭代时都创建变量。

# 2.合并嵌套 If 语句

我们使用嵌套 If 来检查多个条件。下面是一个嵌套 If 语句的例子。

```
if a > 5:
    if b < 10:
        print("Checking conditions")
```

我们可以重构这段代码，在一行中检查多个条件，而不是在多行中检查多个条件。下面是一个例子:

```
if a > 5 and b < 10:
     print("Checking conditions")
```

这种检查多个`if`条件的方式比上面的例子更具可读性。

# 3.简化序列检查

很多时候，我们需要检查集合中是否有条目，比如列表、元组和字典，然后检查集合的长度是否大于零。

下面是一个例子:

```
if len(list_of_fruits) > 0:
   fruit_to_eat = choose_fruit(list_of_fruits)
```

相反，我们可以写:

```
if list_of_fruits:
    fruit_to_eat = choose_fruit(list_of_fruits)
```

这种重构的方式比前一种方式更具 Pythonic 式。这也被称为[真值测试](https://docs.python.org/3/library/stdtypes.html)。

# 4.使用 Any 而不是循环

为了检查列表中是否有负数，我们使用循环遍历列表，并检查当前数字是否为负数。一旦满足负数的条件，我们就打破循环。

下面是检查负数的代码:

```
nums = [1,5,7,9,-3, 15]
flag = Falsefor num in nums:
    if num < 0:
        flag = True
        break
```

相反，我们可以对这段代码使用`Any()`函数。下面是一个例子:

```
nums = [1,5,7,9,-3,15]
flag = any(num<0 for num in nums) 
```

如果给定 iterable 中的任何一项为真，这个`Any()`函数返回真，否则返回假。

# 5.删除只使用一次的内联变量

这种情况会发生很多次，我们在函数内部声明一个变量，然后在下一行返回它。这里有一个简单的例子:

```
def sum(a, b):
    total = a + b
    return total
```

在上面的例子中，声明额外的变量是多余的，如果我们在下一行返回它。

以下是同一个示例的重构代码:

```
def sum(a, b):
    return a+b 
```

这种方式更简洁，我们不会在冗余变量上浪费内存。

# 6.用 If 表达式替换 If 语句

我们不用使用`if`和`else`语句来设置变量的值，而是使用 if 表达式在一行中设置它。

下面是一个正常做法的例子:

```
if condition:
    flag = True
else:
    flag = False
```

以下是同一个示例的重构代码:

```
flag = True if condition else False
```

以上两个例子都在执行相同的任务，你可以选择哪一个。

# 7.添加保护条款

当使用`if`和`else`条件时，深度嵌套的函数有时会难以理解。在许多函数中，我们看到许多嵌套的`if`条件，然后在最后有一个`else`条件。

下面是这种情况的一个例子:

```
def do_i_need_hat(self, hat):
    if isInstance(hat, Hat):
        weather_outside = getWeatherReport()
        if weather_outside.is_raining:
            print("Yes")
            return True
        else:
            return False
    else:
        return False
```

可以通过以下方式重构该代码:

```
def do_i_need_hat(self, hat):
    if not isInstance(hat, Hat):
        return Falseif isInstance(hat, Hat):
        weather_outside = getWeatherReport()
        if weather_outside.is_raining:
            print("Yes")
            return True
        else:
            return False
```

这里，我们通过反转条件并在开始时返回它，添加了一个保护子句。

# 结论

这就是这篇文章的全部内容。在本文中，我们已经讨论了如何重构我们的 python 代码。重构代码是开发过程中的一个重要部分。代码重构的主要目的是提高代码的可读性和可维护性。

感谢阅读！

你可以在这里免费订阅我的时事通讯: [Pralabh 的时事通讯](https://pralabhsaxena.medium.com/subscribe)