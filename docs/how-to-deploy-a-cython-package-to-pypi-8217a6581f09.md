# 如何将 Cython 包部署到 PyPI

> 原文：<https://levelup.gitconnected.com/how-to-deploy-a-cython-package-to-pypi-8217a6581f09>

![](img/9f429e605ba8438e9b1a0814803c60bc.png)

我发现这实际上很难，所以我想会有其他人也会发现这一点。

# 项目结构

这是我们将在这个项目中使用的文件结构:

```
.
├── classification_library
│   ├── data
│   ├── __init__.pxd
│   ├── __init__.pyx
│   └── test_classification_library.py
├── MANIFEST.in
├── pyproject.toml
├── README.md
└── setup.py
```

# 安装脚本

Python 包的设置脚本是`setup.py,`，打包工具使用它来知道如何处理代码。

首先，从 setuptools 导入所需的函数。

```
from setuptools import find_packages, setup
```

既然你正在制作一个 Cython 包，我假设你知道`setup`是做什么的。如果你以前没有使用过`find_packages`，它确实如你所料，当你看到对`setup`函数的调用时，我们为什么需要它就变得非常明显了。

然而，在我们调用`setup`之前，我们必须再导入一些东西。

```
from Cython.Build import cythonize
import numpy as np
```

我们需要`cythonize`，因为它将 Cython 代码转换成 C，我需要 numpy，因为我的 Cython 生成的 C 代码依赖于它。

我们还需要加载自述文件，以便它可以显示在您的软件包的 PyPI 页面上。你可以用这段代码或者类似的代码来实现:

```
with open("README.md", 'r') as f:
    long_description = f.read()
```

当您添加`setup`调用时，文件看起来是这样的:

`setup`调用的重要部分是最后三个参数。`ext_modules`指定包的 C 扩展，`include_dirs`需要被指定以便依赖于 numpy 的 C 扩展可以被编译，`install_requires`指定`classification_library`依赖的包。

可以使用`setuptools.Extension`代替`Cython.Build.cythonize`来生成和编译 C 扩展，但是仍然需要安装 Cython。

# 指定生成依赖项

你可能一直在担心在`setup.py`导入 numpy 和 Cython。如果你没有，这里是为什么它是一个问题:

当你使用 pip 安装某个东西时，它会临时下载`setup.py`并在上面运行某些命令(比如`python3 setup.py <command>`)，这样正确的文件就会出现在你的`sys.path`中的某个地方，这样你就可以导入它们了。除非 pip 安装您的软件包的人碰巧已经安装了 Cython 和 numpy，否则这些导入语句将会抛出错误。因此，您必须在`pyproject.toml`中指定它们。这使得 pip 将临时安装安装您的软件包所需的软件包。下面是我的`pyproject.toml`的样子:

```
[build-system]
requires = ["setuptools", "wheel", "numpy>=1.19.0", "Cython>=0.29.21"]
build-backend = "setuptools.build_meta"
```

最后一行基本上是告诉包管理器使用`setuptools.build_meta`，以便知道如何处理该部分中指定的其他信息。

除了指定构建依赖关系之外，还可以做更多的事情，但这是另一篇博客文章要讨论的问题。

# 包括非 Python 文件

拥有一个`MANIFEST.in`文件很重要，因为它指定了包含哪些非 python 文件，否则打包工具会忽略这些文件。您不必包含`.pyx`文件，因为这些文件会被编译成`.so`文件，后者会被检测为 python 文件。但是，`.pxd`文件如果不包含将被忽略。所以，我的`MANIFEST.in`长这样:

```
include classification_library/__init__.pxd
include classification_library/data
```

# 生成源档案和 Wheel 文件

如果您尚未安装 wheel，请安装:

```
pip install wheel
```

现在您可以运行该命令:

```
python3 setup.py sdist bdist_wheel
```

您应该看到一个名为`dist`的目录，其中包含以`.tar.gz`和`.whl`结尾的文件。如果你在 Linux 上，你的 wheel 文件以`cp38-linux_x86_64.whl`结尾，那么你必须做一些额外的工作。

由于技术原因，PyPI 不支持上传这种车轮文件。要将其更改为正确的格式，您必须安装 auditwheel:

```
pip install auditwheel
```

然后，您可以运行命令:

```
auditwheel repair dist/<your_package_name>-cp38-cp38-linux_86_64.whl
```

然后将 auditwheel 生成的文件移动到`dist`目录中，并删除旧的 wheels。

```
mv wheelhouse/* dist
rm dist/*-cp38-cp38-linux_x86_64.whl
```

# 上传到 PyPI

将文件上传到 PyPI 是由一个名为 twine 的包处理的。

我相信你已经明白了这一点，但如果你还没有，这里有如何安装 twine:

```
pip install twine
```

您还必须[创建一个 PyPI 帐户](https://pypi.org/account/register/)。

你*可以在每次上传包的时候*指定你的用户名和密码，或者你可以把它们放在一个`~/.pypirc`文件里，就像这样:

```
[pypi]
username = <your_username>
password = <your_password>
```

如果你想更安全，你可以从你的帐户设置中获得一个 API 令牌。你的账户设置页面上有相关说明。

最后，您可以使用以下命令上传到 PyPI:

```
python3 -m twine upload --repository pypi dist/*
```

如果一切顺利，你应该可以在终端上输入这个，你的包就会被安装。

```
pip install <your_cython_package>
```

您必须增加您的`setup.py`中的版本号，重新生成源发行版和 wheel 文件，并在每次更新您的包时使用 twine 重新上传。

我有这个问题，我部署的一些软件包必须安装`pip install git+https://github.com/lol-cubes/<package-name>`。如果您知道为什么会出现这种情况和/或如何解决，请告诉我。非常感谢。

非常感谢您的阅读！祝你在 Cython 冒险中好运！