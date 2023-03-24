# Python 程序员必须学习的 5 个库

> 原文：<https://levelup.gitconnected.com/5-must-learn-libraries-for-python-programmers-d0932bfd0658>

## 这些库将帮助你废弃网站，构建图形用户界面，等等

![](img/276cb0252b33379b4c38f3dc210e3734.png)

照片由 [**设计生态学家**](https://www.pexels.com/@designecologist?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 来自 [**像素**](https://www.pexels.com/photo/silver-imac-displaying-collage-photos-1779487/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

Python 有大量用于计算机科学不同领域的库和包。但是其中一些非常受欢迎，使用非常广泛，每个 Python 开发人员都应该熟悉它们。

在本文中，我们将看看每个开发人员都应该知道的 5 个最流行的 python 库。

# **1。请求**

Requests 是最流行的 python 库之一，用于在 Python 中发送 HTTP 请求。

它主要用于与不同的 web 应用程序进行交互，以请求和接收来自网页的响应。库对 rest API 和 web 抓取非常有帮助。

它不是 python 内置的，所以我们必须使用下面的代码片段来安装它。

```
pip install requests
```

要从网站(比如 GitHub API)获取数据，我们可以使用几行代码。

```
import requestsr = requests.get(“[https://api.github.com](https://api.github.com)")print (r.status_code)
```

如果输出是 200，这意味着您的请求是成功的。404 状态代码表示请求不成功。

# **2。美丽组图**

它是用于 web 抓取的最常见的 Python 库之一。通过使用这个库，我们可以检查 HTML 结构并从 HTML 或 XML 解析树中收集数据。

我们可以使用这个库*以及 Python 请求库和 html5lib 解析器*从网页的 html 结构中提取任何信息。

这个库也需要使用下面的代码片段来安装。

```
pip install bs4
```

我们还需要使用 pip 安装 HTML 解析器 html5lib，如下所示:

```
pip install html5lib
```

现在让我们假设我们想要得到一个特定网页的 HTML 解析表。我们可以使用如下所示的几行代码来实现。

```
import requestsfrom bs4 import BeautifulSoupr = requests.get(“URL of the target site”)soup = BeautifulSoup (r.content, ‘html5lib’)print (soup.prettify())
```

# **3。Tkinter**

Tkinter 是开发 GUI 应用程序的标准 Python 库。该库是 Python 内置的，可用于创建在用户界面上运行的脚本。

要使用这个库，我们只需使用 python 中的 import 来导入它。

```
import tkinter (for Python 3.x)import Tkinter (for  Python 2.x)
```

要创建主窗口对象，我们可以使用 tk()方法，如下所示。

```
m = tkinter.tk()
```

mainloop()方法用于运行脚本。这是一个无限循环，运行应用程序，直到使用终止信号。

有像 pack()、grid()、place()这样的方法用来组织主窗口中的 Button()、Canvas()、CheckButton()这样的小部件。

# **4。Numpy**

Numpy 是数据科学和机器学习领域中最流行的 Python 库之一。numpy 这个名字来源于数字 python。这个库在创建多维数组和操纵它们进行计算时非常有用。

这个库在线性代数、图像处理和许多其他领域非常有用。

我们可以使用 pip 命令在我们的机器上安装这个包，如下所示。

```
pip install numpy
```

要为矩阵创建二维数组，我们可以执行以下操作:

```
import numpy as nparr1 = np.array( [[1,2],[3,4] )# creates a 2*2 matrix with elements 1,2,3,4arr2 = np.zeros((0,0))#creates an array 2*2 initialized with all zeros
```

# **5。Matplotlib**

另一个深受数据科学家欢迎的 Python 库。该库主要用于可视化目的，即以线条、条形图、直方图等形式绘制可用数据。

要安装软件包，我们需要运行以下命令

```
python -m pip install -U matplotlib
```

假设我们想在 2D 平面上绘制 3 个数字的线图。我们可以这样做

```
import matplotlib.pyplot as pltx = [1, 2, 3] # x axis valuesy = [4, 5, 6] # y axis valuesplt.plot (x, y)plt.show() # to show graph
```

# **结论**

有许多重要的 Python 包，如 tensorflow、Pytorch、Keras、OpenCV，它们广泛用于数据科学和机器学习领域，但我们没有在本文中介绍它们，因为它们非常特定于领域。

本文中提到的库可以用于几乎任何领域，这些库的知识对于几乎每个 Python 开发人员都非常重要。

我们将在下一篇文章中更多地讨论一些特定于数据科学的库。