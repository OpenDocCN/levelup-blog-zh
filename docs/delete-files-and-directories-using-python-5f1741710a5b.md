# 使用 Python 删除文件和目录

> 原文：<https://levelup.gitconnected.com/delete-files-and-directories-using-python-5f1741710a5b>

## 迷你项目| Python 101 系列

![](img/9c182b00191733aab2117f47095db38f.png)

使用 Python 删除文件和目录

到目前为止，我们已经学习了很多 Python 的基本概念。我们了解了:

1.  数据类型和变量
2.  字符串操作方法
3.  列表和字典操作方法
4.  条件语句
5.  环
6.  文件处理

但是现在呢？仅仅通过阅读博客或观看 YouTube 教程，我们无法擅长某件事。我们必须不断练习，直到我们擅长为止。所以，在这篇博客中，我会试着展示我们到目前为止学到的大部分概念。

> *“对于我们在做之前必须学习的东西，我们通过做来学习。”* ― **亚里斯多德**

那么，我们要做什么？

# 规划

在这篇博客中，我将向你展示

1.  我们如何使用 **os** 模块列出特定位置的所有文件和目录
2.  分离文件和目录，并使用 **isfile()** 方法将它们添加到各自的列表中
3.  使用 **for** 循环遍历这些文件和目录列表
4.  使用**操作系统**模块删除文件
5.  使用 **shutil** 模块删除目录

除此之外，我们还会看到一些 Python 中的错误处理。即使我们测试我们的代码，在现实环境中，意外的错误最终会出现。为了解决这些情况，我们使用错误处理。错误处理是一个通用的编程概念，它不是 Python 独有的概念。

# 编码

在我们进入代码之前，我想提一下，如果你不理解任何一行或有任何疑问，你可以随时在 [Twitter](https://twitter.com/Sahil_Fruitwala) 或 [LinkedIn](https://www.linkedin.com/in/sahilfruitwala) 上联系我。我会分块解释代码，也会在博客的最后添加完整的代码文件。我们将继续:

# 1.导入必要的模块

```
import os
import shutil
```

# 2.获取特定位置的所有文件和目录的列表

```
# location of temp files in windows
location = 'C:\\Windows\\Temp' 
file_dir = os.listdir(location)
```

*注意:Mac 人，你可以使用任何你希望程序不会停止工作的临时文件夹或目录。我是 windows 用户，所以不知道 mac 设备上的临时文件存储在哪里。*

# 3.文件和目录的分离

listdir()方法返回文件和目录。所以，我们需要将文件和目录从 file_dir 中分离出来。

```
only_files = []
only_dir = []
for i in file_dir:
       # generate full path
       full_path = os.path.join(location, I) # checks if the given path is of file or not
       if os.path.isfile(full_path):
           only_files.append(full_path) # checks if the given path is of directory or not
       if os.path.isdir(full_path):
           only_dir.append(os.path.join(full_path)
```

# 4.删除文件

```
# loop through the list that contains only files
for i in only_files:
    try:
        # os.chmod(i, 0o777)
        os.remove(i) # removes the files
    except Exception as e:
        print(e)
```

这里，我们保留了整个删除逻辑(一行😂)进入`try and except`块。我们为什么要这样做？文件可能已被移动，或者我们没有访问该文件的权限。这种情况会引发错误/异常，我们的代码将停止工作。有许多方法可以处理异常。但是，我选择了简单易行的方法，只需打印错误消息。别担心，我们将在下一篇博客中学习异常处理。

# 5.删除目录

```
# loop through the list that contains only directories 
for i in only_dir:
    try:
        # os.chmod(i, 0o777)
        shutil.rmtree(i, ignore_errors = False) # removes the the directory and all the sub-files and sub-directory that it might have
    except Exception as e:
        print(e)
```

`shutil.rmtree()`方法将接受输入，并尝试删除目录及其所有子文件和目录。这里，我们希望在方法无法删除某些文件/目录时引发异常。因此，我们传递了值为 false 的 **ignore_errors** 参数。

注意:在 Windows 中，有时你会面临权限问题。为了避免这些问题，请打开您的编辑器或控制台，以管理员身份运行这些代码。对于 mac，取消对`**os.chmod(i, 0o777)**`部分的注释。它应该工作，但不确定，因为我没有在 mac 系统中尝试过。

这个演示只是为了更好地理解我们到目前为止所学的概念。我们可以通过在将文件/目录分成两个列表的同时删除它们来优化代码。但是这也是为了让你理解如何使用 python。

# 6.将整个逻辑转换成一个函数

可重用性在编程领域非常重要。对这个程序来说这不是必须的，只是为了向你展示我们将把整个逻辑转换成一个函数。我们只调用这个函数。

```
def delete_temp_file(locations):
    for location in locations:        
        try:
            # get list of all files and directories
            file_dir = os.listdir(location)             only_files = []
            only_dir = [] for i in file_dir:
                # checks if the given path is of file or not
                # print(location+'\\'+i)
                # print(os.path.join(location, i))
                full_path = os.path.join(location, i)
                if os.path.isfile(full_path):
                    only_files.append(full_path) # checks if the given path is of directory or not
                if os.path.isdir(full_path):
                    only_dir.append(full_path)
        except Exception as e:
            print(e) # loop through the list that contains only files 
        for i in only_files:
            try:
                # os.chmod(i, 0o777)
                os.remove(i) # removes the files
            except Exception as e:
                print(getattr(e, 'message', repr(e))) # loop through the list that contains only directories 
        for i in only_dir:
            try:
                # os.chmod(i, 0o777)
                # removes the the directory and all the sub-files and sub-directory it has
                shutil.rmtree(i, ignore_errors = False)
            except Exception as e:
                print(e)
```

现在，执行时间！😎

# 最终接触和执行

这是重复的，但我已经添加了完整的代码，以便您可以在一个流程中看到它。在你运行它之前，根据你的需要编辑位置列表。然后转到保存代码库的位置，运行`python file_name.py`。

终于！我们已经到了这一节的末尾😁。

就这样。感谢您的阅读。

如果你需要任何帮助或者想讨论什么，请告诉我。在 Twitter 或 LinkedIn 上联系我。请务必在下面的评论中留下你的想法、问题或担忧。我很想看看他们。

> 想了解更多？
> 
> 注册我的[时事通讯](https://bit.ly/3Menk8Q)，把最好的文章放进你的收件箱。

直到下一次👋

> 探索我的 Python 101 系列的其他博客

[](https://betterprogramming.pub/organize-files-and-folders-using-python-660246ef9310) [## 如何使用 Python 组织文件和文件夹

### 3 件主要的事情将帮助我们决定如何组织我们的文件

better 编程. pub](https://betterprogramming.pub/organize-files-and-folders-using-python-660246ef9310) [](/file-handling-in-python-6ffc23cc92c) [## Python 中的文件处理

### 所以，只是为了刷新我们的记忆，到目前为止，我们已经看到了 python 的所有基础知识，包括变量、数据类型、函数…

levelup.gitconnected.com](/file-handling-in-python-6ffc23cc92c) [](/modules-and-packages-in-python-63df7d952bb8) [## Python 中的模块和包

### 了解模块和包的基础知识以及如何在 python 中使用它们

levelup.gitconnected.com](/modules-and-packages-in-python-63df7d952bb8)