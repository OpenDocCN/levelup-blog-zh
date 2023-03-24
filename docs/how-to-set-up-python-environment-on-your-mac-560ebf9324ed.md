# 如何在 Mac 上设置 Python 环境

> 原文：<https://levelup.gitconnected.com/how-to-set-up-python-environment-on-your-mac-560ebf9324ed>

![](img/a5f7ec188f76651b79c8c7564b1c1438.png)

照片由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Python 是机器学习和数据分析最流行的编程语言。本文展示了如何在 Mac 上设置 Python 环境。

## 1.安装 pyenv

Pyenv 是一个强大的 Python 版本管理工具。您可以轻松地更新正在使用的 Python 版本和库

Pyenv 可以通过[家酿](https://brew.sh)安装。没有的话先装这个。

现在让我们安装`pyenv`

```
$ brew update
$ brew install pyenv
```

您需要在 bash 配置文件中添加一些初始化脚本。

```
$ echo ‘export PYENV_ROOT=”$HOME/.pyenv”’ >> ~/.bash_profile
$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
$ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bash_profile
```

## 2.安装 pyenv-virtualenv

Pyenv-virtualenv 是一个管理`pyenv`上 Python 虚拟环境的插件。

Virtualenv 是一个创建独立 Python 环境的工具。我们可以通过目录来分离 Python 环境。

`pyenv-virtualenv`也可以自制安装。

```
$ brew install pyenv-virtualenv
```

并在 bash 配置文件中添加初始化脚本。

```
$ echo ‘eval “$(pyenv virtualenv-init -)”’ >> ~/.bash_profile
```

现在重启终端，你就可以按目录创建你的 Python 环境了。

## 创建 Python 环境

首先选择您使用的 Python 版本。如果不在乎版本，我推荐使用最新版本。

可以查看`pyenv`可以安装的版本和发行版。

```
$ pyenv install --list
```

安装 Python

```
$ pyenv install 3.7.3
```

创造`virtualenv`

```
$ pyenv virtualenv 3.7.3 your-virtual-env-name
```

移动到项目目录，并设置本地 python 环境

```
$ cd path-to-your-direcotry
$ pyenv local your-virtual-env-name
```

`pyenv local`命令将创建`.python-version file`。它指定要在目录中使用的 python 环境。

现在`pyenv`自动将您的 Python 环境切换到您的`virtualenv`。

## 安装库

如果你使用 Python，你会使用像`numpy`、`pandas`或者`tensorflow`这样的库。

您可以使用 pip 安装库。Pip 是一个 python 包管理器。您可以在`virtualenv`中使用它，库安装在每个单独的环境中。

要安装`numpy`，只需使用 pip。

```
$ pip install numpy
```

非常容易！

## 导出库列表

Pip 可以将已安装的库导出到文本文件，也可以从文本文件安装库。在其他设备或像 docker 这样的容器上创建相同的环境很重要。例如，通过在 docker 中打包 python 环境，您可以在 AWS batch、AWS elastic beanstalk 或其他 docker 支持的服务上运行您的 Python 脚本。

出口

```
$ pip freeze > requirements.txt
```

导入

```
$ pip install -r requirements.txt
```

现在您已经创建了一个可移植的 python 环境！

Python 是最常用的编程语言之一。

如果你喜欢这个帖子，请关注我 [twitter/@masamichiueta](https://twitter.com/masamichiueta) 。

[](https://gitconnected.com/learn/python) [## 学习 Python -最佳 Python 教程(2019) | gitconnected

### 80 大 Python 教程-免费学习 Python。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/python)