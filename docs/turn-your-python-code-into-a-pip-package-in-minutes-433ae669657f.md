# 在几分钟内将您的 Python 代码转换成 pip 包

> 原文：<https://levelup.gitconnected.com/turn-your-python-code-into-a-pip-package-in-minutes-433ae669657f>

![](img/0301bdc2e618eede7346e83732552730.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/photos/RWTUrJf7I5w) 上的照片

# 为什么您应该打包您的代码

Python 最大的优势之一是用户能够打包他们的代码并发布给其他 Python 爱好者在他们的工作流程中使用。如果没有像 pandas、numpy 和 matplotlib 这样的库，Python 就不会是我们都喜欢的非常灵活的语言。

你不需要创建一个像 numpy 那样庞大的包来让社区中的其他用户受益。即使非常特殊的工作流通常也有一组用户在处理类似的问题。将您的软件打包供他人使用可以节省时间，并让其他开发人员参与您的工作，通过协作来帮助改进您的代码。我发表了我的第一个 Python 库，同时使用被动微波数据进行大规模积雪分析，听起来很合适，对吗？仅仅几个月的时间，它的下载量就超过了 5000 次，现在仅康达-福吉一家就有将近 20000 次下载。我希望它已经帮助一些地球科学家加快了他们的工作流程，并且我知道它帮助我更好地理解了我自己的代码。

# 入门指南

## 选择软件包安装程序:pip 或 conda

Pip 是 python 中默认的包安装程序。它代表 pip 安装包。Conda 是一个环境管理器，旨在通过增加功能取代 pip 的位置。Conda 来自 Anaconda 发行版，必须单独安装，而 pip 默认情况下随所有 python 安装一起提供。

如果您已经安装了 python 的一个实例，并且您只想安装公共包，那么 pip 是一个不错的选择。但是在管理和跟踪外部依赖性以及访问所有可用的 python 包方面，pip 确实有所欠缺。

> **注意**:外部依赖是 python 包依赖的代码，它本身不是用 python 写的。这对 pip 来说可能是个问题，因为 pip 中构建的所有东西都有一个与之关联的`setup.py`文件。但是如果你有一个 C 脚本作为依赖，它的源代码没有 setup.py 文件。这就是 conda 派上用场的地方，它使得管理这些依赖关系变得更加容易。

## 从 pip 开始，然后发布到 conda

构建两个版本，或者维护两个版本，或者选择一个最适合您的包，都没有错。我个人在 pip 和 conda (conda-forge)上都发布了我的库，但是我更喜欢 conda，因为使用 pip 安装 GDAL 会很痛苦。

这篇文章将着重于把你的包发布到 PyPI 上进行 pip 下载，因为这是标准。我的下一篇文章将关注 conda，以及为什么 conda-forge 是我最喜欢的发布库的地方。

# 初始步骤

1.  将代码组织到适当的文件层次结构中
2.  添加您的`__init__.py`文件
3.  如果还没有完成，添加一个`license`和一个`README.md`

