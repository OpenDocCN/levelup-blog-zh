# 每个开发人员都应该知道的终端命令

> 原文：<https://levelup.gitconnected.com/terminal-commands-that-every-developer-should-know-ba3b9c8cb9fb>

## 每个开发人员的终端命令

![](img/d7b5af26428ee548f0fdaaccbf2b437e.png)

Gabriel Heinzer 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

使用命令行界面时，终端命令非常有用。使用 CLI 时，它可以让您更好地控制运行文本命令的设备。

在本文中，我提出了一些有用的终端命令，每个开发人员都应该知道。我们开始吧！

# 0.显示当前工作目录

`pwd`代表打印工作目录。这个命令返回当前工作目录的路径，从根目录开始。

```
pwd
/Users/Pralabh/Desktop
```

# 1.限位开关（Limit Switch）

`ls`代表列表。该命令返回指定目录的内容。如果没有指定特定的目录，那么它返回当前工作目录中的内容列表。

```
/Users/Pralabh/Desktop$ ls
myfile.txt sample.txt 
```

# 2.清楚的

该`clear`命令用于清除终端屏幕。

```
$ clear
```

# 3.出口

该`exit`命令用于退出终端，并关闭当前运行的终端。

```
$ exit
```

用`exit`命令按 enter 后，终端将被关闭。

# 4.mkdir

该命令`mkdir`代表创建目录。该命令用于创建新目录。

```
/Users/Pralabh/Desktop$ ls
myfile.txt sample.txt
```

在这里，我们可以看到在我们当前的工作目录中有两个文件。现在，我们将使用`mkdir`命令创建一个新目录。

```
/Users/Pralabh/Desktop$ mkdir directory // creating a new directory/Users/Pralabh/Desktop$ ls
myfile.txt sample.txt
```

名为`directory`的新目录已经创建。

# 5.空间

该命令`rm`代表**移除**。该命令用于从给定的路径/目录中移除/删除文件。

```
$ ls
file1.txt file2.txt file3.txt file4.txt#Using rm to remove file3.txt
$ rm file3.txt $ ls
file1.txt file2.txt file4.txt
```

我们也可以使用这个命令删除多个文件

```
$ ls
file1.txt file2.txt file3.txt file4.txt#Using rm to remove file3.txt
$ rm file3.txt file2.txt$ ls
file1.txt file4.txt
```

# 6.删除目录

此`rmdir`命令用于从文件系统中删除一个空目录。如果目录为空，此命令将删除命令中指定的每个目录。

```
$ ls
dir1 dir2 dir3#Removing directory dir2
$ rmdir dir2$ ls
dir1 dir2
```

如果指定的目录包含某个文件或子目录，则不能简单地用`rmdir`命令删除。

# 7.激光唱片

该命令`cd`代表改变目录。该命令用于改变当前工作目录。

```
$ pwd
/Users/pralabhsaxena#Using command to change directory
$ cd Desktop/python$pwd
/Users/pralabhsaxena/Desktop/python
```

# 8.chmod

该命令`chmod`用于改变指定文件的访问模式。

语法:

```
$ chmod [reference][operator][mode] filename
```

# 9.平均变化

该命令`mv`用于将文件从文件系统中的一个目录移动到另一个目录。

```
#Moving file1.txt to Desktop
$ mv file1.txt Desktop/file1.txt$ cd Desktop$ ls
file1.txt
```

该命令也用于重命名文件，如果您试图在同一目录内移动文件，它将被重命名。

```
$ ls
file1.txt sample.txt #Renaming file1.txt to renamed.txt
$ mv file1.txt renamed.txt$ ls
renamed.txt sample.txt 
```

# 10.丙酸纤维素

该命令`cp`代表复制。该命令用于将文件或目录从一个地方复制到另一个地方。

`Syntax: cp [option] Source Destination`

```
$ cp file1.txt Desktop/file1.txt$ cd Desktop$ ls
hello1.txt
```

# 结论

这就是这篇文章的全部内容。在本文中，我们浏览了一些有用的终端命令。练习这些命令以便更好地动手操作。

感谢阅读！

> *在你走之前……*

如果你喜欢这篇文章，并希望**继续关注**更多**精彩**文章，请考虑使用我的推荐链接[https://pralabhsaxena.medium.com/membership](https://pralabhsaxena.medium.com/membership)成为一名中级会员。

另外，你可以在这里免费订阅我的时事通讯: [Pralabh 的时事通讯](https://pralabhsaxena.medium.com/subscribe)。