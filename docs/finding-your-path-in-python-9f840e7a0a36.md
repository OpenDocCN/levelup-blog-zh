# 在 Python 中寻找路径

> 原文：<https://levelup.gitconnected.com/finding-your-path-in-python-9f840e7a0a36>

## 看到我做了什么吗？

![](img/63b5dc9e03e1db42b1d55ba3d4fba69f.png)

字面上的第一个结果在单词 path 的 Unsplash 上——照片由[斯蒂芬·莱昂纳迪](https://unsplash.com/@stephenleo1982?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash 上](https://unsplash.com/s/photos/path?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄

Python 的标准库有如此多的酷功能，以至于有些功能不可避免地被忽略了。我想这就是发生在 [pathlib](https://docs.python.org/3/library/pathlib.html) 身上的事情。现在不要误解我，我不是说 pathlib 是某种深奥的、未知的库，但它也不是大多数 Python 程序员在处理路径时首先想到的(肯定是字符串操作或 [os.path](https://docs.python.org/3/library/os.path.html) )。所以在这篇文章中，让我们来探索一下这个美丽的模块。

pathlib 模块在一个方便的地方收集了处理路径所需的所有工具，并提供了一个非常灵活的面向对象的 API 来处理这些工具。Pathlib 使用[路径 API](https://docs.python.org/3/library/pathlib.html#basic-use) 抽象出不同操作系统路径的不必要的复杂性(整个窗口使用\而 Mac 和 Linux 使用/ thing ),但足够灵活，可以使用 [PosixPath](https://docs.python.org/3/library/pathlib.html#pathlib.PosixPath) 和[windows Path](https://docs.python.org/3/library/pathlib.html#pathlib.WindowsPath)API 访问不同的 OS 级 API。

## 让我们开一条路

在 pathlib 中创建路径非常简单。至于传递给 Path 的字符串是基于 Windows 的还是基于 Linux 的，这没有太大关系:

```
from pathlib import Path
path = Path("files/file.txt")
```

> 从这里开始，我将假设路径是导入的

为了构造新的路径，我们使用/操作符。假设在上面的例子中，我们在 files 文件夹的上面有另一个文件夹。要构建路径，我们可以简单地使用:

```
new_path = "all_files"/path
```

只要/运算符的操作数中至少有一个是 Path 对象，这就可以工作。

## 一些基本用途

路径的一个非常常见的用例是选择路径所指向的文件的扩展名和文件名。我见过并使用过的一些解决方法包括:

```
simple_path = "all_files/files/file.txt"# To get the full file name
simple_path.rsplit("/")[-1]# To get the extension
simple_path.split(".")[1]
# This of course work under the assumption that no one will be monstrous enough to put a . in the folder name(of course they won't says .git). To make it more umm... robust you could do
simple_path.rsplit("/")[-1].split(".")[1]# To get just the file name
simple_path.rsplit("/")[-1].split(".")[0]
```

![](img/88ee38204c079aedd541f481dd1189cc.png)

我的上帝，这是很多截图

不太明显的是，这些并不是在 Python 中进行这些操作的唯一方法(在 Python 禅宗的精神中 keeeping)。我们本应该使用 [os.path.split](https://docs.python.org/3/library/os.path.html#os.path.split) 来保证跨平台兼容性。这正是 pathlib 大放异彩的地方。在 pathlib 中复制它:

要获取完整的文件名:

```
path.name
```

要获取扩展:

```
path.suffix # Note this returns the extension with dot included
```

最后只获得不带扩展名的文件名

```
path.stem
```

Pathlib 没有前面例子中的冗长和复杂。你的代码保持干净、漂亮，我认为可读性更好。如果你只是想要路径的字符串表示，你可以简单的使用

```
str(path)
```

同样，如果您想找到给定文件所在的目录，也很简单:

```
path.parent
>>> PosixPath('files')
```

现在，用 os.path 来实现这一点可能更棘手。如果您只想知道文件的完整路径，可以使用:

```
path.resolve()
>>> PosixPath('/content/files/file.txt')
```

如果你想要所有的路径直到根:

```
list(path.resolve().parents) 
>>> [PosixPath('/content/files'), PosixPath('/content'), PosixPath('/')]
```

## 迭代、匹配等等

另一个非常常见的操作是遍历目录。现在 pathlib 确实有一个名为 [iterdir](https://docs.python.org/3/library/pathlib.html#pathlib.Path.iterdir) 的方法来处理这个问题，但是我更喜欢使用 Path()。环球技术。要遍历目录中的所有文件:

```
dir_path = Path("/content")
for p in dir_path.glob("*"):
    print(p.name)# .config
# sample_data
```

如果您想递归地遍历目录，也就是进入每个单独的子目录并打印其中的所有路径，您可以很容易地使用 glob 来完成

```
for p in dir_path.glob("**/*"):
    print(p.name)
# .config# sample_data
# .last_update_check.json
# gce
# config_sentinel
# configurations
# .last_opt_in_prompt.yaml
# .last_survey_prompt.yaml
# active_config
# logs
# config_default
# 2021.11.01
# 13.34.28.082269.log
# 13.34.35.080342.log
# 13.34.08.637862.log
# 13.34.55.836922.log
# 13.34.55.017895.log
# 13.33.47.856572.log
# anscombe.json
# README.md
# mnist_test.csv
# california_housing_test.csv
# mnist_train_small.csv
# california_housing_train.csv
```

这基本上是 Python 中的 glob 模块，也就是说 glob 的所有功能都在这里。因此，如果只需要目录中的 csv 文件，我们可以简单地这样做

```
for p in dir_path.glob("**/*.csv"):
    print(p.name)# mnist_test.csv 
# california_housing_test.csv 
# mnist_train_small.csv
# california_housing_train.csv
```

这只是 pathlib 中所有功能的极小一部分。如果你想要更深入的 pathlib 指南，这个 [RealPython](https://realpython.com/) 指南可能会有帮助:

[](https://realpython.com/python-pathlib/) [## Python 3 的 pathlib 模块:驯服文件系统——真正的 Python

### 由于许多不同的原因，使用文件和与文件系统交互是很重要的。最简单的情况…

realpython.com](https://realpython.com/python-pathlib/) 

如果你想在你自己的项目中即插即用一套收据，这个庞大的列表可能就是你要找的:

[](https://miguendes.me/python-pathlib) [## 关于软件开发、测试和 Python 的写作。

### 当我开始学习 Python 的时候，有一件事我一直很困扰:处理路径！我记得…

米根德斯.我](https://miguendes.me/python-pathlib) 

今天到此为止！希望你有愉快的一天。

> 如果你有任何问题，请随时在 LinkedIn 或 Twitter 上给我留言。