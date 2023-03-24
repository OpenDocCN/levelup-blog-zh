# 使用 XInput API 在 Python 中构建一个 Xbox 控制器抽象层

> 原文：<https://levelup.gitconnected.com/build-an-xbox-controller-abstraction-layer-in-python-using-xinput-api-f6d0c716adf>

或者:如何用夸张的代码量实现简单的目标—第 1 部分

![](img/bc199815b2922bfaf7f5020f03bf72b0.png)

照片由 [Kamil S](https://unsplash.com/@16bitspixelz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我曾经想过:输入——输出。按下游戏手柄按钮 Python 中的动作。*简单地说就是控制器映射*。概念上——是的。就是这么简单。但是，因为您使用 XInput API 与控制器交互，所以事情变得复杂了。毫不奇怪。你使用一个系统 DLL，大概是通过 [ctypes](https://docs.python.org/3/library/ctypes.html) 库，这意味着不同于 Python 原生的数据类型。当然，Microsoft API 提供了一些方便的函数，在本教程中您将使用其中的两个，但是您使用 C 指针与它们进行交互。这样的例子不胜枚举。

在硬币的另一面，可能看起来令人讨厌的只是 ctypes 代码。幸运的是，你很早就将其抽象出来，然后开始解决实际问题。这是非常低级的东西，所以你需要执行一些繁琐的任务，但这是可以管理的。当你看到你的电脑收到输入并准备好处理它时，真的很有趣。希望你也会喜欢！

# 实现 XInput API

您要做的第一件事是实现 [XInput API](https://docs.microsoft.com/en-us/windows/win32/xinput/xinput-game-controller-apis-portal) 所需的类。您将定义三个类:

*   `XInputGamepad` —控制器的实际状态。
*   `XInputState` —控制者声称的状态。
*   `XInputVibration` —马达的速度。

让我们把它们分解一下。

## [XInputGamepad](https://docs.microsoft.com/en-us/windows/win32/api/xinput/ns-xinput-xinput_gamepad#syntax)

一个*事实上的*控制器状态类:

*   `XInputGamepad`类继承自`Structure`类。这意味着我们在 Python 中的类应该被当作一个`struct`。
*   定义`_fields_`变量。它包含结构的字段。如您所见，它们是元组——变量名及其类型。它们都是整数。

如文档中所述，它是以下 C `struct`的直接映射:

```
struct _XINPUT_GAMEPAD {
  WORD  wButtons;
  BYTE  bLeftTrigger;
  BYTE  bRightTrigger;
  SHORT sThumbLX;
  SHORT sThumbLY;
  SHORT sThumbRX;
  SHORT sThumbRY;
}
```

我认为理解`struct`概念最简单的方法是把它看作一个没有方法的类——只有变量。如果您将它定义为一个 Python 类，它将如下所示:

```
class XInputGamepad:
    buttons: int
    left_trigger: int
    right_trigger: int
    thumb_l_x: int
    thumb_l_y: int
    thumb_r_x: int
    thumb_r_y: int
```

不会一直这么默默无闻。很快您将编写一些抽象层，使与 XInput API 的交互更加合理。

## [新输入状态](https://docs.microsoft.com/en-us/windows/win32/api/xinput/ns-xinput-xinput_state)

表示控制器状态的类。它基本上是`XInputGamepad`，但是有一个字段指示状态已经改变:

```
class XInputState(ctypes.Structure):
    _fields_ = [
        ('dwPacketNumber', ctypes.wintypes.DWORD),
        ('Gamepad', XInputGamepad),
    ]
```

*   基本上和前面的类一样，唯一的不同是`Gamepad`字段是一个你之前定义过的结构。
*   `dwPacketNumber`表示状态可能已经改变。

## [XInputVibrations](https://docs.microsoft.com/en-us/windows/win32/api/xinput/ns-xinput-xinput_vibration)

振动，顾名思义:

```
class XInputVibration(ctypes.Structure):
    _fields_ = [
        ('wLeftMotorSpeed', ctypes.wintypes.WORD),
        ('wRightMotorSpeed', ctypes.wintypes.WORD)
    ]
```

*   Xbox 游戏手柄有两个马达——左马达和右马达。他们独立工作。
*   每个变量指定使用哪个马达。它们代表振动强度。

让我们好好利用前面提到的类。

# 获取输入

你马上就会抛弃这些代码，但是看看它是否有效还是很好的。暂时不要创建任何函数。只需在`__main__`街区检查一下:

*   使用`ctypes`导入 XInput `api`。
*   定义控制器`state`。这是你一会儿要用到的一个函数所需要的。你现在还不知道状态。
*   如果连接了几个控制器，请在 0-3 的范围内指定要使用的控制器。如果只连接了一个控制器，它应该默认为`0`。
*   在一个循环中获取状态，提供`gamepad_number`和一个指向`state`的指针。
*   `print`一个`dwPacketNumber`变量，指示控制器状态是否已经改变。
*   `sleep`，然后清除(`cls`)屏幕，使其可读。

有了这个，你就可以连接你的 Xbox 控制器并启动脚本了。首先，让我们将它保存为`xinput.py`，这样就可以用下面的命令运行它:

```
python xinput.py
```

你会看到一个随机数。如果你操作控制器的时候它改变了，那一切都没问题。您可以删除`__main__`块内容，并移动到教程的[下一部分](/build-an-xbox-controller-abstraction-layer-in-python-using-xinput-api-aaf0f8d05ac2)。

# **最后的话(目前)**

感谢阅读！

你可以在我的 [GitHub](https://github.com/izdwuut/xbox-mapper-tutorial/tree/main) 上找到完整的代码。