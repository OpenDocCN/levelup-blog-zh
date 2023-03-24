# 学习 Python:各种语言特性

> 原文：<https://levelup.gitconnected.com/learning-python-assorted-language-features-59882673b50f>

![](img/e28d13ceeea3855142388c5f397efe24.png)

照片由[希特什·乔杜里](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在这篇文章中，也是我学习 Python 的最后一篇文章，我将介绍一些我在以前的文章中没有涉及到的语言特性。

# 条件表达式

编写条件表达式最常用的方法是使用 if-else 语句，如下例所示:

```
evenOdd = ""
if number % 2 == 0:
  evenOdd = "even"
else:
  evenOdd = "odd"
```

这是一个标准的结构，但是有一个更简洁的方法，使用所谓的条件表达式。以下是条件表达式的语法模板:

*语句 0 if(条件)语句 1 else 语句 2*

我们可以用一个条件表达式更简洁地编写上面的例子，如下所示:

```
number = int(input("Enter a number: "))
evenOdd = "even" if number % 2 == 0 else "odd"
print(number,"is",evenOdd)
```

以下是该程序两次运行的输出:

```
C:\python>test.py
Enter a number: 23
23 is odd
C:\python>test.py
Enter a number: 22
22 is even
```

这是艾伦·唐尼的巨著《思考 Python》中的另一个例子。下面是计算一个数的阶乘的递归定义，以及一个测试程序:

```
def factorial(n):
  if n == 0:
    return 1
  else:
    return n * factorial(n-1)print(factorial(3))
print(factorial(5))
```

这个程序的输出是:

```
6
120
```

使用条件表达式可以更简洁地编写`factorial`函数。下面是对`factorial`的重新定义以及相同的测试程序:

```
def factorial(n):
  return 1 if n == 1 else n * factorial(n-1)print(factorial(3))
print(factorial(5))
```

输出是:

```
6
120
```

我将让您决定是否更喜欢使用条件表达式而不是传统的 if-else 语句，但是请记住，简洁并不总是能够提高可读性。

# 列出理解

一个*列表理解*允许你在一个列表中的所有元素上执行一个任务，而不需要使用一个循环。一个例子将有助于解释列表理解是如何工作的。

下面的函数将使用一个`for` 循环对列表中所有单词的字符求和:

```
def sumChars(lyst):
  total = 0
  for word in lyst:
    total += len(word)
  return total
```

下面是程序中使用的函数:

```
words = ["hello","goodbye","walrus","blackbird"]
print(sumChars(words)) # outputs 27
```

以下是使用列表理解改写的该函数:

```
def sum_chars(lyst):
  return sum([len(word) for word in lyst])
```

这个函数像第一个函数一样遍历单词列表，但是在新的定义中，正在构建单词长度列表，如方括号所示。然后，对每个单词长度调用 sum 函数，以获得列表中的字符总数。

列表理解允许更简洁的 Python 代码，但它们也可能更难调试，因为您不能像在第一个函数定义的 for 循环中那样将任何调试语句放在列表理解中。

列表理解可以用来过滤一组元素。例如，如果我有一组测试分数，我想过滤掉所有非 A 分数(那些低于 90 分的分数)，我可以使用 list comprehension 编写以下函数:

```
def onlyA(grades):
  return [grade for grade in grades if grade >= 90]
```

下面是一个测试程序:

```
grades = [81, 92, 77, 94, 68, 100, 79, 90]
print(onlyA(grades))
```

这个程序的输出是:

```
[92, 94, 100, 90]
```

# 生成器表达式

一个*生成器表达式*计算一个可以作为序列一部分的值。例如，您可以使用生成器表达式来计算一系列数字的幂。

当您创建一个生成器表达式时，您并没有创建一个完整的值集，就像您在列表理解中所做的那样。相反，您正在创建一个表达式，该表达式将基于您在表达式中使用的指定计算来生成下一个值。

下面是一个简单的生成器表达式，它计算序列中下一个数字的平方，并附有一个测试它的程序:

```
gen = (num * num for num in range(1,11))
for i in range(1,6):
  print(i,next(gen))
```

这个程序的输出是:

```
1 1
2 4
3 9
4 16
5 25
```

我的下一个例子计算了[斐波那契](https://en.wikipedia.org/wiki/Fibonacci_number)序列中指定数量的项目。该计算要求在计算斐波那契数的函数中使用 yield 语句，而不是 return 语句。[这里的](https://yasoob.me/2013/09/29/the-python-yield-keyword-explained/)是收益表的解释。

现在，您已经了解了 yield 的工作原理，我可以展示我的斐波那契数列生成器代码，以及一个测试程序:

```
def fibo():
  a,b = 0,1
  while True:
    yield a
    a,b = b, a + bfib = fibo() # generatorcount = 0
for f in fib:
  print(f, end=" ")
  count += 1
  if (count > 10):
    break
```

以下是该程序的输出:

```
0 1 1 2 3 5 8 13 21 34 55
```

诚然，这个程序计算指定数量的斐波那契数。我将把它作为一个练习留给读者来修改程序，以便用户决定程序打印多少个 Fibonacci 数。

# 任何功能

`any`函数是一个内置的布尔函数，它测试值容器中的指定条件，如果容器中的任何元素满足该条件，则返回 True。下面是`any`的语法模板:

*布尔返回值 any(条件，元素集)*

这是第一个例子:

```
grades = [81, 92, 78, 91, 100, 65, 88]
flag = any(grade >= 90 for grade in grades)
if flag:
  print("There are A grades in set of grades.")
else:
  print("There are no A grades in set of grades.")
```

any 函数测试以查看等级列表中是否有大于或等于 90 的等级，如果有则返回`True`，否则返回 `False`。

下面是另一个使用字符串而不是列表的示例:

```
def isVowel(letter):
  return letter == 'a' or \
         letter == 'e' or \
         letter == 'i' or \
         letter == 'o' or \
         letter == 'u'word = "hello"
flag = any(isVowel(letter) for letter in word)
if flag:
  print("There is at least one vowel in",word)
else:
  print("There are no vowels in",word)
word = "crwth"
flag = any(isVowel(letter) for letter in word)
if flag:
  print("There is at least one vowel in",word)
else:
  print("There are no vowels in",word)
```

这个程序的输出是:

```
There is at least one vowel in hello
There are no vowels in crwth
```

# all 函数

all 函数与`any`函数相反。如果容器的每个元素都满足指定的条件，则函数返回`True`，否则函数返回`False`。下面是一个使用`all`函数的例子(代码和两次运行的输出):

```
def isVowel(letter):
  return letter == 'a' or \
         letter == 'e' or \
         letter == 'i' or \
         letter == 'o' or \
         letter == 'u'# Eunoia
word = input("Enter a word: ")
flag = all(isVowel(letter) for letter in word)
if (flag):
  print(word,"is all vowels.")
else:
  print(word,"is not all vowels.")C:\python>test.py
Enter a word: euoia
euoia is all vowels.C:\python>test.py
Enter a word: hello
hello is not all vowels.
```

# 另外两个数据结构

Python 中有几个数据结构是我在以前的文章中没有提到的。第一个是*组*。

集合是一个无序的、无类型的值的集合，其中不能有重复的值。集合是使用花括号创建的，如下所示:

```
fruit = {"apple", "banana", "melon"}
```

一旦创建了集合，就不能删除或修改集合中的元素，但是可以使用`add`方法向集合中添加新元素。这里有一个例子:

```
fruit = {"apple", "banana", "melon"}
print(fruit)
fruit.add("kiwi")
print(fruit)
fruit.add("apple")
print(fruit)
```

这个程序的输出是:

```
{'apple', 'banana', 'melon'}
{'apple', 'banana', 'melon', 'kiwi'}
{'apple', 'banana', 'melon', 'kiwi'}
```

`len`功能也适用于器械包:

```
fruit = {"apple", "banana", "melon"}
print(fruit)
fruit.add("kiwi")
print(fruit)
fruit.add("apple")
print(fruit)
print("Number of items in set:",len(fruit))
# displays Number of items in set: 4
```

最后一个例子，您可以使用`update`方法将两个集合放在一起:

```
myFruit = {"apple", "banana"}
yourFruit = {"banana", "kiwi", "melon"}
myFruit.update(yourFruit)
print(myFruit)
```

这个程序的输出是:

```
{'banana', 'apple', 'kiwi', 'melon'}
```

我想介绍的第二个数据结构是计数器。计数器像集合一样存储元素，但是如果一个非唯一的元素被添加到计数器中，计数器会记录该元素出现的次数。在许多方面，计数器就像一个字典，其中的键是元素，值是键出现的次数。

计数器不是内置的数据结构，所以它必须被导入，正如您将在下面的程序中看到的。

下面的程序展示了 Counter 作为单词索引的强大功能:

```
from collections import Counterletter_count = Counter("supercalifragiliciousexpealidocious")
print(letter_count)
```

这个程序的输出是:

```
Counter({'i': 6, 's': 3, 'u': 3, 'e': 3, 'c': 3, 'a': 3, 'l': 3, 'o': 3, 'p': 2, 'r': 2, 'f': 1, 'g': 1, 'x': 1, 'd': 1})
```

这就结束了这篇文章和我的学习 Python 系列文章。感谢您通读本系列，请发电子邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)告诉我您的意见和建议。