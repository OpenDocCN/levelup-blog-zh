# 如何将 python 命令行应用程序发布到 pip (PyPI)

> 原文：<https://levelup.gitconnected.com/how-to-publish-a-python-command-line-application-to-pypi-5b97a6d586f1>

![](img/092bdae8211ea7902b36c381a9d1b6e1.png)

在花了一些时间开发一个 cli 项目后，您想在 pip (PyPI)上发布它。毕竟，哪个安装程序能胜过简单的 *pip 安装*？在互联网上搜索了一番后，你可能已经看到了[关于这个话题的唯一一个帖子，我找到了](https://gehrcke.de/2014/02/distributing-a-python-command-line-application/)，它来自 2014 年，要求你创建一堆文件夹和文件来做一些并不真正需要的事情。

这篇博文可能也会让你想知道，在 2019 年，是否有更好的方式来做事情，而不需要那么多样板文件。我也是这么想的，所以，在阅读了大量关于包装的文档后，我在这里浓缩了这些信息，这样你就不需要再去读了。以下是逐步说明:

1.  在 PyPI ( [注册链接](https://pypi.org/account/register/))上创建账户
2.  创建应用程序的入口点(将以下代码放入 *entry.py* )

```
def main():
    print("It's alive!")
```

3.装诗

```
curl -sSL https://raw.githubusercontent.com/sdispater/poetry/master/get-poetry.py | python
source $HOME/.poetry/env
```

4.设置诗歌

```
cd myproject #Navigate to your project's directory
poetry init
```

5.设置 cli 命令(将以下行添加到 *pyproject.toml* )

```
[tool.poetry.scripts]
APPLICATION-NAME = 'entry:main'
```

您可以将*应用程序名称*替换为您希望的任何主 cli 命令。

6.发布吧！(替换为您在步骤 1 中使用的用户名和密码)

```
poetry publish --username PYPI_USERNAME --password PYPI_PASS --build
```

它还活着！现在，您的用户只需下面两行代码就可以安装和使用您的应用程序:

```
$ sudo pip install PROJECT-NAME
$ APPLICATION-NAME
```

其中*项目名称*是您在步骤 4(诗歌初始化)中为项目指定的名称，而*应用程序名称*是步骤 5 中的 cli 命令名称。

## **更新**

每当你想更新你的包时，只需改变 *pyproject.toml，*中的版本号，它在下面的*行中定义:*

```
version = "0.1.0"
```

并重新运行步骤 6:

```
poetry publish --username PYPI_USERNAME --password PYPI_PASS --build
```

## 额外收获:Travis 为自动出版而构建

将以下几行添加到您的 *.travis.yml:*

```
language: python
dist: xenialbefore_install:
  - curl -sSL [https://raw.githubusercontent.com/sdispater/poetry/master/get-poetry.py](https://raw.githubusercontent.com/sdispater/poetry/master/get-poetry.py) | python
  - source $HOME/.poetry/envinstall:
  - poetry installscript:
  - poetry builddeploy:
  - provider: script
    skip_cleanup: true
    script: poetry publish --username $PYPI_USER --password $PYPI_PASS
    on:
      branch: master
      python: '3.7'
      tags: true
```

并将 PYPI_USER 和 PYPI_PASS 设置为[travis-ci.com](https://travis-ci.com/)中的 travis 环境变量。之后，您可以使用以下命令发布您的包:

```
git tag -a v1.2 # Replace version number with yours
git push --tags
```