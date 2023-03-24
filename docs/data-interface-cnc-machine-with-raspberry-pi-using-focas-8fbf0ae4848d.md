# 使用 Focas 与 Raspberry Pi 的数据接口数控机床

> 原文：<https://levelup.gitconnected.com/data-interface-cnc-machine-with-raspberry-pi-using-focas-8fbf0ae4848d>

在工业 4.0 时代，获取数据比以往任何时候都更加重要。由于所使用的 PLC 和 NC 控制技术，直接从 CNC 机床获取数据通常很麻烦，因此需要这些领域的深入知识。来自制造商的现有接口通常必须单独启用，并且非常昂贵。在本文中，Focas 和 C++编程用于展示如何访问数控机床。

![](img/e8f2c4f5719ea817f58ab3b24fa4d640.png)

**总之，本文的目标是:**
从 Fanuc 获取 CNC 数据
在 Raspberry Pi 上运行 Focas，作为一个廉价的操作系统

# 如何开始？

以下显示了使用数控机床的必要步骤。

**1)下载库** Focas 界面注册后免费下载:[https://www.inventcom.net/support/fanuc/universal-driver](https://www.inventcom.net/support/fanuc/universal-driver)

**2)文件的链接** 要成功链接，必须在 Raspberry Pi 上执行以下命令，并事先将您的 libfwlib32.so.1.0.x 复制到/usr/local/lib。

```
sudo ldconfig
sudo ln –s /usr/local/lib/libfwlib32.so.1.0.x /usr/local/lib/libfwlib32.so
```

**3)设置 c++代码** 基本安装完成后，现在可以开始编程了。需要注意的是，所有的 Focas 命令都必须单独声明。

**4)在 C++中加载库** 在下一步中，动态库被加载，先前声明的命令被初始化。

**5)连接到 Fanuc** 以下线路用于建立 Fanuc 和 Raspberry Pi 之间的连接。

**6)获取轴数据**
从现在开始，所有的命令都可以执行，这样字节就可以被读写了。在本例中，查询当前轴位置。

**7)关闭连接** 请求完成后，应再次关闭连接，库加载将被重置。

## 结论

感谢阅读。如果你有什么要补充的，欢迎随时留言评论！

本文主要关注 access 的初始创建。进一步的开发提供了集成 CMake 和链接到 Python 的可能性。总结一下这篇文章，只需一点编程工作，就可以用廉价的硬件在数控机床上读写数据。

注意:这个设置也可以在 Linux 和 Windows 下运行。设置步骤略有不同。