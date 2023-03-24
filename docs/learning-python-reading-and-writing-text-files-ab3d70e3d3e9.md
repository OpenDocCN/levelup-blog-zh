# 学习 Python:读写文本文件

> 原文：<https://levelup.gitconnected.com/learning-python-reading-and-writing-text-files-ab3d70e3d3e9>

![](img/a99c43fcff50437e763fe180b3d14937.png)

照片由[亚当·伯基特](https://unsplash.com/@abrkett?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在某一点之后，你会想要开始存储一些由你的 Python 程序处理的数据。存储数据以备后用的最简单方法是将数据放在文本文件中。在本文中，我将讨论如何使用 Python 将数据写入文本文件，以及如何将存储在文本文件中的数据读入 Python 程序。

# 将数据写入文本文件

我们将从将数据写入文本文件开始。Python 有一个打开文本文件的特殊函数，open。这个函数有两个参数:一个文件名和一个模式，它表示您希望如何打开文件。该函数返回一个 file 对象，您将使用它来处理文件。

下面是 open 函数的语法模板:

*file-object = open("文件名"，"模式")*

下面是一行代码，它通过打开一个文件来创建一个 file 对象，这样它就可以被写入:

```
fileOut = open("text.txt", "w")
```

当你打开一个文本文件进行写入时要小心，因为 open 函数会用给定的文件名创建一个新文件，并删除同名的现有文件。

当您处理完文件时，应该关闭所有打开的文件。Python 为此提供了一个`close`函数。下面是语法模板:

*file-object.close()*

请注意，如果您不调用该函数，操作系统将关闭该文件，但显式关闭一个打开的文件被认为是良好的编程实践，这样程序的读者就知道该文件正在关闭。

打开文件后，您可以使用`write`功能向其中写入文本。该函数将一个字符串作为参数，并将该字符串写入文本文件中的下一行。下面是`write`函数的语法模板:

*file-object.write(字符串)*

下面是一个小程序，它创建一个新的文本文件，并向该文件中写入几行文本:

```
fileOut = open("text.txt", "w")
fileOut.write("Now is the time\n")
fileOut.write("for all good people\n")
fileOut.write("to come to the aid\n")
fileOut.write("of their party.\n")
fileOut.close()
```

我们可以通过列出文件内容来检查文件的内容:

```
C:\python>type text.txt
Now is the time
for all good people
to come to the aid
of their party.
```

注意，我在每一行的末尾加上了`\n`，这样就可以写出一个新行。如果没有它们，文件将如下所示:

```
Now is the timefor all good peopleto come to the aidof their party.
```

`write`函数不会自动添加新行。你必须自己提供。

如果我想把一组数字写到一个文件里怎么办？`write`函数只接受字符串，所以如果你想用这个函数给文件添加数字，你必须先把数字转换成字符串。这里有一个如何做到这一点的例子:

```
fileOut = open("grades.txt", "w")
fileOut.write(str(82))
fileOut.write("\n")
fileOut.write(str(77))
fileOut.write("\n")
fileOut.write(str(81))
fileOut.close()
```

这个程序会把所有的数字写在同一行上。您可能希望它们在单独的行上，所以需要在每个数字之间写一个换行符，如下所示:

```
fileOut = open("grades.txt", "w")
fileOut.write(str(82))
fileOut.write("\n")
fileOut.write(str(77))
fileOut.write("\n")
fileOut.write(str(81))
fileOut.close()
```

# 读取文本文件

通过用文件名调用`open`函数来读取文本文件。您不必提供模式参数。这里有一个例子:

```
fileIn = open("text.txt")
```

一旦文件被打开读取，您可以使用`readline`功能读取一行文本。以下是该函数的语法模板:

*variable-name = file-object . readline()*

下面的程序打开一个文件进行读取，并将第一行读入程序:

```
fileIn = open("text.txt")
line = fileIn.readline()
print(line)
fileIn.close()
```

`readline`函数将完整的文本行读入变量，包括换行字符，这样每行文本都可以在自己的行上。如果我们再添加两次对`readline`函数的调用，我们可以读到以下文本:

现在是所有善良的人们伸出援手的时候了

它是这样输出的:

```
Now is the timefor all good peopleto come to the aid
```

这是因为`print`函数打印作为该行一部分的换行符，并添加自己的换行符。如果我们只是想要打印函数中的换行符，我们可以通过调用`strip` string 方法去掉我们读入的文本中的换行符。

这是新的程序:

```
fileIn = open("text.txt")
line = fileIn.readline()
line = line.strip()
print(line)
line = fileIn.readline()
line = line.strip()
print(line)
line = fileIn.readline()
line = line.strip()
print(line)
fileIn.close()
```

以下是该程序的输出:

```
Now is the time
for all good people
to come to the aid
```

到目前为止，我已经演示了如何从文件中读入特定数量的行。很多时候你在读文件的时候不知道文件的大小，所以你需要一些更好的方法来处理文件。

一种方法是使用带有关键字`in`的`for` 循环。下面是一个使用上述程序中的文件的示例:

```
fileIn = open("text.txt")
for line in fileIn:
  line = line.strip()
  print(line)
fileIn.close()
```

这将打印没有任何空行的文本文件。如果不需要保存删除的代码行，我们甚至可以将这个程序缩短一点:

```
fileIn = open("text.txt")
for line in fileIn:
  print(line.strip())
fileIn.close()
```

如果从文件中读取的数据是数字，那么在从文件中读取数据时，必须将数据转换为数字。以下程序从文本文件中读取一组考试成绩，并计算成绩的平均值:

```
gradesIn = open("grades.txt")
total = 0
count = 0
for line in gradesIn:
  grade = int(line)
  total = total + grade
  count = count + 1
average = total / count
print("The grade average is",average)
gradesIn.close()
```

以下是 grades.txt 文件的内容:

81
77
92
89
74
100

以下是该程序的输出:

```
The grade average is 85.5
```

# 将数据追加到文本文件

通过使用模式`a+`和`open`功能打开文件，可以将文本添加到文件中。以下是此版本函数的语法模板:

*file-object = open(" file-name "，" a+")*

假设有一个文件，其中写有以下几行:

这是 1 号线
这是 2 号线

我们可以用这个程序添加更多的行:

```
fileAppend = open("lines.txt", "a+")
fileAppend.write("This is line 3\n")
fileAppend.write("This is line 4\n")
fileAppend.close()
```

文件现在看起来像这样:

```
C:\python>type lines.txt
This is line 1
This is line 2
This is line 3
This is line 4
```

我们可以对包含数字数据的文件做同样的事情:

```
fileAppend = open("grades.txt", "a+")
for i in range(1, 4):
  grade = input("Enter a grade: ")
  fileAppend.write(grade + "\n")
fileAppend.close()
```

请注意，我必须在成绩的末尾连接一个换行符，这样每个成绩都写在自己的行上，而不是所有三个成绩都在同一行上。

Python 中还有更多关于文件处理的内容，我将在以后的文章中介绍这些特性。

感谢您的阅读，请发邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)告诉我您的意见和建议。