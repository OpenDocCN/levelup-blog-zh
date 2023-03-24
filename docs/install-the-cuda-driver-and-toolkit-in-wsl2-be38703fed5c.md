# 在 WSL2 中安装 CUDA 驱动程序和工具包

> 原文：<https://levelup.gitconnected.com/install-the-cuda-driver-and-toolkit-in-wsl2-be38703fed5c>

## 系列:人工智能

## 附有说明和截图的简明指南

![](img/00d7c762f314c6f5a908866fd7b2dd81.png)

图片由 [Denys Nevozhai](https://unsplash.com/@dnevozhai)

> [扩展指南](https://medium.com/p/9800abd74409)使用术语和命令的定义来帮助您了解正在发生的事情。

## 加入 NVIDIA 开发者计划:

1.  访问[官网](https://developer.nvidia.com/developer-program)。
2.  点击“立即加入”
3.  输入电子邮件地址
4.  点击“下一步”
5.  点击“创建账户”
6.  输入用户信息
7.  点击“创建账户”
8.  验证电子邮件地址
9.  点击“提交”
10.  输入用户信息
11.  点击“提交”

![](img/ad5b1620b86804735e4399534234715a.png)

## 下载 NVIDIA CUDA 驱动程序:

1.  访问[官网](https://developer.nvidia.com/cuda/wsl)
2.  点击“获取 CUDA 驱动程序”
3.  点击“立即下载”

![](img/d3bf88fa85c44200c61a9f73a9be419c.png)

## 安装 NVIDIA CUDA 驱动程序:

1.  打开“460.15 _ gameready _ win 10-dch _ 64 bit _ international . exe”
2.  单击“确定”
3.  选择“NVIDIA 图形驱动程序”
4.  点击“同意并继续”
5.  点击“下一步”
6.  点击“关闭”

![](img/0e63076334a708ba10d1565a1b9eb7bf.png)

## 打开 PowerShell:

1.  按下“⊞之窗”
2.  在搜索栏中输入“PowerShell”
3.  单击“以管理员身份运行”

![](img/85d08ab3ee16b35cee1afde54cb86555.png)

## 更新 WSL2:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

WSL2 版本号必须是 4.19.121 或更高版本。

```
wsl --update
```

![](img/5d938f3f22f0a8737766ccc0e77348f8.png)

## 关闭 WSL2:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
wsl --shutdown
```

![](img/04707ebc9999ad5e023b7da2065f7929.png)

## 打开 WSL2:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
wsl
```

![](img/a025366fc6ae2dc7654174992caefacb.png)

## 获取 NVIDIA 公钥:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt-key adv --fetch-keys [http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub](http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub)
```

![](img/51fa43af385e3995269a13017b07507c.png)

## 将 NVIDIA 添加到源列表目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo sh -c 'echo "deb [http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64](http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64) /" > /etc/apt/sources.list.d/cuda.list'
```

![](img/2640932bd3f5abe38b3095fd567238e8.png)

## 更新来源列表和来源列表目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt-get update
```

![](img/8ef06045f34537411c663311b3478838.png)

## 安装 NVIDIA CUDA 工具包 11:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt-get --yes install cuda-toolkit-11-0 cuda-toolkit-10-2
```

![](img/159ef14156be22bae0eb6abdfc65ec56.png)

## 将 NVIDIA 添加到源列表目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo sh -c 'echo "deb [http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64](http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64) /" > /etc/apt/sources.list.d/nvidia-machine-learning.list'
```

![](img/435084bc450f1c47d8bc572b478c591d.png)

## 更新来源列表和来源列表目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt-get update
```

![](img/9b55ad9e70f34f115718542ac3c0f68f.png)

## 安装 CUDA 和 cuDDN 库:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt-get install --yes --no-install-recommends cuda-11-0 libcudnn8=8.0.5.39-1+cuda11.0 libcudnn8-dev=8.0.5.39-1+cuda11.0
```

![](img/a0772b341efde35373a41b8758674c13.png)

## 安装 TensorRT 库:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt-get install --yes --no-install-recommends libnvinfer7=7.1.3-1+cuda11.0 libnvinfer-dev=7.1.3-1+cuda11.0 libnvinfer-plugin7=7.1.3-1+cuda11.0
```

![](img/1bf4b591e5a804ed5ae379c58dded025.png)

## 打开 BlackScholes 目录:

1.  从下面这些说明中找到 Python 版本
2.  复制提供的命令
3.  将命令粘贴到 PowerShell 中
4.  按“回车”

```
cd /usr/local/cuda-11.0/samples/4_Finance/BlackScholes
```

![](img/6886118e1fc5d97f83aeecb36a1d7d19.png)

## 运行 MakeFile:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo make
```

![](img/e194d182de2dedb68f8dcde22baa4fc1.png)

## 运行示例:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
./BlackScholes
```

![](img/e0c095071fea9e2b10e861abbb2cc2e1.png)

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