# 在 Mac 上的 Jupyter 笔记本中安装虚拟环境

> 原文：<https://levelup.gitconnected.com/install-virtual-environments-in-jupyter-notebook-on-mac-557f23e55f99>

## 系列:人工智能

## 带有说明和屏幕截图的精简演练

![](img/f9f606d75a453258303d0c036ed05d1d.png)

图片由[西恩·佛利](https://unsplash.com/@_stfeyes)

> [扩展指南](https://medium.com/p/e3de97491b3a)使用术语和命令的定义来帮助您了解正在发生的事情。

## 开放终端:

1.  打开 web 浏览器
2.  输入 Jupyter 笔记本电脑服务器的 IP 地址
3.  按“回车键”
4.  点击“新建”
5.  点击“终端”

![](img/11fdeec3ba3966b82d73176fea045f30.png)

## 打开桌面目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
cd $HOME/desktop/
```

![](img/403f1b9598684d5ab286fe9a55e3bfdc.png)

## 克隆存储库:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
git clone --recursive [https://github.com/zzh8829/yolov3-tf2.git](https://github.com/zzh8829/yolov3-tf2.git)
```

![](img/98d59e867ed6874e8ba6f5a1dfe865b7.png)

## 打开 YoloV3-Tf2 目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
cd yolov3-tf2
```

![](img/e172074dcbf9c0dfd838052fe5f144d9.png)

## 设置 Pyenv 版本环境变量:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
PYENV_VERSION=3.6.8
```

![](img/e8e11fe81235eb2ae94aff58872189ab.png)

## 创建虚拟环境:

1.  从下面这些说明中找到 Python 版本
2.  复制提供的命令
3.  将命令粘贴到终端
4.  按“回车键”

```
**Python 3.5:** python -m venv venv35**Python 3.6: <----------**
python -m venv venv36**Python 3.7:**
python -m venv venv37**Python 3.8:**
python -m venv venv38
```

![](img/0b1914002da51a2194eea7153ba86147.png)

## 激活虚拟环境:

1.  从下面这些说明中找到 Python 版本
2.  复制提供的命令
3.  将命令粘贴到终端
4.  按“回车键”

```
**Python 3.5:**
[source](#768b) venv35/bin/activate**Python 3.6: <----------**
source venv36/bin/activate**Python 3.7:**
source venv37/bin/activate**Python 3.8:**
source venv38/bin/activate
```

![](img/aff576e635fd404d1bcf9ce1588ee201.png)

## 升级 Pip:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
python -m pip install --[upgrade](#33fe) pip
```

![](img/694e2d7de8f5123b08a1202e63b0f90d.png)

## 安装要求:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
python -m pip install --requirement requirements.txt
```

![](img/73454a66c5181d9a792584e9adec85d4.png)

## 安装自制软件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
/bin/bash -c "$(curl -fsSL [https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh](https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh))"
```

![](img/af191f35fa7ba9d95ae9f9be23535804.png)

## 安装 Wget:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
brew install wget
```

![](img/7357ce9bf66cdbd06abcbba7c9f0ab19.png)

## 下载重量:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
wget [https://pjreddie.com/media/files/yolov3.weights](https://pjreddie.com/media/files/yolov3.weights) -O data/yolov3.weights
```

![](img/5c3abfcdeaaeee9adadfe52ebb688365.png)

## 转换重量:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
python convert.py --weights ./data/yolov3.weights --output ./checkpoints/yolov3.tf
```

![](img/8b7acb8ccaf10263abd5b40bae52bf03.png)

## 使用存储库:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
venv36/bin/python detect.py --image ./data/meme.jpg
```

![](img/cd00657b68a0440a4f8fb93e3521dcaa.png)

## 安装 IPython 内核:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
python -m pip install ipykernel
```

![](img/89c6f6c9466882f6ad812c507f90eaac.png)

## 安装虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
**Python 3.5:**
sudo venv35/bin/python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"**Python 3.6: <----------**
sudo venv36/bin/python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"**Python 3.7:**
sudo venv37/bin/python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"**Python 3.8:**
sudo venv38/bin/python -m ipykernel install --name "yolov3-tf2" --display-name "yolov3-tf2"
```

![](img/1442866fe816b08fde5357d30b6bb333.png)

## 打开 YoloV3-Tf2 目录:

1.  重新打开 Jupyter 笔记本
2.  单击“桌面”文件夹
3.  单击“YoloV3-Tf2”文件夹

![](img/81492af2e84a15a9d2920680c87acd24.png)

## 使用虚拟环境:

1.  刷新页面
2.  点击“新建”
3.  点击“yolov3-tf2”

![](img/8b3294499c18ec2c979a6c88678980d1.png)

## 运行代码单元格:

1.  从这些指令下面复制代码
2.  单击 Jupyter 笔记本中的空白单元格
3.  将代码粘贴到空单元格中
4.  按“Shift”+“Enter”

![](img/90160ef202049a8c626e47194602d45c.png)

## 运行代码单元格:

1.  从这些指令下面复制代码
2.  单击 Jupyter 笔记本中的空白单元格
3.  将代码粘贴到空单元格中
4.  按“Shift”+“Enter”

![](img/f9dfac21ee2728e8f34dc246c0ccce06.png)

## 停用虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
deactivate
```

![](img/f2761a96ff0133779554ddf6c6d6514a.png)

## 取消设置 Pyenv 版本环境变量:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
PYENV_VERSION=""
```

![](img/d759dcb6065dd2b9710736ac4526d4ce.png)

## 列出已安装的虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
jupyter kernelspec list
```

![](img/53b64196c78b4b6af684d5fba1612ee2.png)

## 卸载虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
sudo ~/.pyenv/shims/jupyter kernelspec uninstall yolov3-tf2
```

![](img/67bd832deabc18530e371693f2efa9e2.png)

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