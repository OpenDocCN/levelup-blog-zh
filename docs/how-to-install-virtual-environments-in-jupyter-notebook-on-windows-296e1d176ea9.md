# 如何在 Windows 上的 Jupyter 笔记本中安装虚拟环境

> 原文：<https://levelup.gitconnected.com/how-to-install-virtual-environments-in-jupyter-notebook-on-windows-296e1d176ea9>

## 编程:

## 漂亮简单的教程，一步一步的指导

![](img/81a045d621e0a955244b62e42425fc31.png)

图片由[斯蒂芬](https://unsplash.com/@wanderingstef)

## 总结:

本文在 Jupyter 笔记本中安装了一个虚拟环境。它克隆一个任意的存储库，并在 Jupyter Notebook 中安装创建和安装虚拟环境所需的包。它创建并激活虚拟环境，并安装存储库所需的软件包。它还在 Jupyter Notebook 中安装、使用和删除虚拟环境。

## 目录:

1.  [准备法斯泰图书仓库](#b5ef)
2.  [创建虚拟环境](#9c01)
3.  [安装需求](#7e89)
4.  [安装虚拟环境](#50a8)
5.  [使用虚拟环境](#4b9e)
6.  [移除虚拟环境](#66b4)

## 附录:

1.  [教程:人工智能设置](#50ec)
2.  [教程:人工智能课程](#c3c2)
3.  [教程:人工智能库](#e533)

## 准备 Fastai 图书存储库:

本节安装 fastbook 存储库、virtualenv 和 ipykernel。

```
**# open the powershell shell**
1\. press “⊞ windows”
2\. enter “powershell” into the search bar
3\. right-click "windows powershell"
4\. click “run as administrator”**# open the desktop directory** cd $home\desktop\**# clone the fastai book repository** git clone --recursive [https://github.com/fastai/fastbook.git](https://github.com/fastai/fastbook.git)**# open the fastai book directory** cd fastbook**# install virtualenv to create the virtual environment** python -m pip install virtualenv**# install ipykernel to install the virtual environments** python -m pip install ipykernel
```

## 创建虚拟环境:

本节创建并激活虚拟环境。

```
**# create the virtual environment**
python -m virtualenv venv**# activate the virtual environment** venv\scripts\activate
```

## 安装要求:

本节将升级 pip 包管理器，创建需求文件，并从文件中安装使用存储库所需的包。

```
**# upgrade the pip package manager** python -m pip install --upgrade pip**# create the requirements file** set-content "$home\desktop\fastbook\requirements.txt" -encoding ascii "--find-links https://download.pytorch.org/whl/torch_stable.html`ntorchvision==0.8.2`n--find-links [https://download.pytorch.org/whl/torch_stable.html`n](https://download.pytorch.org/whl/torch_stable.html\nfastai==2.2.7\ngraphviz)torch==1.7.1`[nfastai==2.2.7](https://download.pytorch.org/whl/torch_stable.html\nfastai==2.2.7\ngraphviz)`nfastbook`n[graphviz](https://download.pytorch.org/whl/torch_stable.html\nfastai==2.2.7\ngraphviz)`nkaggle`nwaterfallcharts`ntreeinterpreter`ndtreeviz`n"**# install the requirements** 
python -m pip install -r requirements.txt
```

## 安装虚拟环境:

本节将虚拟环境安装到 Jupyter 笔记本电脑中。

```
**# install the virtual environment**
venv\scripts\python -m ipykernel install --name "fastai-pytorch" --display-name "fastai"
```

## 使用虚拟环境:

这个部分访问 Jupyter 笔记本服务器，打开 Jupyter 笔记本文件，加载虚拟环境，并运行代码单元。

```
**# store the private ip address**
$private_ip_address = [regex]::new('\d*\.\d*\.\d*\.\d*').match($(ipconfig)).value**# store the url to the server**
$url = "https://$private_ip_address`:8887"**# access the jupyter notebook server** start-process iexplore $url**# bypass the unnecessary "connection" warning** 1\. click anywhere on the website
2\. type "thisisunsafe"**# log into the server**
1\. enter the server password
2\. click "log in"**# load the jupyter notebook file**
1\. click "desktop"
2\. click "fastai"
3\. click "clean"
4\. click "01_intro.ipynb"**# load the virtual environment**
1\. click "kernel" menu
2\. click "change kernel"
3\. click "fastai"**# run the code cells with the virtual environment** 1\. click the first code cell
2\. press "Shift" + "Enter"
3\. click the next code cell
4\. press "Shift" + "Enter"
5\. repeat
```

## 删除虚拟环境:

本节列出了 Jupyter Notebook 中当前安装的虚拟环境，并卸载了指定的虚拟环境。

```
**# list the installed virtual environments** jupyter kernelspec list**# uninstall the virtual environment**
jupyter kernelspec uninstall fastai-pytorch
```

> "最后，记得订阅并按住鼓掌按钮，以获得定期更新和帮助."

## 附录:

这个博客的存在是为了提供完整的解决方案，回答你的问题，加速你与人工智能相关的进步。它提供了设置计算机和完成 fastai 课程前半部分所需的一切。它将让你接触到人工智能子领域中最先进的知识库。它也将涵盖 fastai 课程的后半部分。

## 教程:人工智能设置

本节提供了设置电脑所需的一切。

```
**# linux**
01\. [install and manage multiple python versions](https://medium.com/p/916990dabe4b)
02\. [install the nvidia cuda driver, toolkit, cudnn, and tensorrt](https://medium.com/p/cd5b3a4f824)
03\. [install the jupyter notebook home and public server](https://medium.com/p/b2c14c47b446)
04\. [install virtual environments in jupyter notebook](https://medium.com/p/1556c8655506)
05\. [install the python environment for ai and machine learning](https://medium.com/p/765678fcb4fb)
06\. [install the fastai course requirements](https://medium.com/p/116415a9df22/)**# wsl 2**
01\. [install windows subsystem for linux 2](https://medium.com/p/8ef0e1538052/)
02\. [install and manage multiple python versions](https://medium.com/p/1131c4e50a58)
03\. [install the nvidia cuda driver, toolkit, cudnn, and tensorrt](https://medium.com/p/9800abd74409) 
04\. [install the jupyter notebook home and public server](https://medium.com/p/7c96b3705df1)
05\. [install virtual environments in jupyter notebook](https://medium.com/p/3e6bf456041b)
06\. [install the python environment for ai and machine learning](https://medium.com/p/612240cb8c0c)
07\. [install ubuntu desktop with a graphical user interface](https://medium.com/p/95911ee2997f)
08\. [install the fastai course requirements](https://medium.com/p/15a77fc7e301/)**# windows 10**
01\. [install and manage multiple python versions](https://medium.com/p/153f8258be0f/)
02\. [install the nvidia cuda driver, toolkit, cudnn, and tensorrt](https://medium.com/p/af58647b6d9a/) 03\. [install the jupyter notebook home and public server](https://medium.com/p/9bfcf39ee2a7/)
04\. [install virtual environments in jupyter notebook](https://medium.com/p/296e1d176ea9/)
05\. [install the programming environment for ai and machine learning](https://medium.com/p/8d1bc340c1c5/)**# mac** 01\. [install and manage multiple python versions](https://medium.com/p/ca01a5e398d4)
02\. [install the jupyter notebook home and public server](https://medium.com/p/2a276f679e0)
03\. [install virtual environments in jupyter notebook](https://medium.com/p/e3de97491b3a)
04\. [install the python environment for ai and machine learning](https://medium.com/p/2b2353d7bcc3)
05\. [install the fastai course requirements](https://medium.com/p/90fdd524bc82)
```

## 教程:人工智能课程

本部分包含每课结束时对问卷的回答。

```
**# fastai course** 01\. [chapter 1: your deep learning journey q&](https://medium.com/p/6f266bdb1340/)a
02\. [chapter 2: from model to production q&a](https://medium.com/p/5a0902207f5b)
03\. [chapter 3: data ethics q&a](https://medium.com/p/501bb37ca30d)
04\. [chapter 4: under the hood: training a digit classifier q&a](https://medium.com/p/89077906197e/)
05\. [chapter 5: image classification q&a](https://medium.com/p/aa7cacdeab1/)
06\. [chapter 6: other computer vision problems q&a](https://medium.com/p/aa7cacdeab1/)
07\. [chapter 7: training a state-of-the-art model q&a](https://medium.com/p/6f6dcc83dd9f/)
08\. [chapter 8: collaborative filtering deep dive q&a](https://medium.com/p/52d3583d626b/)
```

## 教程:人工智能库

这个部分包含不同子领域中的最先进的知识库。

```
**# repositories related to audio** 01\. [raise audio quality using nu-wave](https://medium.com/p/e3dd979056e0/)
02\. [change voices using maskcyclegan-vc](https://medium.com/p/8bdfeb1faecb/)
03\. [clone voices using real-time-voice-cloning toolbox](https://medium.com/p/7b8609438001/)**# repositories related to images**
01\. [achieve 90% accuracy using facedetection-dsfd](https://medium.com/p/9c9fefb3f863/)
```