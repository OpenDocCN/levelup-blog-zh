# 烧瓶+ Docker:快速提示

> 原文：<https://levelup.gitconnected.com/flask-docker-quick-tips-285e4e360f2>

Flask 是一个众所周知的 Python 微框架。有人会说 Python 是一门容易学习的语言，我会说也许是。这取决于你想做什么。

![](img/52277c29cd444a34a3167e34fc72b775.png)

在 [Unsplash](https://unsplash.com/s/photos/flask?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由[路易斯·里德](https://unsplash.com/@_louisreed?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片

我不会说 Python 对于初学者来说很容易调试。如果您不熟悉一些基本概念，尤其是来自*nix 领域的概念，那么设置起来也不容易。

我将在这里给**带来一些快速提示，帮助初学者**申请[烧瓶](https://flask.palletsprojects.com/en/1.1.x/)的过程。

## 为什么是烧瓶？

[Flask](https://flask.palletsprojects.com/en/1.1.x/) 是一个用 Python 编写的微框架，极其灵活，占用空间小，轻量级。

> **在我们开始**之前——这里有一个很大的免责声明:
> 有一百万种方法可以将 Flask 应用程序引入 Docker。**这只是其中之一**。请随时带着您的解决方案过来与我们分享。也就是说，我们走吧！

# 1.选择基础图像

如果你是初学者，我的建议是:坚持使用 Ubuntu。更容易找到资源和如何的。在 Ubuntu 上取得成功后，你可以尝试其他风格，比如 Alpine。

```
FROM ubuntu:18.04
```

# 2.更新您的信箱

在开始之前，最好运行一个 apt 更新和 apt 安装。注意让所有东西都在同一个 RUN 语句中。它会让你远离由于过时的库而导致的错误和奇怪的场景。

```
RUN apt-get update -y && \
    apt-get install -y python3-pip python3-dev python3-venv
```

# 3.安装 requirements.txt

将 requirements.txt 发送到容器中，然后运行 PIP

```
COPY ./requirements.txt /app/requirements.txtRUN pip3 install --upgrade pip
RUN pip3 install -r requirements.txt
```

# 4.复制应用程序文件

```
COPY ./app /app/
```

# 5.对. sh 引导文件的权限被拒绝

在终端中运行“ls -la”会告诉您该文件是否具有正确的权限。如果没有，请记住在构建映像时进行设置，如下所示:

```
RUN chmod +x ./boot.sh
```

我强烈建议在这里使用正确的权限，而不是作为根用户运行，但是我想在开发阶段这是完全可以接受的。**有时会发生权限冲突，你必须对你机器上的文件应用相同的权限。**

# 6.暴露端口

```
EXPOSE 5000
```

# 7.定义入口点

```
ENTRYPOINT [ "./boot.sh" ]
```

# **8。应用程序正在运行，但浏览器没有显示任何内容**

发生这种情况是因为您还看不到服务器。请记住将应用程序绑定到 0.0.0.0 地址，以便从容器外部访问它。

**启动应用程序时，这可以像下面这样轻松完成:**

```
flask run --host 0.0.0.0
```

# 9.烧瓶自动再装

每当你改变一个文件时，停止容器然后再启动它是很烦人的。嗯，你真的不需要这样做！

在启动应用程序之前，将您的 ENV 导出为开发:

```
export FLASK_ENV=development
```

# 10.字符和编码问题

将你的应用默认编码设置为 UTF-8:

```
export LC_ALL=C.UTF-8
export LANG=C.UTF-8
```

# 入口点文件

**在这种情况下，整个 boot.sh 将是:**

```
**#!/bin/bash**
export LC_ALL=C.UTF-8
export LANG=C.UTF-8
export FLASK_APP=app.py
export FLASK_ENV=developmentflask run --host 0.0.0.0
```

**对完整的 Dockerfile 好奇？就是这里:**

```
FROM ubuntu:18.04

RUN apt-get update -y && \
    apt-get install -y python3-pip python3-dev python3-venv

COPY ./requirements.txt /app/requirements.txt

RUN pip3 install --upgrade pip && \
    pip3 install -r requirements.txt

COPY ./app /app/
RUN chmod +x ./boot.sh
EXPOSE 5000
ENTRYPOINT [ "./boot.sh" ]
```

就这些了，希望对你有帮助！
快乐编码用[烧瓶](https://flask.palletsprojects.com/en/1.1.x/)。

[![](img/62095e5d34c9f0d8bd36b088feb508a9.png)](https://www.buymeacoffee.com/gabrielguerra)