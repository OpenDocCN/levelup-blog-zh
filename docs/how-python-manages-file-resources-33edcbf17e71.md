# Python 如何管理文件资源

> 原文：<https://levelup.gitconnected.com/how-python-manages-file-resources-33edcbf17e71>

资源是计算机系统的物理(硬件)组件或虚拟组件(文件、网络连接和内存)，可用性有限。

当一个进程使用一个资源时，一旦进程终止就释放它是很重要的。

否则，这将导致 ***资源泄露*** 。

![](img/2d0185bae45e6c9861c58515d93b078a.png)

照片由 [Garmin B](https://unsplash.com/@garmin2001?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们举个例子。

# 与文件交互

要在 Python 中打开一个文件，我们可以使用内置的`open`函数。

```
file **=** open("my_file.txt", "r")# "r" is for the 'read' mode
```

要读取该文件并显示其内容，我们使用以下命令。

```
print(file.read())
```

如果我们停在这里，文件仍然是打开的，它的资源正在被使用。

为了释放它，我们使用了`close`方法。

```
file.close()
```

![](img/5162d776645b954026003b23d9bcd021.png)

大卫·布鲁诺·席尔瓦在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 与文件交互:更好的方式

Python 通过**上下文管理器**提供了一种简单的管理方法。

这是使用`with`关键字完成的。

将`with`关键字与`open`函数一起使用会创建一个上下文管理对象。

让我们看看怎么做。

```
with open("my_file.txt", "r") as file:
    print(file.read())
```

这样，我们就不必关闭正在使用的文件。一旦文件的内容被打印到屏幕上，Python 就会自动为我们完成这项工作。

![](img/e1c0d590aabba806172757b1897c51a9.png)

照片由[un splash](https://unsplash.com/@77hn?utm_source=medium&utm_medium=referral)上的 77 人类需求系统拍摄

# 这是如何工作的？

关键字`with`之后的语句返回一个包含两个 dunder 方法的上下文管理对象。

*   `__enter__`
*   `__exit__`

让我们通过创建自己的上下文管理器类来了解这些是如何工作的。

```
class ContextManager:
    def __init__(self, fileName, mode):
        self.fileName = fileName
        self.modeName = mode def **__enter__**(self):
        self.file = open(self.fileName, self.mode)
        return self.file def **__exit__**(self,exc_type, exc_value, exc_traceback):
        print(exc_type, exc_val, exc_traceback)

        self.file.close()
```

`__enter__`方法指定`ContextManager`将以名称`fileName`和模式`mode`打开文件(读/写)。

如果出现异常，`__exit__`方法使用`close`方法关闭文件，并打印类型(`exc_type`)、值(`exc_value`)和回溯(`exc_traceback`)。

该自定义类可与`with`一起使用，如下所示(由于上述 dunder 方法):

```
with ContextManager("my_file.txt", "r") as file:
    print(file.read())
```

这将输出类似于与`open`函数一起使用的默认上下文管理器。

![](img/3e615250d68380993e346b5739e46456.png)

照片由 [Prateek Katyal](https://unsplash.com/@prateekkatyal?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 进一步阅读

 [## PEP 343-“with”语句

### 这个 PEP 在 Python 语言中增加了一个新的语句“with ”,使它有可能推导出…的标准用法

peps.python.org](https://peps.python.org/pep-0343/) 

*希望这篇文章对你有帮助！感谢阅读！*

[](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium-Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)