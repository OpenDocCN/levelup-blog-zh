# 尽可能简单地开始使用 VS 代码开发容器开发 Qt

> 原文：<https://levelup.gitconnected.com/getting-started-developing-qt-with-vs-code-development-containers-as-simple-as-possible-9c0c3a2de090>

![](img/807a28aec750df04470834e8321f005e.png)

图片来自 [ICS](https://www.ics.com/blog/getting-started-qt-and-qt-creator-windows)

*免责声明—* 这只是我的拙见，也许有更方便的方法可以达到这个目的。

为此，我已经在 [GitHub](https://github.com/ierturk/qt-qml-ai-collection) 上准备了回购。报告收集了一些关于人工智能、图像处理、Qt Qml 和嵌入式 RPC 的实验作品。程序就像下面这样简单。

**要求**

*   任何带有`Wayland Display Manager`的`Linux`分布(在`Debian 11`测试)
*   `Docker`守护进程
*   `VS Code`带`Remote Container`分机。或者也可以使用`Remote SSH`

**按照**运行即可

*   如下克隆回购

```
$ git clone [https://github.com/ierturk/qt-qml-ai-collection.git](https://github.com/ierturk/qt-qml-ai-collection.git)
$ git submodule update --init
```

*   只需打开`VS Code`中的克隆文件夹
*   在`development container`中重新打开
*   使用`CMake`构建
*   运行任何由`CMake`构建的目标

有关构建开发容器的更多信息，您可以参考下面的链接

*   [用于演示的回购](https://github.com/ierturk/qt-qml-ai-collection)
*   [开发容器来源](https://github.com/ierturk/dev-containers)

就这样