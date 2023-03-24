# 使用 PEP 8 标准检查和格式化 Python 代码

> 原文：<https://levelup.gitconnected.com/checking-and-formatting-python-code-with-pep-8-standards-c158be7eee5d>

![](img/5c63acf00277c662fb96b2ff7fafea0e.png)

克里斯·里德的照片

## 如何检查你的 Python 代码是否按照 PEP 8 标准格式化？

## 首先，PEP 8 是什么？

PEP 代表 *Python 增强提案*

标题为“Python 代码的**风格指南**的第 8 号 Python 增强提案(PEP 8)被认为是 Python 代码的标准风格指南。

所以如果你想写符合标准的代码，建议遵循 PEP 8。

您可以在本文末尾找到该提案的链接。

## 你怎么做到的？

您可以自动化一些过程来检查您的代码是否符合它，甚至自动化大部分的重新格式化。

因此，在我们修复任何东西之前，我们需要检查需要修复什么，为此，您可以使用的工具之一是 *pycodestyle*

## 用 pip 安装 pycodestyle

```
pip install pycodestyle
```

安装后，您可以检查您的 python 文件，以查看为了符合 PEP 8 需要修复的所有内容

## 使用 pycodestyle

```
pycodestyle --first backend/test_flaskr.py
```

运行它将在屏幕上显示所有需要修复的行，以及它们在这个特定的 python 文件中违反了什么规则。

或者对所有文件运行它，您也可以这样做:

```
pycodestyle .
```

我将在文章末尾留下一个指向 pycodestyle 文档的链接，在那里您可以找到所有可能的选项，如果您想了解更多信息，还可以使用更多高级配置。

所以一旦你知道你可以手动修改代码，但是有一个更快更简单的方法(特别是当你需要修改很多行的时候！)

## 安装 autopep8

```
pip install autopep8
```

一旦完成，你所需要做的就是指向你想要格式化的文件，看看奇迹发生了。

## 使用 autopep8

```
autopep8 --in-place --aggressive --aggressive --recursive .
```

一旦完成，如果您使用 pycodestyle 再次检查您的代码，大多数警告应该会消失，但很可能不是全部，有些您可能需要手动修复。

```
pycodestyle .
```

现在你有了，你的代码应该符合 PEP 8 了！

如果你感兴趣，你也可以检查一下 *pylint* ，它不仅仅是检查 PEP 8，还可以捕捉一些可能的错误和代码气味。

## 想更进一步吗？

如果你想更进一步，也遵守 PEP 257 title **Docstring 约定**

为此，您可以使用 *pydocstyle*

它不会为您修复 docstring，但会让您知道您需要在哪里修复它！

要用 pip 安装它，只需运行:

```
pip install pydocstyle
```

要使用它，很简单:

```
pydocstyle file_name.py
```

要分析单个文件或简单地:

```
pydocstyle
```

来分析所有的 python 文件！

## 奖励材料

另一种在你的 ide 上快速检查你的风格指南的方法是使用 SonarLint，它可以在大多数 IDE 上使用，而且是免费的！

## 资源链接

你可以在这里阅读 PEP 8 官方文档:[https://www.python.org/dev/peps/pep-0008/](https://www.python.org/dev/peps/pep-0008/)

人教版 257:
[https://www.python.org/dev/peps/pep-0257/](https://www.python.org/dev/peps/pep-0257/)

pycodestyle:
[https://pypi.org/project/pycodestyle/](https://pypi.org/project/pycodestyle/)
[https://pycodestyle . pycqa . org/en/latest/intro . html #简介](https://pycodestyle.pycqa.org/en/latest/intro.html#introduction)

autopep 8:
https://pypi.org/project/autopep8/

pydocstyle:
http://www.pydocstyle.org/en/stable/

给皮查姆的 SonarLint:
[https://plugins.jetbrains.com/plugin/7973-sonarlint](https://plugins.jetbrains.com/plugin/7973-sonarlint)

## 你觉得这篇文章有用吗？

分享你的评论和经验！让我们知道你的想法。

如果你喜欢这篇文章并想看更多，请给这篇文章一些掌声。

要了解最新信息，请务必关注，直到下次！

## 你在找导师吗？取得联系！

你可以在这里找到我的社交网络链接:[https://linktr.ee/gnstudenko](https://linktr.ee/gnstudenko)