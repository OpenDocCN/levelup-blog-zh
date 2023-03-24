# 如何像专业人士一样用 Python 处理文件

> 原文：<https://levelup.gitconnected.com/how-to-handle-files-with-python-like-a-pro-16ef6bb43e98>

## 使用 Python 删除、读取和写入文件的简单指南

![](img/655123bccfba983d4a6bbf80ee13dcb6.png)

萨拉·库菲在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

文件处理是最重要和最常用的技能之一。用 Python 做任何类型的实际应用(如 Web 报废、数据科学或 Web 应用)都需要它，这一事实可以看出它的受欢迎程度。然而，在学习 Python 时，它经常被忽略，但现在不再是了！

有了这个基本技能，你将能够打开，编辑和操作无限的文件格式。例如，你将能够操纵流行的格式，如:

1.  逗号分隔值(CSV)
2.  活力
3.  纯文本
4.  JSON
5.  可扩展标记语言
6.  超文本标记语言
7.  形象
8.  便携文档格式
9.  DOCX
10.  MP3 文件
11.  MP4

请注意，在本文中，我们将主要关注。txt 文件，但是这里使用的方法也可以用于其他格式。所以让我们开始真正的交易吧！

# 1.如何打开文件

![](img/f84ce930980b14a87a8d3da039ced64f.png)

要打开一个文件，非常简单，只需使用 Python 内置的 ***open()*** 函数。

如果文件存在于当前目录中，那么只要文件名就足够了。Python 将在同一个文件夹中查看该文件。否则，如果文件存在于当前目录之外，则需要该文件的完整路径。

在下面的代码中，mode 指定了需要对文件做什么以及应该如何打开它。

```
file = open(name, mode)
```

下面是经常使用的模式列表，还有一些其他的我没有在下面提到，因为它们没有被广泛使用。

```
1\. r: Read opens a file for reading only. (default)2\. w: Write opens a file for writing only, deleting earlier contents3\. a: Append opens a file for appending.
```

例如。

```
f=*open*("scrap.txt","w")
```

上面一行代码将以只写模式打开当前目录中的 scrap.txt 文件。如果存在任何名为 scrap.txt 的文件，它将被删除，并创建一个具有给定名称的新文件。

如果你不想删除已经存在的文件，选择 ***和*** 是正确的选择。

> 注意:如果在模式参数中没有给出任何东西，那么默认情况下它被设置为 **r**

# 2.如何读取文件

![](img/edf9cbbca85b25dd761bdad8d197aa2b.png)

要读取文件的内容，必须先打开它。

```
f=*open*("scrap.txt") *print*(f.read())
```

> **注意:我们使用 read()函数是因为，open 函数只是存储文件的路径和其他设置，而不是内容。**

如果您尝试在不使用 read 函数的情况下打印 f 变量，您将在控制台日志中得到以下内容，这些内容主要是文件的详细信息而不是内容。

```
<_io.TextIOWrapper name=’scrap.txt’ mode=’r’ encoding=’UTF-8'>
```

还有另一种方法来查看文件的内容，这种方法具有更好的控制，那就是遍历文件的每一行。

```
f=*open*("scrap.txt")

for line in f:
    *print*(line)
```

# 3.如何写入文件

![](img/6e8560c1813fa251492fa36d8628badf.png)

要写入文件，必须使用 write ***w*** 或追加 ***a*** 作为方法。

```
f=*open*("scrap.txt","w")
f.write("This is how you can use Python to write data in files")
f.close()
```

> **提示:要添加新的空行，您可以在代码中使用\n 字符。这将添加一个空行，否则，你会很快发现一切都乱糟糟的。**

```
f.write("This is how you can use Python to write data in files\n")
f.write("Added a new line")
```

# 4.如何在 Python 中关闭文件

使用完文件后关闭它是很重要的，因为关闭文件将释放文件以前使用的所有资源。

```
f=*open*("scrap.txt","w")
f.close()
```

# 5.如何删除文件

![](img/c666bfc0c493975eb50cd99dfdea84f8.png)

要删除文件，您必须导入 Python OS 模块。

```
import os
os.remove(“scrap.txt”)
```

# 结论

在本文中，我们看到了如何使用 Python 打开、读取、写入和删除文件。有了这项技能，可能的用例是无穷无尽的，因为几乎所有的事情都与操作文件有关。无论是网络报废、自动化、网络应用等…

谢谢你，我希望你喜欢这篇文章:)