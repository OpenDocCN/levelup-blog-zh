# [如果你想掌握它，请在理解列表中逐项列出]

> 原文：<https://levelup.gitconnected.com/item-for-item-in-list-comprehensions-if-you-want-to-master-it-237e2f7213ee>

Python 中这一有用特性的简单易懂的指南

![](img/a986530e481c05b82741367bf7fc4e84.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Waldemar Brandt](https://unsplash.com/@waldemarbrandt67w?utm_source=medium&utm_medium=referral) 拍照

列表理解是 Python 编程中一个非常实用的特性，它不仅可以使代码更快，而且更容易编写和阅读。在本文中，您将学习 Python 中列表理解的基础知识和一些更高级的实现，并准备好充分利用它。

让我们从回顾列表的基本定义开始。列表是 Python 中一种非常丰富的数据结构。它们被定义为任意对象的有序集合，这些对象是可变的、动态的并且可以通过索引来访问。例如:

```
my_list = [123,”look at this”, [1,2,3], {“list items can be”:”anything”}, True, (1,2,3)]type(my_list)
listtype(my_list[3])
dicttype(my_list[5])
tuple
```

如您所见，列表对象可以是任何类型——整数或浮点、字符串、其他列表、字典、布尔、元组。它们甚至可以包含函数、类和模块，每个对象都可以通过其索引来访问。这不是很方便吗？但是，本文不会关注 Python 中列表的所有优点，而是关注一种创建列表的方法——列表理解。如果你想学习更多关于列表的知识，我推荐这篇非常清晰有用的关于列表的[文章](https://realpython.com/python-lists-tuples/)和官方的 [Python 文档](https://docs.python.org/3/tutorial/datastructures.html?#more-on-lists)。

列表理解提供了一种创建列表的简洁方法，当您通过对来自 [iterable](https://docs.python.org/3/glossary.html#term-iterable) 的每一项应用某种操作和/或根据某种条件过滤元素来创建列表时，它特别有用。它们通常比 for 循环或 map()更 pythonic 化，因为它们需要更少的代码行，并且非常灵活，可以应用于广泛的情况。

## 列表理解结构

如果你理解了列表理解的结构，你就能够通过简单地遵循它的逻辑来将它应用到大多数任务中。下面是列表理解的基本结构:

```
new_list = [ expression **for** item **in** iterable **if** condition ]
```

让我们来分解一下:

*   **表达式**可以仅仅是项本身，一个数学运算，一个字符串方法，一个函数，甚至包括一个条件语句或者是另一个列表的理解，应用于项。
*   **Item** 是 iterable 中的每个单数对象或值。
*   **Iterable** 是能够一次返回一个项目的任何 Python 对象。列表、字符串、集合、元组、字典、生成器等等都是可迭代的对象。
*   **条件**使用 if 语句根据条件过滤列表项。你可以用这个，如果不需要也可以直接跳过。然而，其他三个部分对于理解列表总是必要的。

让我们来看一些例子，这样会更容易掌握这个结构。为了帮助你形象化理解列表的每一部分，我总是用粗体字来区分它们。

![](img/6f9334caf6b05e7148bfb3361cd1ee30.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Héctor J. Rivas](https://unsplash.com/@hjrc33?utm_source=medium&utm_medium=referral) 拍摄

## 从 iterable 创建项目列表

列表理解的一个最简单的用途是当你需要创建一个简单的数字列表时，表达式部分几乎只需要调用 iterable 中的每一项:

```
numbers = [number **for** number **in** range(1,11)][1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

## 创建执行操作的列表

现在让我们看一些例子，其中表达式执行一个操作或方法。假设你有一个数字列表，你想得到它们的等价平方:

```
squares = [n * n **for** n **in** numbers][1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

## 创建一个对多个列表执行操作的列表

你也可以在一个列表理解中使用多个列表中的项目。看这个例子，我想把我们两个不同列表中的每一项加起来:

```
n_plus_square = [n + s **for** n, s **in** zip(numbers, squares)][2, 6, 12, 20, 30, 42, 56, 72, 90, 110]
```

## 创建元组列表

列表理解也可以支持创建元组列表。假设您想要获取数字列表，并使用原始数字及其平方创建元组:

```
list_of_tuples = [(n, n*n) **for** n **in** numbers][(1, 1),  (2, 4),  (3, 9),  (4, 16),  (5, 25),  (6, 36),  (7, 49),  (8, 64),  (9, 81),  (10, 100)]
```

## 创建执行方法的列表

表达式可以使用方法，也可以完成多项任务。假设你有一个电子邮件地址列表，除了最初创建该列表的人忘记添加“@gmail.com”。他们也没有用所有的小写字母标准化列表，所以你想纠正你的列表，在每个项目上执行这两个任务。您可以:

```
emails = ['Maria123','keanureeves','nineties_songs','Smart.coder']correct_emails = [email.lower()+'@gmail.com' **for** email **in** emails]['[maria123@gmail.com](mailto:maria123@gmail.com)','[keanureeves@gmail.com](mailto:keanureeves@gmail.com)',
'[nineties_songs@gmail.com](mailto:nineties_songs@gmail.com)','[smart.coder@gmail.com](mailto:smart.coder@gmail.com)']
```

## 应用函数创建列表

在对旧列表中的每一项应用一个函数之后，您可以使用类似于`list(map())`的东西来获得一个新列表，但是使用 list comprehension 也可以很容易地完成这个任务。列表理解表达式可以包含复杂表达式和嵌套函数。假设你想得到你的正方形列表，并得到一个新的列表，每个数字减去列表的平均值。这就是我们在列表理解表达式中使用函数`eval(), sum() and len()`的方法:

```
new_list = [eval('x - sum(squares)/len(squares)') **for** x **in** squares][-37.5, -34.5, -29.5, -22.5, -13.5, -2.5, 10.5, 25.5, 42.5, 61.5]
```

## 使用 if/else 条件作为表达式创建列表

列表理解的表达式部分也可以包括 if/else 语句。假设我们想要获取新列表并保留正数，但是用负数代替 0:

```
positives = [n if n > 0 else 0 **for** n **in** new_list][0, 0, 0, 0, 0, 0, 10.5, 25.5, 42.5, 61.5]
```

## 使用另一个列表理解作为表达式创建列表

您可以使用另一个列表理解作为您的列表理解表达式。当您想要访问列表中每个项目的元素时，这可能很有用。例如，假设您有一个单词列表，您想要一个包含所有单词的所有字母的新列表。以下是如何做到这一点:

```
list_of_words = ['let', 'the', 'sun', 'shine']letters = [letter **for** word **in** list_of_words **for** letter **in** word]['l','e','t','t','h','e','s','u','n','s','h','i','n','e']
```

## 用嵌套的列出的理解创建一个列表

假设你想要的不是一个包含所有字母的列表，而是一个包含每个单词中所有字母的列表？这可以通过使用另一组括号嵌套列表理解来实现:

```
nested = [[letter **for** letter **in** word] **for** word **in** list_of_words][['l','e','t'], ['t','h','e'], ['s','u','n'], ['s','h','i','n','e']]
```

嵌套列表理解的另一个便利用途是用它来创建矩阵。Python 没有矩阵数据类型，但是我们可以将列表列表视为矩阵:

```
matrix = [[0 **for** item **in** range(3)] **for** row **in** range(4)][[0, 0, 0], [0, 0, 0], [0, 0, 0], [0, 0, 0]]
```

嵌套列表理解对于转置矩阵也是有用的。让我们创建一个包含数字的新矩阵，这样我们可以更清楚地看到这一点，然后使用嵌套列表理解来转置它。

```
X = [[item **for** item **in** range(4)] **for** row **in** range(2)][[0, 1, 2, 3], [0, 1, 2, 3]]
```

现在让我们转置它:

```
X_t = [[X[j][i] **for** j **in** range(len(X))] **for** i **in** range(len(X[0]))][[0, 0], [1, 1], [2, 2], [3, 3]]
```

## 创建带条件的列表

到目前为止，我们还没有将列表理解末尾的**条件**元素应用到实例中。列表理解的最后一个元素可以用来根据条件过滤新列表中的元素。但是请注意，与在列表理解的**表达式**部分中使用条件不同，这里只能使用`if`语句，而不能使用`else`。

回到我们的字母列表，假设你只想要辅音。您可以使用 condition 元素过滤掉元音:

```
consonants = [l **for** l **in** letters **if** l not in 'aeiou']['l','t','t','h','s','n','s','h','n']
```

## **创建多条件列表**

在你的列表理解中可以有不止一个条件。例如，假设您想要的数字是 2 或 3 的倍数，这也可以在`if`语句中实现:

```
numbers = [2,3,5,6,7,8,9,10,12,15,24,28,30]multiples_2_or_3 = [n **for** n **in** numbers **if** n%2==0 or n%3==0][2, 3, 6, 8, 9, 10, 12, 15, 24, 28, 30]
```

## 创建具有嵌套条件的列表

你还会在列表理解中发现嵌套的条件，这意味着列表评估不止一个条件，并且只添加两个条件都满足的条目。 [Python 使用短路布尔求值](https://docs.python.org/2/reference/expressions.html#boolean-operations)，这意味着你可以使用两个`if`语句或`and`在大约相同的计算时间内得到相同的结果:

```
multiples_2_and_3 = [n **for** n **in** numbers **if** n%2==0 **if** n%3==0][6, 12, 24, 30]#ormultiples_2_and_3 = [n **for** n **in** numbers **if** n%2==0 and n%3==0][6, 12, 24, 30]
```

然而，在列表理解中使用多个`if`语句的能力对于用可选的 if 条件链接多个 for-part 是必不可少的。为了看这个例子，让我们回到从单词列表中获得的字母列表。假设现在您不仅希望新列表中只有辅音字母，而且还希望从列表中跳过单词“sun”。这是如何工作的:

```
list_of_words = ['let', 'the', 'sun', 'shine']conditional_letters = [l **for** word **in** list_of_words **if** word != 'sun' **for** l **in** word **if** l not in 'aeiou']['l', 't', 't', 'h', 's', 'h', 'n']
```

瞧啊。到现在为止，你可能已经对列表理解的惊人能力充满信心，并准备好使用它了。好消息是，同样的概念也适用于集合理解和字典理解。

不过，还是有一些注意事项！

您可以为任何列表理解编写一个 for 循环，但是反过来就不正确了——不是每个 for 循环都可以变成列表理解。此外，如果你的列表理解太长太复杂，你的可读性会大打折扣，在这种情况下，列表理解很可能不是你任务的最佳选择。

希望这篇文章对你有用。编码快乐！