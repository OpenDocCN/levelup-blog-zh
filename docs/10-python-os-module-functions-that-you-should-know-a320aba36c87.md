# 你应该知道的 10 个 Python OS 模块函数

> 原文：<https://levelup.gitconnected.com/10-python-os-module-functions-that-you-should-know-a320aba36c87>

## 必须知道 python 中的操作系统模块函数

![](img/17522b9f940af99624182b07a5f453c7.png)

照片由 [Lorenz Lippert](https://unsplash.com/@lorenzlippert?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在使用 Python 时，我们需要利用底层操作系统的功能，无论是 Windows 还是 Linux。Python 编程中有一个操作系统模块，允许我们与底层操作系统进行交互，并使用依赖于操作系统的功能。

这个操作系统模块使我们能够使用底层的操作系统功能，例如获取当前工作目录的路径、创建新的目录或登记目录中所有文件和文件夹的名称等。

在本文中，我们将介绍 OS 模块中一些最常用的内置函数。我们开始吧！

# 1.getcwd()

这个`getcwd()`函数返回一个代表当前工作目录的字符串。当前工作目录是运行 Python 脚本的位置/文件夹。

示例:

```
import oscwd = os.getcwd()
print("Current working directory path:", cwd) Output:
Current working directory path: C:\Users\Pralabh\Workspace
```

# 2.chdir()

这个`chdir()`方法用于改变当前的工作目录路径。此方法将单个参数作为新的工作目录路径，并将当前工作目录更改为指定的路径。

示例:

```
import osprint("Current path:", os.getcwd())
os.chdir("C:\Users\Pralabh\Workspace\Python")
print("New path:", os.chdir())Output:
Current path: C:\Users\Pralabh\Workspace
New path: C:\Users\Pralabh\Workspace\Python
```

# 3.列表目录()

这个`listdir()`方法用于获取指定目录路径中所有可用文件和目录的列表。此方法将指定目录的路径作为参数。

如果没有将路径指定为参数，那么它将返回当前工作目录中的文件和目录列表。

示例:

```
import ospath = "/users/python"
print(os.listdir(path))Output:['Doc', 'etc', 'include', 'Lib', 'libs', 'LICENSE','NEWS.txt', 'python.exe', 'Scripts', 'share', 'tcl', 'Tools']
```

# 4.mkdir()

在 OS 模块中，这个`mkdir()`方法用于在指定的路径下创建一个新的目录。

示例:

```
import osos.mkdir("directory_name")
```

将创建一个名为 **directory_name 的新目录。**

# 5.path.join()

在 OS 模块中，这个`path.join()`用目录分隔符(“/”)连接各种路径组件，后跟路径的非空部分。此方法返回带有串联路径的字符串。

这里有一个例子:

```
import osbasePath = "/users/pralabh"
print(basePath)newPath = os.path.join(basePath, "python")
print(newPath)Output:
/users/pralabh
/users/pralabh/python
```

# 6.path.isfile()

这个`path.isfile()`方法是 OS 模块中非常有用的方法。此方法用于检查指定的路径是否属于现有文件，并返回一个布尔值。

这里有一个例子:

```
import osos.path.isfile("/users/python/train_data.csv")Output:
True
```

如果有一个文件属于指定的路径，那么它将返回 True。否则，它将返回 false。

# 7.path.exists()

这个`path.exists()`方法用于检查指定的文件是否存在。该方法将文件名作为参数。如果具有指定文件名的文件存在，那么它将返回 True，否则，它将返回 False。

这里有一个例子:

```
import osprint(os.path.exists("temp_filename"))Output:
False
```

# 8.path.isdir()

这个`path.isdir()`方法用于检查指定的路径名是否属于一个现有的目录。如果给定路径名的目录存在，那么它将返回 True，否则，它将返回 False。

这里有一个例子:

```
import osprint(os.path.isdir("/users/python/train_data.csv"))
print(os.path.isdir("/users/python"))Output:False
True
```

在第一个示例中，它返回了 False，因为给定的路径属于一个文件，而不是一个目录。在第二个例子中，它返回 True，因为给定的路径属于一个目录。

# 9.path.dirname()

这个`path.dirname()`方法用于从指定的路径中获取目录名。

此方法返回一个字符串，该字符串表示指定路径中的目录名。

这里有一个例子:

```
import ospath = "/home/user/python/train.csv"
dirname = os.path.dirname(path)
print(dirname)Output:
/home/user/python
```

# 10.马克迪尔斯()

在 OS 模块中，这个`makedirs()`方法用于递归地创建一个目录。在创建叶目录时，如果给定路径中的任何中间级目录缺失，那么该方法将创建所有这些目录。

这里有一个例子:

```
import osdirectory = "data"
main_directory = "C:/users/python"path = os.path.join(main_directory, directory)os.makedirs(path)
```

# 结论

这就是这篇文章的全部内容。在本文中，我们介绍了 Python 中一些有用的操作系统模块函数。当我们想要利用底层操作系统的功能时，这些函数非常有用。所以为了更好的实践，练习这些功能。

感谢阅读！

你可以在这里免费订阅我的时事通讯: [Pralabh 的时事通讯](https://pralabhsaxena.medium.com/subscribe)