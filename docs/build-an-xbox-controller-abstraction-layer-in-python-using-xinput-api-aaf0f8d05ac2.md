# 使用 XInput API 在 Python 中构建一个 Xbox 控制器抽象层

> 原文：<https://levelup.gitconnected.com/build-an-xbox-controller-abstraction-layer-in-python-using-xinput-api-aaf0f8d05ac2>

或者:如何用夸张的代码量实现简单的目标—第 2 部分

![](img/4da541961a33ee4ba17a73ea524a5c86.png)

由 [Kamil S](https://unsplash.com/@16bitspixelz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在教程的[前一部分](/build-an-xbox-controller-abstraction-layer-in-python-using-xinput-api-f6d0c716adf)中，你已经让 Python 检测游戏手柄输入。到目前为止，一切顺利。在本课程中，您将:

*   写一个包装类的存根。
*   添加配置文件。

我们开始吧，好吗？

# 包装纸

从创建自己的 XInput 类开始。我们需要一种方法来告诉脚本哪个游戏手柄被连接，以及使用什么设置:

*   传递配置(`ini`文件的路径；更多关于它的一会儿)文件和游戏手柄号来初始化方法。尽管外观可变，但我不会讨论如何处理多种输入设备，但是如果您需要的话，选项就在那里。
*   初始化状态。
*   阅读配置并将`gamepad`段分配给类变量`config`。

接下来，需要定义一些常量:

很多东西，但大部分只是映射:

*   导入`xinput1_4`库。是 Windows 10 专用的最新版本。如果您也希望支持其他版本，可以查看[文档](https://docs.microsoft.com/en-us/windows/win32/xinput/xinput-versions)进行参考。
*   定义`TRIGGERS`和`THUMBS`映射。它们直接对应于`XInputGamepad`结构字段，只是没有`XINPUT_GAMEPAD_`前缀。使用映射值更方便。此外，您需要一种方法来确定哪组按钮被按下了。
*   将`AXES`字典中的`TRIGGERS`和`THUMBS`合并。从技术上来说，触发器既是按钮也是轴，但是因为它们是压力敏感的，所以把它们看作轴更符合逻辑。
*   `BUTTONS`映射。[参考](https://docs.microsoft.com/en-us/windows/win32/api/xinput/ns-xinput-xinput_gamepad#members)。
*   设定最大值`TRIGGER`、`MOTOR`和`THUMB_MAGNITUDE`。这些值由 [XInputGamepad](https://docs.microsoft.com/en-us/windows/win32/api/xinput/ns-xinput-xinput_gamepad#members) 和 [XInputVibration](https://docs.microsoft.com/en-us/windows/win32/api/xinput/ns-xinput-xinput_vibration) 文档页面建议。
*   `ERROR_SUCCESS`是成功获取控制器状态时得到的错误代码。如果选择的控制器被拔掉，你会得到不同的代码。

# 配置

目前还没有设置，所以让我们现在创建`ini`文件。将来，您可以在其中放置按钮映射。将文件命名为`default.ini`，并设置以下内容:

```
[gamepad]
THUMB_DEAD_ZONE = 0.2
TRIGGER_DEAD_ZONE = 0.2
THUMB_SENSITIVITY = 1
TRIGGER_SENSITIVITY = 1
```

*   只有一个[段](https://docs.python.org/3/library/configparser.html#supported-ini-file-structure):T6。
*   轴死区和灵敏度。

仅此而已。这是所有可以(也需要)调整的。

# 获取状态

现在，我们可以获取状态:

*   它看起来与您在本教程的前一部分中编写的代码相似，唯一真正的区别是它现在是一个方法。
*   只有一个 XInput API 方法来获取控制器状态和检查游戏手柄是否连接，这意味着您必须在一个函数中实现这两个功能。

这就是这部分教程的全部内容。在[最后一部](/build-an-xbox-controller-abstraction-layer-in-python-using-xinput-api-e971fe4adce8)中，你会和游戏手柄进行更重度的互动。会很好吃的，我保证。

# 最后的话(暂时)

感谢阅读！

你可以在我的 [GitHub](https://github.com/izdwuut/xbox-mapper-tutorial/tree/main) 上找到完整的代码。