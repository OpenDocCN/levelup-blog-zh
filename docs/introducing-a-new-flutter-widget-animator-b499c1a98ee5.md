# 介绍一个新的 Flutter widget 动画师

> 原文：<https://levelup.gitconnected.com/introducing-a-new-flutter-widget-animator-b499c1a98ee5>

## 最后的小部件源代码。

![](img/6cd2d12420aec95353e0877bbf78130c.png)

[颤振 _ 圆形 _ 动画制作](https://github.com/Ezaldeen99/flutter_circular_animator)

我在 Lottie 上看到了这个动画，并决定将其转换为一个新的 flutter animations 小部件，所以让我们看看如何做。

在开始阅读本文之前，如果你有使用 flutter[**custom paint**](https://api.flutter.dev/flutter/widgets/CustomPaint-class.html)**的经验，请跳到文章的第二部分(弧线)。**

# **让我们从画两个圆开始**

**要画一个圆，我们必须创建一个 **CustomPainter** 类。下面是一个画圆的例子:**

**为外圆制作另一个 **CustomPainter** ，现在使用相同的代码。**

**如果您想查看您的 **CustomPainter** 结果，您可以这样使用它。**

**现在我们可以使用一个堆栈来得到这样的结果:**

**您应该会得到这样的结果:**

**![](img/ab9b939cf19a9ff9f10cc1ded57e9dd8.png)**

**[颤动 _ 圆形 _ 动画制作](https://github.com/Ezaldeen99/flutter_circular_animator)**

# ****圆弧****

**为了给我们的旋转图标腾出空间，我们必须把我们的圆分成几个弧**

**![](img/bdb3b81b71b7a21120eba90992f80411.png)**

**[颤动 _ 圆形 _ 动画制作](https://github.com/Ezaldeen99/flutter_circular_animator)**

**我们可以控制我们在第一步中已经用来画圆的每个圆弧的起点和终点。**

**这是一个简单的例子，演示了如何将一个圆分割成两个圆弧。**

# **动画片**

**为了制作旋转动画，我们可以使用颤动动画来制作曲线线性动画。您可以在小部件 initState()中创建圆形动画，如下所示。**

**你可以看到我们使用了**反转动画**让第二个圆反向旋转第一个方向**

**现在您必须添加几行代码来将这些动画应用到您的 **CustomPainter** 类中**

**现在你可以看到围绕你的部件旋转的圆环，困难的部分已经完成了(不是真的😂)**

# ****绘图图标****

> **坚持住，我们快到了，不要放弃**

**这对我来说是最难的部分，主要的挑战是跟踪两条弧线之间空间的 X，Y 坐标，以便在那里绘制图标。我从最简单的形状开始，比如十字形、圆形、椭圆形以及介于十字形和圆形之间的形状。**

**以下是我在空白处画一些图标的方法:**

**最后，你可以看到一些动画围绕着你的小部件旋转**

**![](img/d8abe5e849d4450e7d26f13f983db5f1.png)**

**[颤振 _ 圆形 _ 动画制作](https://github.com/Ezaldeen99/flutter_circular_animator)**

**您可以从这里直接在您的应用程序中使用它:**

**[](https://pub.dev/packages/widget_circular_animator) [## widget _ circular _ animator | Flutter 包

### 这个 lottie 动画启发了一个新的 Flutter widget 动画师。[https://lottiefiles.com/3619-profile]小部件…

公共开发](https://pub.dev/packages/widget_circular_animator) 

你可以在这里看到这个小部件的完整源代码，还有一个演示应用程序[在这里](https://github.com/Ezaldeen99/flutter_circular_animator)

【github.com】ezaldeen 99/Flutter _ circular _ animator:一个预建的 Flutter circular animator 可以适合你的新剖面图或者你的任何其他部件(T3)

我希望你喜欢它，不久你会在这里看到更多的动画。**