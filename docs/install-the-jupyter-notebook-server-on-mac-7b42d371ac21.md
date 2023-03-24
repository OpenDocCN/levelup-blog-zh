# 在 Mac 上安装 Jupyter 笔记本服务器

> 原文：<https://levelup.gitconnected.com/install-the-jupyter-notebook-server-on-mac-7b42d371ac21>

## 系列:人工智能

## 带有说明和屏幕截图的精简演练

![](img/88e508fa670ed6d2fc136520b977cb40.png)

图像由[晶体 C](https://unsplash.com/@nightwxnderer)

> [扩展指南](https://medium.com/p/2a276f679e0)使用术语和命令的定义来帮助您了解正在发生的事情。

## 开放终端:

1.  按“命令⌘ +空格键”
2.  输入“终端”
3.  按“回车键”

![](img/cc81b0c20dd1e9643c43dee8744228bf.png)

## 安装 Jupyter 笔记本:

1.  从下面这些指令中复制命令
2.  末端的
3.  按“回车键”

```
python -m pip install jupyter
```

![](img/b4034038e4d56d63dc692252b27ad39f.png)

## 安装 WebSocket 扩展:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
python -m pip install jupyter_http_over_ws
```

![](img/5af41902f6fa85b439908ba56fe85da6.png)

## 创建配置文件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
jupyter notebook --generate-config
```

![](img/9c0f249fdcc246a699e5a3c0654a7ab5.png)

## 打开 Jupyter 目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
cd $HOME\.jupyter
```

![](img/77fca94f5a2fd1012ac05ff8757c2a81.png)

## 创建 SSL 证书:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”
4.  在“国家名称”中输入“美国”
5.  按“回车”
6.  输入“.”到剩余的字段中
7.  按“回车”

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout mykey.key -out mycert.pem
```

![](img/c9332c832c48196989d757548603a04b.png)

## 创建 JSON 文件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
echo "" >> jupyter_notebook_config.json
```

![](img/ff0a96e846a8c4c0e770d14c77220ef8.png)

## 打开 JSON 文件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
open -a textedit jupyter_notebook_config.json
```

![](img/9f1fdb7fc42e74eea5fb851e1f75ae77.png)

## 编辑 JSON 文件:

1.  从这些指令下面复制代码
2.  将代码粘贴到“文本编辑”中
3.  将“Admin”更改为 Windows 用户名
4.  单击“文件”菜单
5.  点击“保存”

![](img/f403251ca0f29a4abf3a1510f7f28d92.png)

## 创建密码:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
jupyter notebook password
```

![](img/280d917bc0c91b929cd15f3a930953e5.png)

## 打开启动代理目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
cd $HOME/library/launchagents/
```

![](img/e97ad43e4a326dcc1486a96dd33797dd.png)

## 检查可执行文件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”
4.  记下 Jupyter 笔记本的路径

```
which jupyter-notebook
```

![](img/829ce96e06931981fd0a8125da6b9d25.png)

## 创建 Plist 文件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
echo “” > com.jupyter.notebook.plist
```

![](img/1acb72dafca26eb4a4957e8ae98e0a89.png)

## 打开 Plist 文件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
open -a textedit com.jupyter.notebook.plist
```

![](img/6741a655998a31b194145a80c8cc1247.png)

## 编辑 Plist 文件:

1.  从这些指令下面复制代码
2.  将代码粘贴到“文本编辑”中
3.  将“Admin”更改为 Windows 用户名
4.  替换 Jupyter 笔记本路径
5.  单击“文件”菜单
6.  点击“保存”

![](img/a3cb90d84c8a130e398e26ca9a477bd1.png)

## 登录路由器:

1.  打开 web 浏览器
2.  从下面这些说明中复制[路由器的 IP 地址](https://www.techspot.com/guides/287-default-router-ip-addresses/)
3.  将路由器 IP 地址粘贴到 web 浏览器中
4.  按“回车”
5.  登录路由器

```
192.168.0.1
```

![](img/40b1ba3a2748733359860d5fa24ca036.png)

## 设置端口转发:

1.  找到“端口转发”页面
2.  将 IPv4 地址粘贴到“输入 IP 地址”文本字段中
3.  从下面复制端口这些说明
4.  将端口粘贴到“ [WAN](https://thealtruist.medium.com/how-to-set-up-the-jupyter-notebook-home-and-public-server-on-windows-10-4f92c2296b35#579b) Starting Port”文本字段中
5.  将端口粘贴到“WAN 终端端口”文本字段中
6.  选择“所有 IP 地址”
7.  点击“应用”

```
7777
```

![](img/eae55e596cf9bc639b030b931a9a71a2.png)

## 获取专用 IP 地址:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”
4.  记下私有 IP 地址

```
ipconfig getifaddr en0
```

![](img/9907782a6f98d04009cec26661472f2a.png)

## 获取 DNS 服务器:

1.  点按“苹果”菜单
2.  点按“系统偏好设置”
3.  点击“网络”
4.  点击“高级”
5.  单击“DNS”
6.  记下 DNS IP 地址

![](img/6aac5f2c6565284a1ba5fa00ef89f4b5.png)

## 设置静态 IP 地址:

1.  单击“TCP/IP”选项卡
2.  将“配置 IPv4”设定为“手动”
3.  输入之前的私有 IP 地址

![](img/21a515e24c633dfffd0aeb7d0da542e1.png)

## 设置 DNS 服务器:

1.  单击“DNS”选项卡
2.  单击 DNS 服务器中的“+”按钮
3.  输入之前的 DNS IP 地址
4.  单击“确定”
5.  点击“应用”

![](img/4a5d4caa499b08bc4526201b3d439c9a.png)

## 从本地网络访问服务器:

1.  登录到不同的计算机或笔记本电脑
2.  连接到同一个 WiFi 网络
3.  在 web 浏览器中输入 IPv4 地址
4.  在 IP 地址前添加“https://”
5.  在 IP 地址后附加“7777”
6.  按“回车”
7.  键入“thisisunsafe”
8.  输入密码
9.  点击“登录”

![](img/6a87b147358fe3a35fa72599406f9d06.png)

## 获取公共 IP 地址:

1.  从这些说明下面复制 URL
2.  将 URL 粘贴到 web 浏览器中
3.  记下公共 IP 地址

```
[https://www.google.com/search?q=whatsmyip](https://www.google.com/search?q=whatsmyip)
```

![](img/c697262d48595a1cc73b0556a34fcc5a.png)

## 从远程网络访问服务器:

1.  登录到不同的计算机或笔记本电脑
2.  连接到不同的 WiFi 网络
3.  在 web 浏览器中输入公共 IP 地址
4.  在 IP 地址前添加“https://”
5.  在 IP 地址后附加“7777”
6.  按“回车”
7.  键入“thisisunsafe”
8.  输入密码
9.  点击“登录”

![](img/61d338ede4409e52dfa2a5feb3b68d43.png)

> “希望这篇文章能帮助您获得👯‍♀️🏆👯‍♀️，记得订阅获取更多内容🏅"

## 后续步骤:

这篇文章是一个迷你系列的一部分，帮助读者设置他们开始学习人工智能、机器学习、深度学习和/或数据科学所需的一切。它包括包含复制和粘贴代码的说明和截图的文章，以帮助读者尽快获得结果。它还包括一些文章，包含带有解释和截图的说明，以帮助读者了解正在发生的事情。

```
**Linux:**
01\. [Install Multiple Python Versions](https://medium.com/p/8bd6d301d78c)
02\. [Install the CUDA Driver and Toolkit](https://medium.com/p/3494a4436d6)
03\. [Install the Jupyter Notebook Server](https://medium.com/p/f5bbc07e184a)
04\. [Install Virtual Environments in Jupyter Notebook](https://medium.com/p/c93fd8d07ca0)
05\. [Install the Python Environment for AI](https://medium.com/p/d2937ce641b7)**WSL2:**
01\. [Install Windows Subsystem for Linux 2](https://medium.com/p/e01f92e98cc0)
02\. [Install Multiple Python Versions](https://medium.com/p/ba81f21109d6)
03\. [Install the CUDA Driver and Toolkit](https://medium.com/p/be38703fed5c)
04\. [Install the Jupyter Notebook Server](https://medium.com/p/3ea9bc06a0e5)
05\. [Install Virtual Environments in Jupyter Notebook](https://medium.com/p/d99de1d79fd4)
06\. [Install the Python Environment for AI](https://medium.com/p/6d73735b546)
07\. [Install Ubuntu Desktop GUI (Bonus)](https://medium.com/p/7c3730e33bb2)**Windows 10:**
01\. [Install Multiple Python Versions](https://medium.com/p/15a8685ec99d)
02\. [Install the CUDA Driver and Toolkit](https://medium.com/p/f103ea5eae4b)
03\. [Install the Jupyter Notebook Server](https://medium.com/p/c2ca45793e3b)
04\. [Install Virtual Environments in Jupyter Notebook](https://medium.com/p/a307b6524715)
05\. [Install the Python Environment for AI](https://medium.com/p/604168afbd6e)**MacOS:** 01\. [Install Multiple Python Versions](https://medium.com/p/a58b1966825f)
02\. [Install the Jupyter Notebook Server](https://medium.com/p/7b42d371ac21)
03\. [Install Virtual Environments in Jupyter Notebook](https://medium.com/p/557f23e55f99)
04\. [Install the Python Environment for AI](https://medium.com/p/ed5c93639301)
```