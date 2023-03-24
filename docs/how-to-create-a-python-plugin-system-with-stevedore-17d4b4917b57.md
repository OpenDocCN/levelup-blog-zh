# 如何用 Stevedore 创建一个 Python 插件系统

> 原文：<https://levelup.gitconnected.com/how-to-create-a-python-plugin-system-with-stevedore-17d4b4917b57>

![](img/88c04764c37f7e63b4cf37adff65837c.png)

照片由[基兰伍德](https://unsplash.com/@kieran_wood?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我经常看到和听到的一个问题是如何使用 Python 插件系统来扩展应用程序。对于测试工程师来说，这通常与硬件抽象有关。对于其他人，他们可能希望将核心功能与扩展分开。通过这种方法，可以简化部署，只需通过各自的软件包安装所需的组件。不管是什么原因，有几个库可以解决这个问题。在本文中，将使用 [Stevedore](https://pypi.org/project/stevedore/) 库构建一个简单的抽象层，我们将介绍构建插件的主要概念。

这篇文章中的代码可以在 https://github.com/chinghwayu/plugin_tutorial 获得。

注意:虽然代码说明了中继和硬件抽象层，但是相同的概念也可以用于服务，比如带有软件抽象层的数据库。

# 插件架构

不管使用什么插件框架，插件实现背后的架构大多是相同的。

*   定义抽象基类
*   定义从基类继承的插件类

# 抽象基类

对于 Python 插件系统中相似类型的插件，通常有一些共同的功能。基于协议或规范的插件通常具有共同的基本功能。例如，符合 IEEE 488.2 标准的仪器必须响应对其标识的查询。

尽管抽象部分有时会被省略，但这是重要的一步，因为它通过定义必须实现哪些方法来建立每个插件的基本结构。在 Python 中，这是通过模块 [abc](https://docs.python.org/3/library/abc.html) 来完成的。

所示示例用三种方法定义了一个基本的继电器乐器驱动程序:连接、断开和重新连接。在`__init__`方法中，`connected`属性是为状态建立的，因为这在所有插件之间是通用的。

对于其他抽象方法，只需要文档字符串。没有必要包含`pass`或其他代码。

```
from abc import ABCMeta, abstractmethod

class RelayBase(metaclass=ABCMeta):
    """Base class for relay plugins"""

    def __init__(self) -> None:
        """Define base attributes."""
        self.connected = False

    @abstractmethod
    def disconnect(self) -> None:
        """Disconnects relay."""

    @abstractmethod
    def connect(self) -> None:
        """Connects relay."""

    @abstractmethod
    def reconnect(self, seconds: int) -> None:
        """Disconnects for specified time and reconnects.
        Args:
            seconds (int): Amount of time to sleep between disconnect and connect.
        """
```

# 插件类

当定义实际的实现时，每个插件都将从插件基类继承。在这个例子中，定义了两个继承自`RelayBase`的插件。

在`__init__`方法中，进行了一个`super()`调用来创建其他方法将更新的`connected`属性。

```
class RelayOne(RelayBase):
    def __init__(self):
        super().__init__()

    def disconnect(self):
        self.connected = False
        print("Disconnected One")

    def connect(self):
        self.connected = True
        print("Connected One")

    def reconnect(self, seconds: int = 5):
        self.seconds = seconds
        self.disconnect()
        print(f"One paused for {seconds} seconds...")
        self.connect()

class RelayTwo(RelayBase):
    def __init__(self):
        super().__init__()

    def disconnect(self):
        self.connected = False
        print("Disconnected Two")

    def connect(self):
        self.connected = True
        print("Connected Two")

    def reconnect(self, seconds: int = 5):
        self.seconds = seconds
        self.disconnect()
        print(f"Two paused for {seconds} seconds...")
        self.connect()
```

在这一点上，你可能认为这是建立一个 Python 插件系统所需要的。对于简单的插件，这可能是真的。但是，请考虑以下情况:

*   我们如何决定在运行时调用哪个插件？
*   我们如何在实现之间轻松切换？
*   如何才能将这些插件打包发行？
*   我们如何测试接口以确保正确的实现被调用？

对于简单的应用程序或脚本，我们可以简单地直接调用特定的实现并硬编码。但是请考虑这样一种情况，您可能正在与另一个位置的队友共享代码，而他们没有相同的中继。我们不想用硬编码的替代实现来维护同一代码的另一个版本。我们需要的是在不改变任何代码的情况下在实现之间轻松切换的方法。应该进行的唯一更改是在运行时使用的实现的配置文件中。

# 插件入口点

有几种方法可以发现和加载插件。对于 Stevedore，它使用[入口点](https://docs.python.org/3/library/importlib.metadata.html#entry-points)来建立键，这些键可以作为指向特定代码位置的指针进行查询。这与软件包中常见的机制相同，一旦安装到环境中，就可以将 Python 代码作为命令行脚本启动。虽然这使用了内置的`console_scripts`类型的入口点，但是为了管理插件，可以创建定制的入口点类型。

关于用插件扩展应用程序的完整讨论，请观看《码头工人》的作者 Doug Hellman 在 2013 年 PyCon 上的演讲。该演示将装卸工采用的方法与当时其他现有的方法进行了比较。

当构建用于分发的包时，我们可以添加入口点，这样当它被安装到 Python 环境中时，环境就可以立即从已建立的键中知道插件的位置。添加入口点很简单。在包的`setup.py`中，将字典分配给`setup`的`entry_points`参数。

```
from setuptools import setup

setup(
    entry_points={
        "plugin_tutorial": [
            "relay1 = relay:RelayOne",
            "relay2 = relay:RelayTwo",
        ],
    },
)
```

`plugin_tutorial`键被用作插件名称空间。每个插件的名字被定义为`relay1`和`relay2`。插件的位置被定义为模块名和模块内的类，用冒号、`relay:RelayOne`和`relay:RelayTwo`分隔。

如果我们正在使用插件，但不需要作为软件包安装，我们可以将它们注册到 Stevedore 的入口点缓存中。这对于开发和实现单元测试非常有用。

下面的示例检查指定入口点的命名空间。如果入口点不存在，它将被添加。

# 管理插件

使用 Stevedore，有几种方法可以管理 Python 插件系统中的插件。

*   驱动程序—单一名称、单一入口点
*   钩子——单个名字，多个入口点
*   扩展—许多名称、许多入口点

对于本文，将讨论驱动程序方法，因为这是最常见的用例。看一下装卸工文档，其中讨论了其他方法的[装载模式](https://docs.openstack.org/stevedore/latest/user/patterns_loading.html)。

对于一个驱动程序，我们需要调用`[DriverManager](https://docs.openstack.org/stevedore/latest/reference/index.html#stevedore.driver.DriverManager)`。在参数表中，只有`namespace`和`name`是必需的，它们与入口点直接相关。可选参数可用，本例中使用的是`invoke_on_load`。虽然 relay 示例只建立了一个类属性，但是对于实际的仪器驱动程序，我们通常需要执行某种初始化。这可以在加载插件时执行。

调用[驱动管理器](https://docs.openstack.org/stevedore/latest/reference/index.html#stevedore.driver.DriverManager)将返回一个管理器对象。实际的驱动程序对象可以通过`driver`属性来访问。根据这个属性，我们还可以创建抽象方法来调用驱动程序方法。

```
from stevedore import driver

class Relay:
    def __init__(self, name="", **kwargs) -> None:
        self._relay_mgr = driver.DriverManager(
            namespace="plugin_tutorial",
            name=name,
            invoke_on_load=True,
            invoke_kwds=kwargs,
        )

    @property
    def driver(self):
        return self._relay_mgr.driver

    def disconnect(self) -> None:
        self.driver.disconnect()

    def connect(self) -> None:
        self.driver.connect()

    def reconnect(self, seconds: int = 5) -> None:
        self.driver.reconnect(seconds)
```

没有使用`**kwargs`参数，但包含该参数是为了展示如何将参数传递给可能具有不同初始化参数的驱动程序。

`driver`方法的`@property`装饰器是语法糖，为驱动程序对象提供快捷方式。如果没有提供，我们需要调用驱动程序的`disconnect`方法，如下所示:

```
r = Relay(name="relay1")
r._relay_mgr.driver.disconnect()
```

# 把它放在一起

对于安装在 Python 环境中的插件，入口点是在安装包时建立的。通过抽象接口，我们可以决定在运行时加载哪个插件。这里展示的是一个调用插件及其每个方法的单元测试。

为了运行它，我们需要首先作为一个包安装。

```
$ pip install -e /path/to/plugin_tutorial
...
Installing collected packages: plugin-tutorial
  Running setup.py develop for plugin-tutorial
Successfully installed plugin-tutorial
$ pip list | grep plugin_tutorial
plugin-tutorial 1.0.0    /path/to/plugin_tutorial
```

测试代码:

```
from relay import Relay

def test_installed_plugin():
    r1 = Relay(name="relay1")
    assert isinstance(r1, Relay)
    assert r1.driver.connected == False
    r1.disconnect()
    assert r1.driver.connected == False
    r1.connect()
    assert r1.driver.connected == True
    r1.reconnect(7)
    assert r1.driver.seconds == 7

    r2 = Relay(name="relay2")
    assert isinstance(r2, Relay)
    assert r2.driver.connected == False
    r2.disconnect()
    assert r2.driver.connected == False
    r2.connect()
    assert r2.driver.connected == True
    r2.reconnect(9)
    assert r2.driver.seconds == 9
```

要调用未通过软件包安装过程安装的插件，我们需要首先注册，然后调用插件。这对于编写包含虚拟插件的单元测试非常有用。显示的是注册了插件的相同单元测试。

```
from relay import Relay
from register_plugin import register_plugin

def test_register_plugin():
    namespace = "plugin_tutorial"
    register_plugin(
        name="relay1",
        namespace=namespace,
        entry_point="relay:RelayOne",
    )
    register_plugin(
        name="relay2",
        namespace=namespace,
        entry_point="relay:RelayTwo",
    )
    r1 = Relay(name="relay1")
    assert isinstance(r1, Relay)
    assert r1.driver.connected == False
    r1.disconnect()
    assert r1.driver.connected == False
    r1.connect()
    assert r1.driver.connected == True
    r1.reconnect(7)
    assert r1.driver.seconds == 7

    r2 = Relay(name="relay2")
    assert isinstance(r2, Relay)
    assert r2.driver.connected == False
    r2.disconnect()
    assert r2.driver.connected == False
    r2.connect()
    assert r2.driver.connected == True
    r2.reconnect(9)
    assert r2.driver.seconds == 9
```

# 资源

欲了解更多信息:

*   教程代码—[https://github.com/chinghwayu/plugin_tutorial](https://github.com/chinghwayu/plugin_tutorial)
*   装卸工用户指南—[https://docs.openstack.org/stevedore/latest/user/index.html](https://docs.openstack.org/stevedore/latest/user/index.html)
*   抽象基类—[https://docs.python.org/3/library/abc.html](https://docs.python.org/3/library/abc.html)
*   入口点—[https://docs . python . org/3/library/import lib . metadata . html #入口点](https://docs.python.org/3/library/importlib.metadata.html#entry-points)
*   PyCon 2013 装卸工展示—[https://youtu.be/7K72DPDOhWo](https://youtu.be/7K72DPDOhWo)
*   Yapsy(又一个插件系统)——[http://yapsy.sourceforge.net/](http://yapsy.sourceforge.net/)
*   Pluggy (Pytest 插件系统)——[https://pluggy.readthedocs.io/en/stable/](https://pluggy.readthedocs.io/en/stable/)

*原载于 2021 年 11 月 30 日*[*https://chinghwayu.com*](https://chinghwayu.com/2021/11/how-to-create-a-python-plugin-system-with-stevedore/)*。*