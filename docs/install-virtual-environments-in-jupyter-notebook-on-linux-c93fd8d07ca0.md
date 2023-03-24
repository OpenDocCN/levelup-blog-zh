# 在 Linux 上的 Jupyter 笔记本中安装虚拟环境

> 原文：<https://levelup.gitconnected.com/install-virtual-environments-in-jupyter-notebook-on-linux-c93fd8d07ca0>

## 系列:人工智能

## 附有说明和截图的简明指南

![](img/a32c9ff817031281b94f8b8e0947fa43.png)

图片由[西恩·佛利](https://unsplash.com/@_stfeyes)

> [扩展指南](https://medium.com/p/1556c8655506)使用术语和命令的定义来帮助您了解正在发生的事情。

## 开放终端:

1.  输入 Jupyter 笔记本电脑服务器的 IP 地址
2.  按“回车”
3.  点击“新建”
4.  点击“终端”

![](img/60dd8fd7d74fdd3ff72ef94cafd839a3.png)

## 开放 Bash:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
bash
```

![](img/087ac697acc4ba0b77423612aa58b729.png)

## 打开桌面目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
cd $HOME/Desktop/
```

![](img/99e35ed5705366ddfec32bf1c3ddb010.png)

## 克隆存储库:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
git clone --recursive [https://github.com/zzh8829/yolov3-tf2.git](https://github.com/zzh8829/yolov3-tf2.git)
```

![](img/23b2a94605f2c6a2a64df4c650ee765b.png)

## 打开 YoloV3-Tf2 目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
cd yolov3-tf2
```

![](img/8a70aa1a82226f78628d18213422dbbe.png)

## 设置 Pyenv 版本环境变量:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
PYENV_VERSION=3.6.8
```

![](img/fbbd542141d3bc85984f714d4fd7a1b0.png)

## 创建虚拟环境:

1.  从下面这些说明中找到 Python 版本
2.  复制提供的命令
3.  将命令粘贴到终端
4.  按“回车”

```
**Python 3.5:** python -m venv venv35**Python 3.6: <----------**
python -m venv venv36**Python 3.7:**
python -m venv venv37**Python 3.8:**
python -m venv venv38
```

![](img/60a82ec3f9c32d70390eb6f32678b445.png)

## 激活虚拟环境:

1.  从下面这些说明中找到 Python 版本
2.  复制提供的命令
3.  将命令粘贴到终端
4.  按“回车”

```
**Python 3.5:**
[source](#4554) venv35/bin/activate**Python 3.6: <----------**
source venv36/bin/activate**Python 3.7:**
source venv37/bin/activate**Python 3.8:**
source venv38/bin/activate
```

![](img/18af2cb79ff4ece357476bce62298ce8.png)

## 升级 Pip:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
python -m pip install --[upgrade](#465a) pip
```

![](img/4059878a7370d03b5748250460d9390d.png)

## 安装要求:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
python -m pip install --requirement requirements-gpu.txt
```

![](img/4fa5d9986bc64db1416480139a75f888.png)

## 下载重量:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
wget [https://pjreddie.com/media/files/yolov3.weights](https://pjreddie.com/media/files/yolov3.weights) -O data/yolov3.weights
```

![](img/5fa60b057d1d32e8dce68f1be70b1b1a.png)

## 转换重量:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
python convert.py --weights ./data/yolov3.weights --output ./checkpoints/yolov3.tf
```

![](img/21cb54f340e66ee46ce9ab1f2beefc7e.png)

## 使用存储库:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
venv36/bin/python detect.py --image ./data/meme.jpg
```

![](img/b14d75c0db09051a2759b73f51283674.png)

## 安装 IPython 内核:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
python -m pip install ipykernel
```

![](img/591cb1993145e20046821a22cef936e5.png)

## 安装虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
**Python 3.5:**
sudo venv35/bin/python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"**Python 3.6: <----------**
sudo venv36/bin/python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"**Python 3.7:**
sudo venv37/bin/python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"**Python 3.8:**
sudo venv38/bin/python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"
```

![](img/cc4fcd4d8f32ef8d3aec17b81be30a79.png)

## 打开 YoloV3-Tf2 目录:

1.  重新打开 Jupyter 笔记本
2.  单击“桌面”文件夹
3.  单击“YoloV3-Tf2”文件夹

![](img/c1c39e7356235f53fed7e6f3087ee5b9.png)

## 使用虚拟环境:

1.  刷新页面
2.  点击“新建”
3.  点击“yolov3-tf2”

![](img/c4d1d45bd4192b56c6cbbd26daeed189.png)

## 运行代码单元格:

1.  从这些指令下面复制代码
2.  单击 Jupyter 笔记本中的空白单元格
3.  将代码粘贴到空单元格中
4.  按“Shift”+“Enter”

![](img/8cda0c3026d25c4812e9ea7f11ed82cc.png)

## 运行代码单元格:

1.  从这些指令下面复制代码
2.  单击 Jupyter 笔记本中的空白单元格
3.  将代码粘贴到空单元格中
4.  按“Shift”+“Enter”

![](img/d99a24f05b38b5ef63864d111ddff55c.png)

## 停用虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
deactivate
```

![](img/d634d090a915515a740147e8abfb3ef3.png)

## 取消设置 Pyenv 版本环境变量:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
PYENV_VERSION=""
```

![](img/7f45cc6c1738a973cb0e6f771772bd11.png)

## 列出已安装的虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
jupyter kernelspec list
```

![](img/15d448a66011c845ea0dd3db30d10573.png)

## 卸载虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
sudo ~/.pyenv/shims/jupyter kernelspec uninstall yolov3-tf2
```

![](img/1f2c077764ac2e8430942a644a95b520.png)

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