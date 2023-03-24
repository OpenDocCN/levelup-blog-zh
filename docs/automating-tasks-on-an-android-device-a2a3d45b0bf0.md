# 在 Android 设备上自动化任务

> 原文：<https://levelup.gitconnected.com/automating-tasks-on-an-android-device-a2a3d45b0bf0>

![](img/b6a095b79a6adb07d603264b4ad83dae.png)

约书亚·萨拉科的安卓主页

**ui automator Python 包介绍**

本文展示了如何使用 Python 代码在计算机上的 Android 设备上执行操作。需要强调的是，需要具备 Python 编程的知识。

这种自动化方法使用了 **uiautomator.py** ，这是 Android uiautomator 工具的 Python 包装器。[这里的](https://pypi.org/project/uiautomator/)是套餐详情。

**目录**

*   安装软件包
*   设置设备
*   uiautomator 简介
*   在设备上执行操作
*   结论

# 安装所需的软件包

使用 pip 井设置，任何 Python 版本都需要安装在机器上。uiautomator 包可以通过打开和终端/shell，输入以下代码安装；

```
pip install uiautomator
```

需要 java，通过这个[链接](https://java.com/en/download/)下载安装 Java。

需要安装 Android SDK。 [**这里的**](https://www.youtube.com/watch?v=wvi03sOBKWQ) 是一个关于下载安装 Android SDK 命令行工具 Windows 版的 YouTube 教程视频。[这里的](https://guides.codepath.com/android/installing-android-sdk-tools)是针对 MacOX 的[这里的](https://www.youtube.com/watch?v=OGJOPnV2fDc)是针对 Linux 的。

安装 Android SDK 后，需要 Android 平台工具。您可以通过在终端/shell 上运行以下代码来安装平台工具，该终端/shell 的目录在*‘bin’*文件夹中

```
sdkmanager "platform-tools" "platforms;android-28"
```

右键点击**我的电脑- >属性- >高级系统设置- >环境变量**，设置 *ANDROID_HOME* 变量，并将以下内容添加到 path 变量中

```
;%ANDROID_HOME%\platform-tools;%ANDROID_HOME%\tools;
```

创建一个名为 *ANDROID_HOME* 的新变量，其值包含安装在您计算机上的 Android SDK 的目录。

对于 macOS，使用 nano 在用户 home 目录下创建或编辑用户 bash 概要文件；

```
$ nano .bash_profile
```

在用户 bash 配置文件中添加 *ANDROID_HOME* 和 *PATH* 环境变量。

```
export ANDROID_HOME=/Users/Jerry/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/tools/bin
export PATH=$PATH/:$ANDROID_HOME/platform-tools
```

对于 Linux，请将此添加到您的*的末尾。bashrc，。配置文件*，或者适合您的 shell 的文件:

```
export ANDROID_HOME=your directory to android SDK on your computer
export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platforms-tools
```

# 设置设备

电脑需要 USB 调试权限才能访问 android 设备。

头转向设备**设置- >开发者选项- > USB 调试，**并打开它。

如果您的智能手机的设置中没有开发者选项，请在“关于手机”页面上查找设备内部版本号，并点击几次，直到您看到一条消息，表明您现在是一名开发者。转到设备的设置，寻找开发者的选项。

要确认 USB 调试处于活动状态，请打开终端/shell，将目录更改为 Android SDK 文件中的 platform-tools 文件夹，并输入以下命令来查看连接到计算机的设备(设备的序列号)。

```
adb devices
```

# Uiautomator 简介

打开任何首选的代码编辑器，并运行以下代码来启动程序

```
from uiautomator import Device
d = Device(‘enter the device serial number here’)
```

这段代码将把连接到计算机的带有指定序列号的 android 设备作为变量 *d* 导入

要获取有关导入设备的基本信息，请运行以下代码；

```
d.info
```

这将显示设备信息及其值的字典。

程序识别当前显示在设备屏幕上的 UI 对象，运行；

```
d.dump()
```

将在设备上显示当前对象的 XML 结果。

```
d.click(x, y)
```

将点击显示设备上的指定坐标。其他功能包括:

```
# press home key
d.press.home()

py# press back key
d.press.back()

# launch camera
d.press(“camera”)
```

要了解更多可用的操作，请访问官方 GitHub [这里](https://github.com/xiaocong/uiautomator)并通读文档。

# 使用 Python 在设备上执行任务/操作

本文将提供如何启动特定应用程序(用户或系统)并在其中执行一些基本任务的步骤。

本教程将利用 Python 程序打开 Spotify 应用程序并找到一首要播放的歌曲。

[**这里的**](https://youtu.be/TzcMblrBsik) 是这段特定代码运行的 YouTube 视频。

# 结论

使用上面的基本步骤，用 Python 编写您希望在设备上自动执行的操作。使用 *d.dump()* 生成当前屏幕内容，然后定位您想要操作的 UI 及其选择器(单击、拖动等)。)