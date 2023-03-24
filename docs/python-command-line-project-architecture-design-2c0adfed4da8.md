# Python 命令行项目—架构设计

> 原文：<https://levelup.gitconnected.com/python-command-line-project-architecture-design-2c0adfed4da8>

![](img/abd198f920f18121d3d9e83c077b253d.png)

[兰斯·安德森](https://unsplash.com/@lanceanderson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

用 Python 设计你的命令行应用程序通常会很棘手，因为我们没有像 Java 或 Ruby 那样严格的架构设计概念。

在这里，我们将讨论一种可能的项目结构，它可以帮助您构建一个定义良好、组织有序的命令行实用程序。

# 骨骼

首先，让我们在父目录**根目录**下创建一个事实上的 python 项目存储库。

```
/root
├── /app
├── .gitignore
├── MANIFEST.in
├── Dockerfile
├── LICENSE.txt
├── README.md
└── setup.py
```

我们添加一个名为 ***app*** *的目录。这个目录将保存我们所有的代码。我们将更进一步，把我们的代码放在不同的子目录下，我们将构建一个 Python 模块方法。*

*   一个目录包含所有公开的代码
*   一个目录包含所有实用程序、助手方法
*   一个包含所有配置的目录
*   一个包含所有模型类的目录(*我们将在后面讨论*)

## / **app** 目录

```
root
├── /app
│   ├── /bin
│   │   └── /resources
│   ├── /lib
│   │   └── /resources
│   └── /config
```

这里我们添加了 ***app/bin，*** 将托管所有脚本和方法。当我们运行 python 程序时会调用这个目录。我们可能**不**想让我们的业务逻辑与这部分分开——因为这可能成为我们的主要活动场所。这也可能包含参数解析、条件逻辑和退出策略。

我们添加了 ***app/lib*** ，它将托管所有不直接作为业务成果的脚本，但有助于开发人员在整个项目中重用。这些文件可以是数据库连接器、*记录器*、 *sftp* 实用程序等。

## 非 python 资源

在上面的结构中，我们添加了 ***resources*** 目录，该目录将只保存非 python 文件，这些文件可能会在其父目录中的某些代码中使用。我们不想添加一个 *__init__。py* 在这里，因为这个地方不会有任何 python 代码。然而，我们需要告诉我们的构建工具包含这些目录。我们可以将它们添加到 ***MANIFEST.in*** 文件中。

```
# MANIFEST.ininclude app/bin/resources/*
include app/lib/resources/*
```

现在，每当我们构建一个项目的发行版时， ***resources*** 目录中的项目将被原样包含。

# 参数解析

对于参数解析，我们需要做的第一件事是确定我们的入口点。这可以使用我们的 *setup.py* 来完成。下面的官方文件可以帮助你。

[编写安装脚本—docs.python.org](https://docs.python.org/3/distutils/setupscript.html)

现在假设文件 *app/bin/app.py* 是你的一个入口点。我们继续写一些像下面这样的行。

```
# MANIFEST.in*import* argparse
..
..
Your code
..
.. def main():
  parser = argparse.ArgumentParser(description='Arguments being passed to the program')
  parser.add_argument('--partner', '-p', required=False, default='paypal', help='Partner name')
  args = parser.parse_args()
  print(f'partner is {args.partner.lower()}')*if* __name__ == "__main__":
  main()
```

正如你在上面看到的，我们保留了 *main()* 中的所有内容，并没有过多使用*_ _ name _ _ = _ _ main _ _*部分。这里有两个好处:

*   事情保持清晰和紧凑
*   代码行为没有歧义，无论它是作为 python 包运行还是独立运行。

但是，如果你有以下情况，

*   你的项目会越来越大
*   你会有很多切入点
*   你的程序有复杂的参数

在这些情况下，拥有一个专用于参数解析的单独文件是一种更合适的方式。它给了你在任何需要的地方调用模块的灵活性。然后你必须想出你的策略来在项目间共享你的参数变量，也许你可以考虑将它们设置为一个*环境变量*。

# 模型和用户类别

在用一堆 Python 模型类进行面向对象编程时，您可以选择两条路径中的任何一条。

*   创建一个目录 ***app/models*** 把你的文件放在那里。此外，您可以通过创建更多的嵌套目录来打破它。当你有很多这样的类时，这是很有帮助的。
*   放在 ***app/lib*** 目录下。正如我们之前所讨论的，该模块设计用于*这样的原因*仅*。*当你有几个类并且已经正确应用了 OOP 概念的时候，这可能是有帮助的。

# 使用 __init__。巴拉圭

我们常常低估了 *__init__。py* 文件。这个文件不仅初始化 python 中的模块，还充当该模块的入口点。我们可以把任何预处理的东西放在这个文件下。

让我们假设我们有 3 个类写在 3 个文件中，它们位于 ***app/lib*** *下。*

```
# app/lib/myclass1.pyclass MyCustomClass1(object):
..
..# app/lib/myclass2.pyclass MyCustomClass2(object):
..
..# app/lib/mylogger.pyclass Logger(object):
..
..
```

现在，为了使用/导入任何类，用户不仅需要知道它在 ***app/lib*** 中，还需要知道要查找哪个文件。相反，我们可以将所有这样的引用放在 *__init__.py.*

```
# __init__.pyfrom myclass1 import MyCustomClass1
from myclass2 import MyCustomClass2
from mylogger import Logger
```

现在用户可以从 ***app/lib*** 中导入需要的类目录，不需要再去找了。

```
# script1.pyfrom app.lib import Logger, MyCustomClass1, MyCustomClass2
```

# 非 Pyhton 人工制品

## 键

我们有时需要在代码中使用各种公钥。如果是这种情况，您可以在**根**级别创建一个目录作为 ***密钥*** 并将所有公钥保存在该目录下。

> 将私钥放在存储库中会带来威胁；你应该想出自己处理这种情况的方法。

```
/root
├── /app
├── **/keys**
├── .gitignore
├── MANIFEST.in
├── Dockerfile
├── LICENSE.txt
├── README.md
└── setup.py
```

> 请确保在 MANIFEST.in 文件中包含该目录。

## 日志文件

我希望你基本上会使用任何 **logger** 框架来记录对你重要的事情。通常，我们将日志捕获到文件中，并将其打印到标准输出中。在这种情况下，我们有两条路可走。

*   您有带循环的时间戳日志文件。他们很快就会成为一群。在根目录下创建一个名为 ***logs*** 的目录，在这里保存你的时间戳日志文件。
*   每次应用程序运行时，您都有一个重新创建的日志文件。将文件目录放在 ***根目录下*** 。

在这个 [GitHub 库](https://github.com/arghajit/init-python)中找到代码。

目前就这些。我希望这有所帮助。让我们知道你在遵循什么样的架构设计。

编码快乐！