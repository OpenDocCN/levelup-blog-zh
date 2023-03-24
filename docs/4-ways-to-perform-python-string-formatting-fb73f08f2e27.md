# 执行 Python 字符串格式化的 4 种方法

> 原文：<https://levelup.gitconnected.com/4-ways-to-perform-python-string-formatting-fb73f08f2e27>

![](img/13f95d64bf7a176cc389993ad562b129.png)

[Artturi Jalli](https://unsplash.com/@artturijalli?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/python?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

字符串格式化可能是非常基础和简单的，但是真正触发我的是在编写特定情况下的代码时进行格式化的最佳方式。好奇心让我掉进了兔子洞。

顺便说一下，我实际上以前写过其他 Python 相关的文章，比如 [Python 数字格式二进制类型](/python-number-formatting-binary-type-3cc722dd95d2)，这可能需要我再写一篇关于 Python 3 的文章。如果你也感兴趣的话，我写这篇关于用二进制写消息的文章只是为了好玩。除此之外，我主要是用 Java 写的，也有一些是用 JavaScript 写的，请随意查看！

一般来说，字符串格式化是一个评估字符串文字的过程，字符串文字包含最终将被相应值替换的占位符。这意味着你可能会有这样的情况:

```
“I am [your_name] and I am [age] years old.”
```

因此，在上面的字符串示例中，您可以看到`your_name`和`age`是占位符，在这些占位符中，它们可以被您希望的任何输入或值所替换。对于上述情况，可以用你的名字代替名字，用你的年龄号代替年龄。

Python 基本上允许你写一个这样的字符串，并且在运行它的时候替换它的值。这真的可以帮助您根据这个人是谁来动态地更新这些值，而不是固定姓名和年龄。

我以前在 Java 上写过[字符串格式化，所以让我们看看 Python 是怎么做的。我还将尝试更多地关注 Python 3，而不是 Python 2。](https://betterprogramming.pub/ways-to-java-string-formatting-d0aecc391cc9)

也就是说，在 Python 中大约有 4 种方法来进行字符串格式化。以下是清单:

1.  %-格式
2.  str.format()
3.  f 弦
4.  字符串。模板

如果你想知道如何大声说出来，我可以这样称呼它们:

1.  百分比格式
2.  点格式
3.  f 弦
4.  字符串模板

是的，到目前为止它对我很有效，但是如果你有更好的建议，请在下面留下你的评论！

# 1.%-格式

我记得我是在 Python 2 还在的时候学到这一点的，那时人们大多还在使用 Python 2。因此，这个%操作符的工作原理是它利用了位置格式。位置格式是一种根据值的顺序或位置替换占位符的格式，有点像。

让我给你看一个例子:

```
name = ‘John’
friend = ‘Sarah’
“I am %s and I am with %s.” % (name, friend)# Output
I am John and I am with Sarah.
```

让我们再看一下上面的例子，但是我把名字和朋友互换了一下:

```
“I am %s and I am with %s.” % (friend, name)# Output
I am Sarah and I am with John.
```

注意当我交换这两个值时，它们是如何交换的？是的，这是因为位置格式。它接受传递给它的第一个值，并分别替换第一个占位符，以此类推。第一对第一，第二对第二，等等。

现在，上面的例子只处理一种类型，字符串值类型或由%s 命名的% operator。其他类型呢？% operator 为不同的类型提供了不同的占位符，比如:

1.  %s —字符串
2.  %d —整数
3.  %f —浮点数
4.  %x / %X —十六进制表示的整数(小写和大写)
5.  %. <no_of_digits>f —指定位数的浮点数。我认为默认情况下有 6 个十进制数字。</no_of_digits>

让我们看另一个例子:

```
name = ‘John’
age = 23
"I am %s and I am %d years old." % (name, age)# Output
I am John and I am 23 years old.
```

请注意这些值是如何根据占位符类型成功替换的。因此，如果您交换姓名和年龄，同时保留占位符的位置如上，它将抛出一个错误，因为`%s`是用于字符串类型的，但您提供的是整数。记住位置格式化是怎么回事，所以第一对第一匹配，第二对第二匹配等等。

如果您交换了`%s`和`%d`，而保持姓名和年龄不变，它仍然会因为类型不匹配而抛出一个错误。

更多示例供您理解:

```
name = ‘John’
age = 23
friend = ‘Sarah’
money = 68.88
print(‘I am %s and I am %d years old.’ % (name, age))
print(‘I am %s and I am with %s.’ % (name, friend))
print(‘I am %s and I am with %s.’ % (friend, name))
print(‘You are %s and your current balance is %f.’ % (name, money))
print(‘You are %s and your current balance is %.3f.’ % (name, money))# Output
I am John and I am 23 years old.
I am John and I am with Sarah.
I am Sarah and I am with John.
You are John and your current balance is 68.880000.
You are John and your current balance is 68.880.
```

另一个例子也很有趣，因为它可以有一个命名的占位符，但它仍然使用位置格式。让我们来看看:

```
"I am %(my_name)s and my friend is %(friend_name)s." % {'my_name':name, 'friend_name':friend}
```

它仍然会产生与没有命名的结果相同的结果。然而，我很少使用它，也没有经常看到它被使用，因为你仍然不能交换它。所以在我看来，这是不灵活的。这就是为什么我很少或者根本不使用它的主要原因。

# 2.Str.format()

不过这个，当事情简单不复杂的时候，我会相对频繁地使用它。另外，人们在阅读代码时可以理解得更快。我是说，上面写着“格式”。

这个的基本语法是这样的:

```
[your_string].format()
```

这里有两种格式化方式。第一个是位置格式，占位符相应地被替换。第二个是我所说的基于占位符名称的格式。后一个简单的意思是，即使你的值的位置没有按顺序排列，你仍然可以正确地替换它们，如果这些值被相应地引用到占位符的命名的话。

让我们看看位置格式是如何工作的:

```
name = ‘John’
age = 24
print('I am {} and {}.'.format(name, age))
print('I am {} and {}.'.format(age, name))# Output
I am John and 24.
I am 24 and John.
```

现在让我们通过引用名称来看看格式:

```
print('I am {your_name} and {your_age}.'.format(your_name=name, your_age=age))
print('I am {your_name} and {your_age}.'.format(your_age=age, your_name=name))# Output
I am John and 24.
I am John and 24.
```

非常简单明了，对吧？此外，第二种方式真的给了你灵活性，你真的不需要关心订单，我个人更喜欢这种方式。

其他带有一点描述的例子:

```
# positional arguments
print("Hello {}, your current balance is {}.".format("John", 288.1234))
# index arguments
print("Hello {0}, your current balance is {1}.".format("John", 288.1234))
print("Hello {1}, your current balance is {0}.".format("John", 288.1234))
# keyword arguments
print("Hello {name}, your current balance is {blc}.".format(name="John", blc=288.1234))
# mixed arguments
print("Hello {0}, your current balance is {blc}.".format("John", blc=288.1234))# Output
Hello John, your current balance is 288.1234.
Hello John, your current balance is 288.1234.
Hello 288.1234, your current balance is John.
Hello John, your current balance is 288.1234.
Hello John, your current balance is 288.1234.
```

尽管有这么多好处，人们可能还是不想使用`str.format()`。人们可能不喜欢它的冗长。例如，文本值在此处重复出现:

```
value = 5 * 10
'The value is {value}.'.format(value=value)# Output
'The value is 50.'
```

即使是最简单的形式，也有一些样板文件，插入占位符中的值有时会远离占位符所在的位置:

```
'The value is {}.'.format(value)
'The value is 80.'
```

老实说，我不觉得上述问题困扰我，但谁知道呢，这取决于你。

# 3.f 弦

这种类型之所以称为 so，实际上是因为你写了一个前面带 f 的字符串，后面是单引号或三引号，字符串值夹在中间。它是在运行时计算的表达式。

下面是语法:

```
f’I am {name} and {age} years old.’
```

因此，这种格式允许您将 Python 表达式嵌入字符串本身。使用上面的语法/示例，我们可以看到我将变量直接嵌入到字符串中。运行它，你会得到这个没有错误:

```
# Output
I am John and 24 years old.
```

这种格式化的方式真的很神奇，因为它非常灵活。你不仅可以用它来格式化整个模板，还可以直接在里面做算术。就像:

```
a = 5
b = 10
print(f‘Basic arithmetic gets you {a * b} and {2 * b + a}.’)# Output
Basic arithmetic gets you 50 and 25.
```

那么，如何格式化整个模板呢？这个和带三引号的 f 弦有关。通过使用三重引号而不是单引号，它实际上可以准确地显示您编写的字符串。下面来看看我的意思:

```
name = 'Mark'
friend = 'John'
a = 5
b = 10
output = f'''
My friend is {friend}
and I am {name}
and we are doing simple math
which yields {a * b}.
'''
print(output)# Output
My friend is John
and I am Mark
and we are doing simple math
which yields 50.
```

如您所见，它甚至跟随空白，而不必使用`\n`等。当您需要进行复杂的格式化时，这个选项非常有用。对于简单的，你可以用单引号就够了。

反斜杠不能出现在 f 字符串的表达式部分，因为它可能会产生错误。这意味着您不能使用它们，例如，转义引号:

```
$ f'{\'quoted string\'}'File "<stdin>", line 1
SyntaxError: f-string expression part cannot include a backslash
```

直到最近，我实际上经常使用这个。我认为`str.format()`也不错，我只是交替使用。

另外，据说 string 比另外两种最常用的字符串格式化机制:`%-formatting`和`str.format()`更快。

# 4.字符串模板

Python 中格式化字符串的另一个选项。据说它的语法和功能稍微简单一些，但这要由您来判断。

就我个人而言，在了解了其他三种字符串格式化方式之后，我并不经常使用这种方法，但是我可以理解为什么它会因为语法和环境而变得更简单。让我们来看看。

为了使用它，您需要将模板导入。让我们看看语法:

```
from string import Template
Template(‘Your string here: $ans’).substitute(ans=’yes’)
```

所以，在看了语法之后，它可能会被比作`str.format()`,因为它和它有一点相似之处。

除了使用模板和结构之外，第二个显而易见的是占位符。它没有使用花括号{}，而是使用了美元符号$。因此，如果我们看看占位符的简单性，它可能类似于%格式中使用的%。

然而，如果你想用花括号，那也是可以的。只是如果你像这样使用它，它和没有花括号的没有什么不同:

```
‘You are ${adj}’
```

这与以下内容相同:

```
‘You are $adj’
```

那么我们什么时候使用花括号呢？当后面还有信的时候。

```
‘You are ${adj}.’
‘When you want to do ${noun}fication.’
```

原谅随机的句子，但是，那是当你包括括号的时候。这是为了识别将被替换的单词或关键字。否则，它可能会被错误地解释为是占位符而不是名词而不是名词。对于*adj*，它可能仍然有效，因为只考虑有效字符。然而，为了安全起见，最好只使用花括号。如果没有花括号，可能会导致错误。

下面是一些例子:

```
Template('$obj is $colour').substitute(obj='Car',colour='black')
Template('$obj. is $colour').substitute(obj=’It’,colour='blue')
Template('${noun}ification').substitute(noun='Ident')# Output
Car is black
It. is blue
Identification
```

最酷的是，你可以用 dictionary 来代替，而不是像上面那样一个一个地定义键值。让我们来看一个例子:

```
dict_var = dict(name=’Mark’, friend=’John’)
Template(‘The $friend has $name as a friend.’).substitute(dict_var)# Output
That John has Mark as a friend.
```

因此，为了使用 dictionary，请确保关键字与占位符相同。Python 会处理剩下的事情。

另一件你可能需要注意的事情是转义的使用。如果你想用美元符号会发生什么？它基本上就像任何其他格式化方式一样，只是通过对折来转义字符。我的意思是:

```
Template(‘$name owes you $$$amount.’).substitute(name=’John’, amount=100)# Output
John owes you $100.
```

请注意，我是如何使用两个$打印出一个$的，而第三个$只是表明它是一个占位符关键字，将被我的值替换。

关于这方面的更多参考，您可以访问模板字符串[文档](https://docs.python.org/3/library/string.html#template-strings)。

# 总结

在本文中，我们看到了 Python 中不同的格式化方式。我们还看到了可用于格式化字符串的各种语法，以及可用于不同数据类型的转换类型。希望你能更好地了解他们。

今天的学习到此为止。如果你发现了我在这篇文章中遗漏的东西，请在下面的评论区分享。我们都喜欢一起学习。

感谢阅读。