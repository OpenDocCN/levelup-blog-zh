# 虚拟环境中使用类、对象、方法和导入的 Hello World

> 原文：<https://levelup.gitconnected.com/hello-world-in-a-virtual-environment-using-classes-objects-methods-and-imports-5214087c0c72>

超越 Hello World，展示重要的 Python 基础知识

![](img/ea18a8ce1e338d29a59e0915603e017f.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 拍摄的照片

Python 具有组织程序的能力，因此易于导航和阅读。您不希望所有代码都在一个文件中。你也不希望你所有的功能都漫无目的地分散。通过使用类，我们可以设置方法，这样您需要的函数只能通过我们从类中设置的命名对象来使用。

把一个类想象成一个工具如何工作的描述和一组指令。我们从该类创建的对象成为我们的活动工具。我们可以在单独的 Python 文件中构建我们的类，这样我们就可以在需要构建这个工具的时候导入我们的类。

# 你好世界

大多数人在开始学习任何编程语言时首先要学习的是 Hello World 脚本。在 Python 中，它是这样表示的:

```
print(“Hello World”)
```

这是一个很好的介绍，因为它能马上产生效果。然后，我们可以使用相同的脚本开始讨论变量。这里我们创建一个字符串变量，并使用 Python 3 的 f-string 格式打印它。

```
hello_world_string = “Hello World”print(f”{hello_world_string}”)
```

然后我们可以继续讨论函数。

```
def hello_world_function(): hello_world_string = "Hello World"
    print(f"{hello_world_string}")hello_world_function()
```

通过将括号留空，我们不需要传入任何内容。我们可以运行这个函数。它需要和做的一切都在函数内部。

```
hello_world_string = “Hello World”def hello_world_function(text_to_print): print(f"{text_to_print}")hello_world_function(hello_world_string)
```

在这个例子中，我们在函数之外定义变量。然后，我们在函数中为一个新变量提供一个名字，我们将传递给它。我们将*hello _ world _ string*传递到 *hello_world_function* 中，然后变成 *text_to_print* ，我们用它作为函数变量来打印我们的消息。

# Python 类和方法

现在我们要把这个函数放到一个类里，让它成为一个方法。我们还将为我们的类创建一个单独的文件，这样我们就可以将它导入到我们的主脚本中。继续创建两个文件:main.py 和 messages.py

我们将从 messages.py 开始，创建 HelloWorld 类，并在其中放置一个打印 Hello World 的方法。

```
# messages.py class HelloWorld:

    def __init__(self, message):
        self.message = message def hello_world_method(self):
        print(f"{self.message}")
```

我们使用 *self* 来传递使用 *__init__* 初始化的所有内容，这意味着我们可以通过使用 *self.message.* 来访问*消息*

现在我们将我们的类导入 main.py，创建一个字符串变量和一个运行我们方法的对象。

```
# main.py from messages import HelloWorldhello_world_string = "Hello World"
hello_world_object = HelloWorld(hello_world_string)hello_world_object.hello_world_method()
```

如果您运行您的程序，您应该看到 Hello World 打印到您的终端。如果你得到一个语法错误，可能是因为你的系统默认为 Python 2，不支持 f 字符串。使用 *Python3 main.py* 代替。

我们也可以通过使用同一个对象直接访问我们的字符串变量。在 *main.py* 的底部添加这一行。

```
print(f”{hello_world_object.message}”)
```

现在，当您运行您的脚本时，您会看到 Hello World 打印了两次。

# 虚拟环境

使用 Python 时需要学习的另一个重要内容是虚拟环境。它们很容易设置，并且会使事情变得更容易，因为你需要的关于 Python 和库的所有东西都包含在同一个文件夹中。您不是从系统中全局提取，而是从文件夹中本地提取。

打开你的终端，确保你在当前有 *main.py* 和 *messages.py.* 的同一个文件夹里

```
$ python3 -m venv virtual_environment
```

你可以把*虚拟环境*改成你想要的任何东西。这将是 python 和所有库所在的文件夹。运行之后，我们需要激活新创建的虚拟环境。

**视窗:**

```
$ virtual_environment\Scripts\activate.bat
```

**Linux 还是 MacOS:**

```
$ source virtual_environment/bin/activate
```

命令行应该会改变，看起来像这样:

```
(virtual_environment) user@computer: ~/hello_world $
```

如果你使用的是类似 Visual Studio 代码的 IDE，它可能会自动识别你的虚拟环境，并询问你是否想在当前项目中使用它。它会在你每次打开项目时为你激活它。

现在，当您使用 pip 安装任何包时，它们将安装在 python 文件夹中，该文件夹现在位于 *virtual_environment 中。*这是许多使用 Python 的公司的常见做法，所以即使你只是浏览教程，养成这种习惯也是一个好习惯。

# 饭桶

另一个值得遵循的习惯是使用 git，因为它将是你最终从事的任何编程工作中非常重要的一部分。如果你还没有安装 git，继续在 google.com 上搜索它，并根据你使用的操作系统按照说明进行操作。

我们将使用 GitHub 创建一个存储库。如果你还没有一个帐户，那就去 github.com 注册吧。登录后，单击右上角的加号按钮，然后单击 add repository。我已经给我的 *hello_world* 打了电话，我在里面放了一个简短的描述。不要初始化它询问的三件事情中的任何一件。单击创建存储库。

回到您的终端窗口，我们可以初始化我们正在工作的文件夹，并在最终提交文件之前添加文件。首先你需要配置 git，让它知道你是谁。

```
$ git config --global user.name "<NAME>"
$ git config --global user.email "<GITHUB EMAIL ADDRESS>"
$ git config --global user.password "<GITHUB PASSWORD>"$ git init
$ git add .
$ git commit -m "First commit for hello_world."
```

一切都准备好了，可以把代码推送到我们的仓库了，但是我们需要知道我们正在推送到哪里，所以回到你在 GitHub 的仓库。在它下面写着快速设置的地方，你可以通过点击链接右边的小剪贴板图标来复制 HTTPS 链接。

回到你的终端窗口。

```
$ git remote add origin <HTTPS URL>
$ git push origin master
```

Visual Studio 代码可能会打开一个浏览器，让您登录 GitHub。否则，您可以从终端窗口做任何事情。如果你回到你在 GitHub 的仓库，你会看到你的项目文件现在是可见的。我建议深入研究 git，因为可以用它做很多事情。github.com 有很多指南。

# 结论

使用像 Hello World 这样的简单脚本是深入了解 Python 功能的好方法。除了教程、指南和文档，我学习代码的一个方法是问自己下一步想对现有代码做什么。然后开始研究需要什么，并尝试去实现它。我失败了很多次，但是通过排查和解决自己的错误和缺陷，我获得了更多的经验。这比教程更能激励我，因为我实际上是在创建自己的代码。

非常感谢你的阅读！如果任何人有任何问题或意见，请随时留下您的回复，或者您可以直接通过 withinmyself@gmail.com 联系我