> 注:任何时候都可以参考我的例子 [Github Repo](https://github.com/wino6687/pip_conda_demo) 来看文件结构

## 文件层次结构和 __init__ 文件

```
/demopackage
    __init__.py
    demopackage.py
    /demosubpackage
      __init__.py
      demosubpackage.py
    /tests
        test_package.py
LICENSE
setup.py
```

无论何时您想要创建一个 python 模块，无论是本地导入还是构建一个 python 库，最好将它放在自己的文件夹中，并带有一个`__init__.py`文件，该文件只包含:

```
name = 'demopackage'
```

这个文件允许 Python 容易地识别模块和子模块，以便可以导入它们。

> **注意**:你的主模块的名字会是你的 Python 库的名字，所以选你喜欢的吧！

## 添加许可证

您发布的任何源代码都必须附有许可证，即使您希望它完全开源。如果你的代码没有附带许可证，从技术上讲，任何人使用它都是不合法的，因为默认情况下，所有创造性的作品都有专属版权。

[这一页](https://docs.python-guide.org/writing/license/)来自 Python 的搭便车指南，很好地向您介绍了可用的许可证，并链接了一些工具来帮助您选择。

大多数 python 库使用 MIT 或 BSD 许可证，这是非常宽松的开源许可证。更宽松的环境使得各种各样的潜在用户更容易使用，尤其是工业用户。请记住，GPL 许可证是“病毒性的”,这意味着如果您合并使用 GPL 许可证的源代码，您的代码也必须有更严格的 GPL 许可证。

我最常从研究伙伴那里听到的关于使用更严格的许可证(如 GPLv3)的论点是，他们可以确保你的代码始终保持自由，永远不会被合并到私有代码库中。

# 将您的包发布到 PyPI

PyPI 是 Python 包索引。它是可以通过 pip 安装的 python 包的存储库。完成这四个简单的步骤后，您将拥有自己的 Python 库！

1.  [注册 PyPI](https://pypi.org/account/register/) 账户
2.  创建你的`setup.py`文件
3.  生成分发存档文件
4.  上传！

## 创建您的`setup.py`文件

您的`setup.py` 文件是您的`setuptools` 的构建脚本，它是实际打包您的代码*的库。*您的`setup.py`文件本质上保存了关于您的包的最基本的信息，并且 pip 可以在那里找到源代码。每次更新你的包，在上传到 PyPI 之前，你会改变你的`setup.py` 中的版本号。

下面是一个`setup.py`文件的例子:

```
import setuptools

with open("README.md", "r") as fh:
    long_description = fh.read()

setuptools.setup(
    name="demopackage",
    version="1.0.0",
    author="demo_author",
    author_email="demo@email.com",
    description="A small demo package",
    long_description=long_description,
    long_description_content_type="text/markdown",
    url="https://github.com/username/demopackage",
    packages=setuptools.find_packages(),
    classifiers=(
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ),
)
```

> 注意:关于 setup.py 文件中可能使用的分类器的完整列表，请参考 PyPI 文档中的[链接](https://pypi.org/pypi?%3Aaction=list_classifiers)。

## 创建您的分发归档文件

在开始之前，运行以下命令来更新所需的软件包:

```
 python3 -m pip install --user --upgrade setuptools wheel 
```

确保您位于与您的`setup.py`文件相同的目录中(项目的主目录),然后运行:

```
python3 setup.py sdist bdist_wheel
```

这将在您的主目录中创建一个`dist/`文件夹，其中包含您的包的压缩文件！

## 将您的发行版档案上传到 PyPI

检查是否安装了捆绳:

```
python3 -m pip install --user --upgrade twine 
```

Twine 只是管理上传到 PyPi 的文件。接下来运行此命令:

```
twine upload dist/* 
```

当您没有为 twine 上传您的发行版档案传递一个位置时，它将默认为 PyPI 遗留服务器，这正是我们想要的！

系统会提示您输入 PyPI 登录凭证，然后开始上传。现在你应该能够登录到你的 PyPI 账户，你会看到你的包。请注意，PyPI 在包的主页上显示了您的自述文件，因此请利用这个空间提供关于您的库的有用信息。

## 测试您的新包

等待 5-10 分钟，让 pip 注册您上传的软件包。有时候可以马上安装，其他时候需要几分钟。

```
pip install demopackage
```

如果您还有其他问题，[这里是制作 pip 可安装代码的 PyPi 指南](https://packaging.python.org/tutorials/packaging-projects/)

# 维护 pip 包

每当您对代码进行改进时，您都会想要将新的发行版文件重新上传到 PyPI。

## 关键步骤:

1.  更改 setup.py 文件中的版本号( [bump2version](https://github.com/c4urself/bump2version) 使版本管理更加简单)
2.  删除旧的分发文件
3.  在你的包的主目录下运行:`python3 setup.py sdist bdist_wheel`来创建新的分发档案
4.  将这个新版本上传到 PyPI: `twine upload dist/*`

我写了一篇文章详细讨论了你可以用来帮助自动化这个工作流程的工具。当您更新版本号并避免这些手动步骤时，您可以利用持续集成来自动部署您的包的新版本。

# 包扎

恭喜你！现在，您拥有了自己的 Python 库，任何人都可以在工作流程中安装和使用它。在这篇文章中，我只讲述了将你的包发布到 PyPI 的细节，但是在我的下一篇文章中，我将详细讲述将它发布到 conda(包括 conda-forge)的细节。

## 后续步骤

1.  为您的软件包编写文档，以便人们知道如何使用它。它可以像你的自述文件中的例子一样简单，也可以像定制的[阅读文档](https://readthedocs.org/)页面一样复杂。有时间做什么就从什么开始比较好；没有使用说明的软件包没有多大用处！
2.  [遵循我的指南](/tools-for-writing-effective-and-robust-python-libraries-28bfd4c018c1)，使用单元测试、持续集成和林挺等工具，使你的包更加健壮，以简化长期维护。
3.  将您的包发布到 conda，并最终发布到 conda-forge 频道，我将在以后的帖子中对此进行介绍。

## 文档链接

*   [PyPI 指南](https://packaging.python.org/tutorials/packaging-projects/)
*   示例 [Github Repo](https://github.com/wino6687/pip_conda_demo)
*   [阅读文档](https://readthedocs.org/)
*   [bump 2 版本](https://github.com/c4urself/bump2version)