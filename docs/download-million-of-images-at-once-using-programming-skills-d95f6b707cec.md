# 使用编程技巧一次下载数百万张图片

> 原文：<https://levelup.gitconnected.com/download-million-of-images-at-once-using-programming-skills-d95f6b707cec>

## 一个简单而有趣的小项目，添加到你的简历中

![](img/b7a35a7df76c44a0031b93a3f30e9061.png)

由[约书亚·阿拉贡](https://unsplash.com/@goshua13?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/loading-screen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在网上冲浪时，我经常遇到需要从网上下载图片的情况。

尽管通过右键单击然后从菜单中保存图像来下载图像是非常简单的。

但是，如果我需要下载更多的一个图像，更重要的是，全部批量或一次全部下载，该怎么办？

这很简单，说实话，任何人都可以做到。

在这篇文章中，我将和你分享如何使用 Python 从网上下载图片。

用 python 做这个任务时，我非常兴奋，因为这个过程可以变得多么动态和令人惊奇

仅仅因为我是用 Python 做的，我就可以从网上下载一张图片做无数不同的事情。

让我们进入激动人心的话题。

# 准备好一切！

你可能急于进入编码部分，但首先，我们需要执行一些导入和预置来实现我们的主要目标。

1.  代码编辑器
2.  下载

## 代码编辑器

[PyCharm](https://www.jetbrains.com/pycharm/download/) 是我使用的 Python IDE，每次我开始一个新项目，它都会为我创建一个虚拟环境。

如果你第一次测试一个程序，虚拟环境是理想的，因为它提供了很大的灵活性和宽容性。

![](img/e4199fb9a441c98d8a2ee6d7a5085833.png)

注意:在这个程序中，我们将关注如何从给定的链接列表中下载图像，这样就可以处理多个图像。

所以，上网复制你想下载的图片链接，用你自己的图片下载程序下载。

# 下载

为了继续我们的项目，我们需要下载一些库。我们将使用 pip 下载它们。

这很容易做到。你只需要在终端窗口输入`pip install requests`，这个库就会被下载到你的电脑上。

> 注意:使用 pycharm 中提供的终端窗口仅在该环境中安装库。

我们总共需要下载两个库，如下所述:

1.  要求
2.  beautifulsoup4

但是我们为什么需要这些图书馆呢？

## 要求

发出 HTTP 请求是一项非常复杂的任务，有许多活动部件和需要小心的事情。

**requests** 库是 Python 中最流行的 HTTP 请求库。

它将发出请求的困难和复杂性隐藏在一个漂亮、简单的 API 后面，使您可以专注于与服务交互和使用应用程序中的数据。

## 美味的汤

Beautiful Soup 是一个 Python 包，用于从 HTML 和 XML 文件中提取数据以满足 web 抓取需求。

它从页面源代码中生成一个树，可以用这个树以更清晰和层次化的方式提取数据。

# 下载带有 URL 列表的多个图像

下面列出了我的 eg 的网址，这是一张来自 unsplash.com 的无版权图片

```
[https://images.unsplash.com/photo-1519060825752-c4832f2d400a?ixlib=rb1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1170&q=80](https://images.unsplash.com/photo-1519060825752-c4832f2d400a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1170&q=80)
```

这就是全部的代码。

我强烈建议继续阅读以完全理解代码，因为我将一步一步地阅读每一行。尽情享受吧！

1.  这是我们为我们的项目所做的所有导入工作

```
import requests
import urllib.request
from bs4 import BeautifulSoup
```

2.包含您想要下载的图像链接的列表。

```
# A list is used so that multiple images can be downloaded with a loopUrls_img = [“URL for the file"] 
```

linksUrls_faulty 存储所有无法下载的图像的链接

```
# A list to store all the faulty 
linksUrls_faulty = []
```

3.这是一个循环，遍历列表中提供的所有链接来下载图像。

```
for image in Urls_img: count += 1

    #Naming the file using our counter variable
    name = f"Image #{count}.png"
    print(f"This is the image name: {name}")
```

计数器变量用于跟踪正在下载的当前图像编号。

这个计数变量也用于文件的命名。例如，如果计数变量在这里等于 3，那么图像将被命名为`Image #3`

4.在这一部分中，我们使用下载的请求库向每个链接发送 HTTPS 请求。

```
req = requests.get(image, stream=True)
```

`req=request.get()`发送请求并存储从服务器接收的数据

5.检查状态代码

```
# We need to check that the status code is 200 before doing anything else
    if req.status_code == 200:
```

`If req.status_code==200`检查从服务器返回的状态。

如果收到的代码是 200，这意味着连接到服务器时一切顺利。否则，在连接过程中会出现一些问题。

您可能想知道这 200 个代码是关于什么的。

HTTP 响应状态代码显示特定的 HTTP 请求是否成功完成。

简而言之，这是传达 HTTPS 请求现状的通用方式。

还有一些其他代码，但我们不会深入讨论这些，**如果你们都想让我写这些，请告诉我！**

例如，你所看到的非常流行的 404 未找到状态码，它的意思就是未找到。

200 码，发消息 OK！，这意味着我们可以继续前进。

因为我们已经检查了服务器状态，我们可以发送请求了

```
# Now let's send a request to the image URL:
    req = requests.get(image, stream=True) 
```

5.写文件

```
# This command below will allow us to write the data to a file as binary
        with open(name, 'wb') as f:
            for data in req:
                f.write(data)
```

这是用 Python 打开文件的标准方式。

我们使用内置的 Python 函数`open()` 如果你想了解更多关于如何在 Python 中打开文件的信息，请查看我以前的帖子，专门讨论 Python 中的文件操作。

`write()`函数在打开的文件中写入数据，写入的数据是使用。get()方法。

6.存储故障链接

```
else:
        # We will write all the faulty URLs back to the Urls_faulty    list:
        Urls_faulty.append(image)
```

在这行代码中，我们只是添加了由于某种原因无法下载的 URL。

# 结论

> “想象力比知识更重要。知识有限。想象力环绕着世界。”
> 
> ——阿尔伯特·爱因斯坦

编码可能很简单，做起来也很有趣。它帮助你发挥你的创造力，并帮助你学习很多东西。

也就是说，您现在可以通过 Python 批量下载图像。

谢谢你一直读到最后。如果你们想要这样的教程，请在下面的评论中联系我！

我很高兴收到你们的来信，并将继续尽我所能为更多的人服务。再见！