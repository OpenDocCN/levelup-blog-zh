# 我做了一个 Python 机器人给我女朋友发“晚安”短信

> 原文：<https://levelup.gitconnected.com/i-made-a-python-bot-to-send-good-night-texts-to-my-gf-8157f7850761>

## 以下是你也可以很容易地构建它的方法。

![](img/45ddba90e87630bd38e3b62d6358da1e.png)

来源:[像素](https://www.pexels.com/photo/a-black-smartphone-with-whatsapp-2733678/)。

拥有一段正常运转的关系很难，尤其是当你是一名全职程序员的时候。你必须一直照顾她和她的需求，最重要的是，如果她粘人，你必须每天晚上发“晚安”短信。

但我会在晚饭后坐下来写代码，老实说，在那之后打开 WhatsApp 给别人发短信非常分散注意力。我的意思是，我要在 3 个不同的窗口中处理数百个方法和类。如今，WhatsApp 网络在没有互联网连接的情况下也能工作，但在 Chrome 上多加一个标签对我可怜的 RAM 来说是一大打击。

所以，我想把整个过程自动化，给自己一些解脱。是的，我知道，你会说我是个糟糕的男朋友，但我刚刚把发一条无聊的晚安短信变得很酷。我编了一个机器人来帮我做这件事！

那么，让我们看看我是怎么做的。稍后感谢我(读完这篇文章后，也许请我喝杯咖啡)。

# **背景故事:**

几个月前，我需要给我的一些同事发送一些促销信息。但是在每个人的聊天中通过复制粘贴的方式给每个人发送相同的文本是如此的繁琐和耗时。此外，为一千个人做这件事只是令人讨厌的重复。

所以，我编写了一个 python 包来实现整个过程的自动化。

是的，有其他 python 包来做类似的事情，但老实说，它们很烂。每一个软件包都会打开一个难看的 Chrome 窗口，然后开始工作。如果我最小化窗口，这个过程就会失败。我想要在后台工作的东西。

所以，我编写了 AutoWhatsPy，**软件包来自动化你的 WhatsApp** ，这个**在后台运行**(不会打开难看的窗口)。

你可以把一个任务分配给一个用这个包编写的程序，它将在后台工作。从技术上来说，它确实会打开一个浏览器窗口，但不会在前台打开。当电脑在后台运行时，您可以继续在电脑上做其他事情。加上一个无头的 firefox 浏览器窗口

另外，它完全可以安全使用。我把整个项目开源了，这样你就可以自己看看代码做了什么。这个包完全是使用 selenium 和 Firefox gechodriver 用 python 构建的。

# 安装 AutoWhatsPy

## 环境设置

AutoWhatsPy 需要 [Python 3。](https://www.python.org/downloads/) 5 (Python 3.9 将是最佳选择)或以上版本运行。我用 Python 3.5 测试过。确保您的路径中添加了 Python。在 Windows 系统中安装 Python 时，请确保选中“将 Python 3.x 添加到路径”。如果你忘记这么做了，请看[这篇教程](https://www.geeksforgeeks.org/how-to-add-python-to-windows-path/)来了解如何将 Python 添加到你的 Windows 路径中。

## 安装 Python

**用于 Windows 和 Mac**

从[这里](https://www.python.org/downloads/release/python-397/)下载 Python 3.9。使用安装程序在您的系统中安装 Python。为 macOS 下载“macOS 64 位 universal2 安装程序”。为您的 Windows 64 位系统下载“Windows x86–64 可执行安装程序”,为 Windows 32 位系统下载“Windows x86 可执行安装程序”。

**针对 Linux 的**

使用以下命令在 Linux 系统上安装 Python 3.9。

```
apt-get install python3.9
```

# 检查 pip

确保您的系统中安装了 pip。使用以下命令检查是否安装了 pip。

```
pip --version
```

如果您看到类似“pip 21.2.2”的消息，那么您的系统上已经安装了 pip。否则，按照本教程[在您的系统中安装 pip。一般来说，Python 自带一个 ensurepip 模块，可以在 Python 环境中安装 pip。](https://pip.pypa.io/en/stable/installation/)

```
python -m ensurepip --upgrade
```

## 下载包

打开您的终端/cmd 并使用 pip 命令安装它。

```
pip install autowhatspy
```

# 编程；编排

现在到了编程部分。要通过 WhatsApp 向单个/多个联系人发送单个文本或图像(或两者)消息，我们必须使用包中的 message_to_contacts 功能。这是函数和所需的参数。

```
message_to_contacts(msg, contacts, gechodriver, gechodriver_log, user_profile, image)
```

## 争论

**消息** —包含消息文本的文本文件的路径。

我创建了一个名为 goodnight.txt 的文件，其中只有一行字“晚安，宝贝”。文件的内容如下:

```
Good night, honey
```

**联系人** —包含联系人列表的文本文件的路径。

在这种情况下，它只包含她的联系人姓名。contacts.txt 文件的内容如下:

```
Bae II
```

文本文件中的姓名应与手机中保存的姓名完全一致。你肯定已经在 WhatsApp 上和他们聊过了。

**gechodriver**—gechodriver.exe 文件的路径。*下载 gechodriver.exe(*[*链接此处*](https://github.com/mozilla/geckodriver) *)并将路径作为参数发送。根据解释器的工作方式，您可以使用绝对路径或相对路径。确保 gechodriver.exe 在你正在处理的同一个项目文件夹中。或者在路径中添加 gechodriver.exe 所在的文件夹。否则，可能会发生错误。* *如果还是得到错误，将 selenium 降级到 2.53.6 版本。*

**gechodriver_log** —保存 gechodriver 日志的文本文件路径。*确保 gechodriver.log 位于您正在处理的同一个项目文件夹中。否则，可能会发生错误。*

**user_profile** —保存的 firefox 用户配置文件的路径。

*   打开火狐。转到“关于:个人资料”并创建新的个人资料。
*   用 firefox 分配给它的任何名字保存在你的项目目录中。
*   打开个人资料，打开 web.whatsapp.com，扫描二维码。
*   将配置文件的路径作为参数发送给函数。
*   图像—(可选)要发送的图像/视频的路径。请确保它受 WhatsApp 支持，并且长度是允许的。

在我给你看代码之前，我有一个小小的请求。请成为灵媒的一员。只要每月 5 美元，你就可以阅读 Medium 上的任何文章(不仅仅是我的文章，任何文章)。单击下面的链接。如果你从我的推荐链接加入，我会得到一点佣金，这对我支付账单很有帮助。

[](https://samratduttaofficial.medium.com/membership) [## 通过我的推荐链接加入媒体

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

samratduttaofficial.medium.com](https://samratduttaofficial.medium.com/membership) 

## 密码

```
from datetime import datetime
import autowhatspy
import os
import timepath = path = os.path.dirname(os.path.realpath(__file__)) + "\\"
msg = path + "msg.txt"
contacts = path + "contacts.txt"
gechodriver = path + "gechodriver.exe"
gechodriver_log = path + "gecholog.txt"
user_profile = path + "firefoxprofile"while True:
    now = datetime.now()
    current_time = now.strftime("%H%M")
    if current_time == "2315":
        autowhatspy.message_to_contacts(msg, contacts, gechodriver, gechodriver_log, user_profile)
        time.sleep(60)
    else:
        time.sleep(30)
```

这会在晚上 11 点 15 分给她发一条“晚安，亲爱的”短信。代码每 30 秒检查一次当前时间，当时间到达晚上 11:15 时，它发送文本。如果你的电脑 24/7 运行，你可以让这段代码永远运行下去……(直到她离开你)

# 结论

我不知道她是否发现我用机器人给她发短信，但是，(剧透)，她分手了。所以，现在**我不得不写一个机器人在 WhatsApp** 上屏蔽她，因为我太虚弱了，自己做不到。

然后**我也要做一个机器人去调戏另一个我喜欢的女生**。有很多机器人。在机器革命发生之前，让我们尽可能多地使用它们。

请关注我，当我编写这些机器人时，您会收到通知。

我和我的朋友在 2019-2020 年对澳大利亚的森林火灾进行了详细的研究。如果您有兴趣阅读一篇资料翔实的数据科学文章，以下是链接:

[](https://towardsdatascience.com/how-much-green-did-australia-lose-during-2020-bushfires-calculating-using-landsat-8-images-36f35c67edb4) [## 澳大利亚在 2020 年的森林大火中损失了多少绿色？—使用 Landsat-8 图像进行计算

### 用 Landsat-8 计算澳大利亚森林火灾后某一地区健康自然植被的变化

towardsdatascience.com](https://towardsdatascience.com/how-much-green-did-australia-lose-during-2020-bushfires-calculating-using-landsat-8-images-36f35c67edb4) 

# 更多信息:

**AutoWhatsPy 文档:**【https://pypi.org/project/autowhatspy 

【https://discord.gg/7Bx6PGVy】大智若愚(编程)不和:

**我的 Github:**[https://github.com/SamratDuttaOfficial](https://github.com/SamratDuttaOfficial)

**我的领英:**[https://www.linkedin.com/in/SamratDuttaOfficial](https://www.linkedin.com/in/SamratDuttaOfficial/)

请考虑给我的 GitHub 库一颗星。这对我帮助很大。

想雇我吗？在 LinkedIn 上联系我。

最后，请考虑[请我喝杯咖啡](https://www.buymeacoffee.com/SamratDutta)。