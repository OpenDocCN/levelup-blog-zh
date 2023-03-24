# 面向开发者的五大 Python GUI 框架

> 原文：<https://levelup.gitconnected.com/top-5-python-gui-frameworks-for-developers-e29ae7f8f70a>

## 每当你第一次打开一个应用，他们首先看到的是什么？

![](img/f700d4512cb0888b1e5246e249143601.png)

由 [charlesdeluvio](https://unsplash.com/@charlesdeluvio?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

每当你第一次打开一个应用，他们首先看到的是什么？是用户界面！人们一打开应用程序，就会接触到设计和用户体验。

即使这个应用程序有着令人惊奇和新奇的功能，非常高的性能，或者强大的安全性，但是没有一个好的设计，所有的机会都将是徒劳的！这仅仅强调了 UI 的重要性。

UI 是大多数现代应用程序与应用程序交互的唯一方式，因此我们不能过分强调简约、坚固和易于使用的 UI 的重要性。这是成败的关键。

然而，要制作一个优雅的 UI，你不必使用复杂的框架、工具，或者学习一门新的语言。如果你了解 Python 的基础知识，那么你将会大吃一惊！

如果您已经掌握了 Python 的基础知识，那么您就可以很好地构建自己优雅而直观的用户界面。

令人惊奇的是，在很短的时间内，您就可以开始转换您已经构建的 python 应用程序，这些应用程序曾经只能使用命令行访问，现在可以变成漂亮的可点击应用程序了！我第一次做这个的时候感觉很棒，你们肯定也会的。

本文的目的是让您了解可用于构建 UI 的各种顶级 python 库及其优缺点，并最终帮助您选择正确的库或框架。

# 1.PyQT5

PyQT5 是 Python 的图形用户界面(GUI)框架。它在开发人员中很受欢迎，并有一个相当大的社区。有趣的是，它支持编码和拖放设计界面。

支持 Windows 和 Linux，但也可以支持 Ios 和 Android，但这不是它唯一的产品，但值得注意的是。

PyQT5 的显著特点是自带 QTDesigner，这是一个拖放界面。因此可以使用拖放来创建界面。太酷了。

对于 PyQT5 的安装，您可以使用以下命令:

`pip install pyqt5`

注意:PyQT5 的缺点是它不像下面提到的其他库那样是开源的，并且需要花钱获得使用许可。

# 2.Python Tkinter

Tkinter 是用于开发桌面应用程序的最流行的 Python GUI 库之一，在所有可用的库中，这是使用最多的一个。您可以在 Mac、Windows 或 Linux 上运行 Tkinter。

它受欢迎的主要原因是它简单易用。猜猜看，它预先包含在所有 Python 发行版中。因此，您可以看一眼它的基本代码，并尽可能地调整它。如果你想快速设置你的界面，那么这就是你要找的库。

# 3.PySide 2

我们要讨论的第三个 Python GUI 库是 PySide2，或者你可以称之为 Python 的 QT。

如果你不知道，Qt for Python 提供了 Qt 的官方 Python 绑定(PySide2)，允许在 Python 应用程序中使用其 API，以及一个绑定生成器工具(Shiboken2)，可用于将 C++项目公开到 Python 中。

据我所知，这个库没有什么特别的特性或独特的产品，我也没有亲自使用过它。试试吧，在评论里让别人知道吧！

# 4.基维

Kivy 是一个开源 Python 库，用于快速开发利用创新用户界面的应用程序，如多点触摸应用程序。基于 OpenGL，具有在 2d、3d 以及网格和着色器中绘制的功能。你可以用这个库制作游戏。

Kivy 提供了一个高性能和现代外观的 GUI。这个库的优势之一是 Kivy framework 是跨平台的，可以在 Windows、Linux、Android、iOS 和 Raspberry Pi、OS X 上运行。

来到 Kivy 的安装，需要安装依赖项“glew”。您可以使用 pip 命令，如下所示:

`pip install docutils pygments pypiwin32 kivy.deps.sdl2 kivy.deps.glew`

输入这个命令并按回车键，它将被安装。之后，您需要键入以下命令来安装 Kivy:

`pip install Kivy`

# 5.wxPython

wxPython 是一个用于 Python 的开源跨平台 GUI 工具包。它包装了用 C++编写的流行的 wxWidget 库，所以基本上，它是原始库的扩展，但是支持 Python。

我喜欢这个很酷的特性，因为它提供了原生支持。因此，您的应用程序看起来和感觉起来就像您操作系统中的另一个应用程序。无论是 Windows、Mac 还是 Linux。告别那些看起来像古代的怪异应用程序。

`pip install wxPython`

# 结论

由此而来的是本文的结尾。我们已经看到了一些最好的图形用户界面库，你可以用它们来制作你的下一个应用。

就我个人而言，对于我的副业项目，我喜欢使用 Tkinter 库，只是因为它的简单性、易于设置，以及它的大型社区。但是如果你想做一个移动应用程序，那就用 kivi 库吧。

谢谢大家看完，继续编码！