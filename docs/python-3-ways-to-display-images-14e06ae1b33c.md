# Python 3 种显示图像的方式

> 原文：<https://levelup.gitconnected.com/python-3-ways-to-display-images-14e06ae1b33c>

![](img/461fb4b4f1851b6d3a9a44efa3e0a613.png)

由 Matthew Altenburg 和 AI 创作的 Python 徽标

如果您正在寻找一个库来帮助您用 Python 显示图像，那么您很幸运。Python 有许多不同的库，可以用来显示图像。三个最流行的库是 **Pillow、Matplotlib 和 OpenCV** 。

你应该选择哪一个，最后归结为个人喜好。比起 Pillow 和 Matplotlib，我更喜欢和使用 OpenCV，因为它有更多的特性。但是，当我只是显示一幅图像或进行批量图像处理时，我会使用 Pillow。当我想在 Jupyter 笔记本中显示图像时，我使用 Matplotlib。

每个库都有其独特的优点和缺点，因此为手头的任务选择正确的库是至关重要的。在本文中，我们将深入探讨每个库，并向您展示如何充分利用每个库。

# 我们从枕头开始。

![](img/85f97e70e0a1405192c6e010ec1c6e78.png)

Matthew Altenburg 和 AI 创作的枕头标志

Pillow 是一个提供简单图像操作方法的库。它支持打开，操作，和保存许多不同的图像文件格式，是优秀的批量图像处理功能，如图像大小调整，旋转和变换。

枕头易于安装和使用。您可以使用 pip 安装它:

```
pip install Pillow
```

然后，要在新窗口中打开图像，请创建一个 python 脚本并运行:

就这样，三行代码，我们已经用枕头库打开了一个图像。

# 接下来，我们来看看 Matplotlib。

![](img/974759ac75008354a7d847815c23363d.png)

Matplotlib 徽标由 Matthew Altenburg 和 AI 创建

Matplotlib 是一个全面的库，用于在 Python 中创建静态、动画和交互式可视化，非常适合在 juypter 笔记本中打开图像。

让我们从使用 pip 安装 Matplotlib 开始:

```
pip install matplotlib
```

现在是在 jupyter 笔记本中打开和显示图像的代码

# 最后是 OpenCV。

![](img/4cd7b07def610d20bbbfd73f2da4e84b.png)

由 Matthew Altenburg 和 AI 创建的 OpenCV 徽标

OpenCV 是一个计算机视觉库，在 Python 社区中有着悠久的历史。它是开源的，免费的，而且非常强大。因此，这是我最喜欢的计算机视觉库，因为你可以用它做很多事情。

我进行了很多计算机视觉项目，这是我的首选。然而，它并不适合每个人，因为它有它的缺点，因为它是一个大的安装包，有一个更大的学习曲线，使它更具挑战性。因此，只有当您有一个需要更多专业图像处理和计算机视觉技术的大型项目时，才使用 OpenCV。

让我们从使用 pip 安装 OpenCV 开始:

```
pip install opencv-python
```

如果你想在 Jupyter 笔记本中使用 OpenCV，你还需要安装 OpenCV 贡献者模块。这可以通过以下命令完成:

```
pip install opencv-contrib-python
```

打开和显示图像的代码很简单。

# 暂时就这样了

感谢您花时间阅读本文。我希望这篇文章对你有益。请随时留下评论并提供反馈，以便我可以在必要的地方进行改进，并让我知道您希望在未来涉及的其他主题。