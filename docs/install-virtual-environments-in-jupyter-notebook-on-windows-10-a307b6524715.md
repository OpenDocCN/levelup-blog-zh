# 在 Windows 10 上的 Jupyter 笔记本中安装虚拟环境

> 原文：<https://levelup.gitconnected.com/install-virtual-environments-in-jupyter-notebook-on-windows-10-a307b6524715>

## 系列:人工智能

## 附有说明和截图的简明指南

![](img/55c0396db219b637f9fb0762632a1587.png)

图片由[西恩·佛利](https://unsplash.com/@_stfeyes)

> [扩展指南](https://medium.com/p/5c189856479)使用术语和命令的定义来帮助您了解正在发生的事情。

## 开放终端:

1.  打开 web 浏览器
2.  输入 Jupyter 笔记本电脑服务器的 IP 地址
3.  按“回车”
4.  点击“新建”
5.  点击“终端”

![](img/f0998e7684e1f415a36832f6158f1c96.png)

## 打开桌面目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
cd $home/desktop/
```

![](img/9a90095a383f0125290dec36d21edbfd.png)

## 克隆存储库:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
git clone --recursive [https://github.com/zzh8829/yolov3-tf2.git](https://github.com/zzh8829/yolov3-tf2.git)
```

![](img/03e0a10d26a05ed52939565b920fe1d5.png)

## 打开 YoloV3-Tf2 目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
cd yolov3-tf2
```

![](img/fc20d63327b26b1476dab801a00d5734.png)

## 创建虚拟环境:

1.  从下面这些说明中找到 Python 版本
2.  复制提供的命令
3.  将命令粘贴到终端
4.  按“回车”

```
**Python 3.5:** python35 -m venv venv35**Python 3.6: <----------**
python36 -m venv venv36**Python 3.7:**
python37 -m venv venv37**Python 3.8:**
python38 -m venv venv38
```

![](img/864e36858a3664a8597e1fbb54bbc889.png)

## 激活虚拟环境:

1.  从下面这些说明中找到 Python 版本
2.  复制提供的命令
3.  将命令粘贴到终端
4.  按“回车”

```
**Python 3.5:**
venv35/scripts/activate**Python 3.6: <----------**
venv36/scripts/activate**Python 3.7:**
venv37/scripts/activate**Python 3.8:**
venv38/scripts/activate
```

![](img/bdf945184a482cdea2eaa604ae5f9036.png)

## 升级 Pip:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
python -m pip install --[upgrade](#33fe) pip
```

![](img/206303cde25f208410d8b7749ebe200c.png)

## 安装要求:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
python -m pip install --requirement requirements-gpu.txt
```

![](img/70b060b7cab885153cd9479f30a4ba61.png)

## 下载重量:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
wget [https://pjreddie.com/media/files/yolov3.weights](https://pjreddie.com/media/files/yolov3.weights) -O data/yolov3.weights
```

![](img/fe59f011d5bf1bd9e4da82e0e5839386.png)

## 转换重量:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
python convert.py --weights ./data/yolov3.weights --output ./checkpoints/yolov3.tf
```

![](img/b7b8cc012852eaf8ef541fcc38db12c9.png)

## 使用存储库:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
python detect.py --image ./data/meme.jpg
```

![](img/ebdf5540286a25412264876f196411b4.png)

## 安装 IPython 内核:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
python -m pip install ipykernel
```

![](img/74a7e33c28b8172c7fa28b827094e038.png)

## 安装虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
**Python 3.5:**
python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"**Python 3.6: <----------**
python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"**Python 3.7:**
python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"**Python 3.8:**
python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"
```

![](img/cf7fe020d84f40a54de217a9b308e6ce.png)

## 打开 YoloV3-Tf2 目录:

1.  重新打开 Jupyter 笔记本
2.  单击“桌面”文件夹
3.  单击“YoloV3-Tf2”文件夹

![](img/b42f610e0dabf775b8a63ae519b438b5.png)

## 使用虚拟环境:

1.  点击“新建”
2.  点击“yolov3-tf2”

![](img/eb998a239efa7c2733952da2da6094d4.png)

## 运行代码单元格:

1.  从这些指令下面复制代码
2.  单击 Jupyter 笔记本中的空白单元格
3.  将代码粘贴到空单元格中
4.  按“Shift”+“Enter”

![](img/4f8ea77582674c355ad5f443729d2843.png)

## 运行代码单元格:

1.  从这些指令下面复制代码
2.  单击 Jupyter 笔记本中的空白单元格
3.  将代码粘贴到空单元格中
4.  按“Shift”+“Enter”

![](img/bc0f907c837b12be092f8907dc7d0057.png)

## 停用虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
deactivate
```

![](img/5f731872de8a09200893588e0118abd5.png)

## 列出已安装的虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
jupyter kernelspec list
```

![](img/9fec0f6e1010dda277bc2e194bf68994.png)

## 卸载虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”
4.  输入“Y”
5.  按“回车”

```
jupyter kernelspec uninstall yolov3-tf2
```

![](img/a03f689e86b77c4529ac463aef128a8f.png)

> 希望这篇文章能帮助您获得👯‍♀️🏆👯‍♀️，记得订阅获取更多内容🏅"

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