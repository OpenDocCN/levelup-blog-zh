# 使用 Python 测试互联网速度——Python 编程

> 原文：<https://levelup.gitconnected.com/test-internet-speed-using-python-python-programming-2b8b387b004f>

![](img/89fa680ffe9da8ce4e36ab2550dcd4f7.png)

在本文中，我们将讨论如何使用 Python 和 speedtest-cli 库来测试互联网速度。

**目录**

*   介绍
*   安装所需的库
*   方法描述
*   测试互联网速度
*   结论

# 介绍

我们家中和办公室的互联网连接因互联网服务提供商(ISP)、允许的流量限制以及最重要的速度而异。

那么，当我们想测试我们的连接速度时，我们该怎么做呢？正确，我们在谷歌上查找一些速度测试网站，然后继续进行。

试着用不到 10 行代码在你的电脑上用 Python 测试网速怎么样？我们来看看吧！

# 安装所需的库

为了继续本文中的示例，您需要安装一个额外的库 speedtest-cli。

如果您没有安装它们，请打开“命令提示符”(在 Windows 上)并使用以下代码安装它们:

```
pip install speedtest-cli
```

现在已经安装好了，让我们继续导入它:

# 方法描述

首先，我们需要创建一个 **Speedtest()** 类的实例，然后检查它拥有的方法，并讨论每一个方法。

这个库的[源代码](https://github.com/sivel/speedtest-cli)目前没有很多关于每个方法及其用法的详细信息，所以我们将从头开始探索。

下一步我们要做的是找出上述类中实际包含的内容。换句话说，我们想看看我们可以使用什么，我们可以检索什么信息。

使用 inspect 库(Python 中预先构建的)，让我们看看**的**对象有哪些方法:

我们应该得到以下列表作为输出:

太神奇了！现在我们找到了包含的内容，接下来我们将讨论这些方法各自执行的操作。

**。下载()**

正如你可能从它的名字中猜到的，这个方法将测试你当前连接的下载速度(以字节为单位)。要查看它的运行情况:

我得到的结果是:142857662.477676，相当于 142.86 Mbps。

**。上传()**

这个方法类似于前一个，但是测试你当前连接的上传速度(以字节为单位)。要查看它的运行情况:

我得到的结果是:235955606.655482，相当于 235.96 Mbps。

**。get_best_server()**

这种方法允许我们确定测试连接的最佳服务器。一般来说，这有助于找到您所在地区(城市)的最佳测试服务器。

就格式而言，它将返回一个包含该服务器详细信息的字典。让我们来看看:

在我的例子中，因为我住在多伦多，它在多伦多市区的某个地方找到了最好的服务器，信息如下:

```
url : [http://tor47spd01.srvr.bell.ca:8080/speedtest/upload.php](http://tor47spd01.srvr.bell.ca:8080/speedtest/upload.php)
lat : 43.6481
lon : -79.4042
name : Toronto, ON
country : Canada
cc : CA
sponsor : Bell Canada
id : 17394
host : tor47spd01.srvr.bell.ca:8080
d : 18.828394194894738
latency : 8.424
```

**。get_closest_servers()**

这种方法允许我们使用一组离我们的位置很近的服务器，然后我们可以使用这些服务器在不同的服务器/地区进行速度测试。

类似于前面的方法，我们将得到一个字典，但不是一个详细的服务器，而是更多。

这里我们创建存储整个字典，但出于显示目的，只打印出字典中第一项的详细信息:

输出显示为:

```
url : [http://speedtest2-tor.teksavvy.com:8080/speedtest/upload.php](http://speedtest2-tor.teksavvy.com:8080/speedtest/upload.php)
lat : 43.6532
lon : -79.3832
name : Toronto, ON
country : Canada
cc : CA
sponsor : Teksavvy Solutions Inc
id : 32632
host : speedtest2-tor.teksavvy.com:8080
d : 18.709590075865655
```

请注意，最好的服务器不一定是最近的服务器，但通常它们都在同一地区/城市。

**。get_servers()**

这种方法允许我们获得所有可用服务器的信息。它的工作方式与前面的方法非常相似，并返回一个包含每个服务器详细信息的字典。

注意:我遇到的唯一问题是它们使用的键值，并且不确定如何解释它们，因为它们不是连续的。但我确信造物主对此有一个内在的逻辑。

这将返回一个大型数据集，因此为了直观显示它们，下面是一台服务器的示例输出:

```
url : [http://24.224.127.172:8080/speedtest/upload.php](http://24.224.127.172:8080/speedtest/upload.php)
lat : 35.5847
lon : -80.8103
name : Mooresville, NC
country : United States
cc : US
sponsor : Continuum
id : 3383
host : 22.224.127.172:8080
d : 922.322219276907
```

**。set_mini_server()**

这个方法允许我们通过传递一个 URL 来设置 speedtest 服务器，而不是查询服务器列表。

服务器 URL 可以从我们使用**检索的任何服务器中选择。get_servers()** 方法，或者来自这个列表的[。](https://gist.github.com/gxgani/11401911)

例如，我选择了“http://speedtest.oltv.ru/”,我可以这样集成它:

我们得到了一台为我们的速度测试设置的服务器:

```
sponsor : Speedtest Mini
name : speedtest.oltv.ru
d: 0
url : [http://speedtest.oltv.ru/speedtest/upload.php](http://speedtest.oltv.ru/speedtest/upload.php)
latency : 0
id : 0
```

**。get_config()**

这个方法允许我们找出 speedtest 类的当前配置，并提供一些关于我们自己的连接的相关信息，包括 IP、服务器的位置、internet 服务器提供商(ISP)等等。

它的执行非常简单，只需要调用方法而不需要任何额外的参数:

它返回一个包含相关信息的字典词典。

# 结论

在本文中，我们讨论了如何使用 Python 测试互联网速度，还介绍了 speetest-cli 库的特性，并展示了如何调整一些参数。

如果你有任何问题或者对编辑有任何建议，请在下面留下你的评论，并查看更多我的 [Python 编程](https://pyshark.com/category/python-programming/)文章。

*原载于 2020 年 7 月 29 日 https://pyshark.com*[](https://pyshark.com/test-internet-speed-using-python/)**。**