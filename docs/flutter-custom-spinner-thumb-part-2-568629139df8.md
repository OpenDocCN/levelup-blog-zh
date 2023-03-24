# 自定义微调拇指在颤振(第二部分)

> 原文：<https://levelup.gitconnected.com/flutter-custom-spinner-thumb-part-2-568629139df8>

欢迎来到本教程建立一个圆角矩形自定义微调按钮。

第一部分:

[](https://medium.com/@singhgursheesh12/flutter-custom-spinner-thumb-aabd4ce43dda) [## 颤动-自定义微调器拇指

### 欢迎来到这个自定义微调拇指教程。

medium.com](https://medium.com/@singhgursheesh12/flutter-custom-spinner-thumb-aabd4ce43dda) 

你可以在 [Instagram](https://www.instagram.com/theboringdeveloper/) 上和我联系

## 让我们从看到我们的最终目标开始

![](img/b3cbd8313f9ad8e758692b38ade2ffbe.png)

来自第 1 部分:

> 我们知道，要创建自定义微调器 thumb，我们需要创建自己的 shape 类并扩展 SliderComponentShape，因为编辑 thumb 的默认方式是不够的

现在让我们看看我们在上面的代码中做了什么

1.  创建了一个名为— RoundedRectangleThumbShape 的类
2.  用 SliderComponentShape 扩展了上面的类
3.  创建了一个构造函数来获取拇指的半径，边框的粗细，边角的圆度
4.  我们从 getPreferredsize()方法返回 Size，下面是 Size.fromRadius()所做的，*创建一个正方形[Size]，其[width]和[height]是给定的* *维度的两倍。*

现在在 paint()方法中，我们绘制我们的拇指。

在前面的代码片段中，我们只对 paint()方法进行了更改。

1.  我们使用 context.canvas 获得画布
2.  基于圆心和半径构建一个矩形
3.  构建基于圆角矩形的矩形尺寸和圆角半径
4.  创建一个绘制对象来绘制拇指圆圈内的区域，我们设置颜色和样式
5.  创建一个 paint 对象来绘制拇指圈的边界，我们设置颜色、样式和笔画宽度。
6.  之后，我们在画布上使用 fillPaint 绘制内部的白色圆角矩形
7.  然后，我们在画布上使用边框绘制绘制圆角矩形

现在，我们将拇指形状更改为我们创建的新形状。

以上代码的结果。

![](img/b3cbd8313f9ad8e758692b38ade2ffbe.png)

万岁！！我们做到了。

点击查看完整项目[。](https://github.com/GursheeshSingh/Custom_Spinner_animation_part_2)

谢谢你坚持到最后。

我将会发布更多关于 flutter 的消息，敬请关注:)

[](https://github.com/GursheeshSingh) [## GursheeshSingh -概述

### 在 GitHub 上注册您自己的个人资料，这是托管代码、管理项目和与 40…

github.com](https://github.com/GursheeshSingh)  [## Gursheesh Singh -印度昌迪加尔|职业简介| LinkedIn

### 查看 LinkedIn 上 Gursheesh Singh 的专业资料。LinkedIn 是世界上最大的商业网络，帮助…

www.linkedin.com](https://www.linkedin.com/in/gursheesh-singh-a66545154/)  [## 古尔希什·辛格

### 欢迎回到 Instagram。登录查看您的朋友、家人和兴趣爱好捕捉和分享了什么…

www.instagram.com](https://www.instagram.com/igursheesh/) [](https://twitter.com/GursheeshChawla) [## 古尔希什·辛格

### Gursheesh singh 的最新推文(@GursheeshChawla)。商业解决方案开发者| Flutter 开发者| Android…

twitter.com](https://twitter.com/GursheeshChawla)