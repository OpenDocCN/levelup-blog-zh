# 11 种强大的文件操作 Python 方法

> 原文：<https://levelup.gitconnected.com/11-powerful-python-methods-for-file-operations-f32377c4ddad>

![](img/08e75bf4bd4be479bed34c10749f4ca4.png)

照片由[亚历克斯·科特利亚斯基](https://unsplash.com/@frantic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/powerful-coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Python 是一种通用的编程语言，可以用于很多任务。

但是 python 最酷的一点是管理文件和对文件执行多种操作的能力，而不需要外部库或资源。

为什么要使用 Python 呢？

例如，重命名一个文件最好手动完成。但是，如果您想根据 10k 文件的修改日期来重命名它们，或者根据它们包含的内容来重命名，该怎么办呢？

为成千上万的文件做这件事要花很长时间！

不仅如此，还有几个惊人的事情会让你的任务变得更容易，给你的文件和文件夹的权力，继续阅读找到答案。

会很棒的！

# 1.获取当前目录

在进行文件操作时，最重要的部分是了解当前目录。

这也是您应该做的第一件事，第一个原因是您可以仔细检查您是否在正确的目录中，从而提供一个总体的认识。

您应该使用当前目录的另一个主要原因是它使应用程序变得动态。

绝对路径是系统特有的。运行使用绝对路径的脚本不能保证它会运行。

同时，相对路径，或者首先获取当前目录，然后使用它转到不同的文件夹树，将确保它是一致的。

要获得当前目录，需要导入一个名为 OS 的 python 模块。OS 模块是做什么用的？

Python 中的 OS 模块提供了创建和删除目录(文件夹)、获取其内容、更改和标识当前目录等功能。

因此，要与底层操作系统交互，需要使用这个模块。

所以让我们开始吧…

```
import os
os.getcwd()
```

```
OUPUT: 'C:\\Users\\Falcon\\Desktop\\Notebooks'
```

# 2.更改目录

`chdir()`用于将目录从当前工作目录改变到指定路径。

这个方法只接受一个参数作为新的工作目录路径。

```
os.chdir(‘C:\\Users\\Ali\\Desktop\\)
```

# 3.列出当前目录中的文件

要获取当前目录中的文件，我们可以使用`listdir()`方法。它接受一个路径作为参数，如果你没有指定路径，那么它接受当前目录作为它的路径。

注意，这个方法以列表的形式返回目录名。

```
os.listdir()
```

```
Output:[‘.ipynb_checkpoints’,
 ‘desktop.ini’,
 ‘d_and_a’,
 ‘linearAlgebra’,
 ‘Notebooks’,
 ‘PythonScripts’,
 ‘Untitled Folder’]
```

# 4.正在创建目录

创建新目录有两种方法，命名为`mkrdir()`和`makedirs()`

我个人使用`makedirs()`方法，原因是它递归地创建目录。这使我们能够创建多个目录。

例如，如果您指定的路径不存在，这个方法将为您创建这些目录，因为它是递归运行的。

而`mkdir()`方法创建一个您已经指定的目录路径。并且该路径应该存在，否则它将抛出一个错误。

注意:如果已经存在同名的目录，那么将抛出一个错误。

```
os.makedirs(“data/pictures”)
```

我没有数据文件夹，但是因为我使用了`makedirs()`方法，它为我创建了。

# 5.删除目录

要删除目录，我们可以用`rmdir()`的方法。我们需要确保文件夹是空的，否则它不会被删除并抛出一个错误，说目录不是空的。

因此，在尝试使用操作系统模块删除文件夹之前，您需要移出文件。

```
os.rmdir(“data”)
```

# 6.删除文件

要删除一个文件，我们可以用`os.remove()`的方法删除一个文件。`rmdir()`方法用于删除一个目录，而这将删除文件。如果您尝试删除目录，将会引发错误。

```
os.remove(file1.txt)
```

# 8.重命名文件

要重命名一个文件，我们可以使用`os.rename()`方法。我们必须给出两个参数，第一个是文件路径，另一个是它的新名称。

```
os.rename(file1.txt, ‘new_file.txt’)
```

# 9.检查文件是否存在

我们可以使用`os.path.exists()`方法来检查一个文件是否存在。该方法返回一个布尔值，该值为 true 或 false。

```
os.path.exists(‘file1.txt’)
```

# 10.加入路径

顾名思义，这个方法连接了两条路径。此方法返回带有串联路径的字符串。

除了连接路径，它还有一个有用的功能。Windows 和 mac 对文件路径使用不同的斜线。这个方法根据脚本运行的系统动态地使用右斜杠。

简单地说，join 函数为我们处理 OS 限制，并为我们生成 join 路径文件。

```
path_1=os.getcwd()
path2=”newfile.txt”
os.path.join(path1,path2)
```

注意:我们可以给`join()`方法任意多的参数。

# 11.显示统计数据

我们可以使用`os.stat()`方法来显示该文件夹或文件的统计数据。运行这个方法将给出关于该文件或文件夹的一系列有用信息。

其中一些是以字节为单位的大小、修改时间等等。

```
os.stat(os.getcwd())
```

# 结论

在本文中，我们研究了 OS 模块为使用 Python 处理文件提供的一些功能。

我们研究的函数是:

`getcwd()`

`chdir()`

`listdir()`

`mkrdir()`

`makedirs()`

`rmdir()`

`os.remove()`

`os.rename()`

`os.path.exists()`

`join()`

`os.stat()`