# 如何在 Windows 上安装和管理多个 Python 版本

> 原文：<https://levelup.gitconnected.com/how-to-set-up-virtual-environments-using-different-python-versions-on-windows-153f8258be0f>

## 编程:

## 漂亮简单的教程，一步一步的指导

![](img/6868d07b3d5a36ab064f0cf73272c149.png)

图片由[大卫·克洛德](https://unsplash.com/@davidclode)拍摄

## 总结:

本文使用一种简单的技术来安装和管理多个 python 版本，而不使用 PyEnv。它安装 python 的每个主要版本，将每个版本添加到 path 环境变量中，并重命名可执行文件以包含版本号。它还使用一个重命名的 python 可执行文件来创建、激活和操作虚拟环境。

*   PyEnv 将是管理多个 python 版本的最佳方式，但它在 Windows 上不受支持，所以这是我发现的最佳替代方案。

## 目录:

1.  [准备安装](#e3f5)
2.  [安装 Python 3.5](#bc0b)
3.  [安装 Python 3.6](#d715)
4.  [安装 Python 3.7](#fb05)
5.  [安装 Python 3.8](#38ef)
6.  [安装 Python 3.9](#e45c)
7.  [安装需求](#b429)
8.  [创建虚拟环境](#61e7)

## 附录:

1.  [教程:人工智能设置](#50ec)
2.  [教程:人工智能课程](#c3c2)
3.  [教程:人工智能知识库](#e533)

## 准备安装:

本节允许脚本在 powershell 中运行，创建 python 目录，打开 python 目录，并备份 path 环境变量。

```
**# open the powershell shell**
1\. press “⊞ windows”
2\. enter “powershell” into the search bar
3\. right-click "windows powershell"
4\. click “run as administrator”**# allow scripts to run in powershell**
set-executionpolicy unrestricted -force**# creates the python directory**
mkdir -p $home\appdata\local\programs\python**# open the python directory**
cd $home\appdata\local\programs\python**# backup the path environment variable**
[system.environment]::getenvironmentvariable('path','user') > path-environment-variable-backup.txt
```

## 安装 Python 3.5:

本节下载并安装 python 3.5，将 python 3.5 添加到 path 环境变量中，并重命名 python 3.5 可执行文件。

```
**# download python 3.5**
invoke-webrequest -uri [https://www.python.org/ftp/python/3.5.4/python-3.5.4-amd64.exe](https://www.python.org/ftp/python/3.5.4/python-3.5.4-amd64.exe) -outfile $home/downloads/python-3.5.4.exe**# install python 3.5** invoke-item $home\downloads\python-3.5.4.exe**# add python 3.5 to the path environment variable** $new_paths = "$home\appdata\local\programs\python\python35\scripts\;
$home\appdata\local\programs\python\python35\"; $old_paths = [system.environment]::getenvironmentvariable('path','user'); [environment]::setenvironmentvariable("path", "$new_paths; $old_paths", "user")**# rename the python 3.5 executable file:**
copy python35\python.exe python35\python35.exe
```

## 安装 Python 3.6:

这一节的内容与上一节相同，但使用的是 python 3.6。

```
**# download python 3.6**
invoke-webrequest -uri [https://www.python.org/ftp/python/3.6.8/python-3.6.8-amd64.exe](https://www.python.org/ftp/python/3.6.8/python-3.6.8-amd64.exe) -outfile $home/downloads/python-3.6.8.exe**# install python 3.6** invoke-item $home\downloads\python-3.6.8.exe**# add python 3.6 to the path environment variable** $new_paths = "$home\appdata\local\programs\python\python36\scripts\;
$home\appdata\local\programs\python\python36\"; $old_paths = [system.environment]::getenvironmentvariable('path','user'); [environment]::setenvironmentvariable("path", "$new_paths; $old_paths", "user")**# rename the python 3.6 executable file:**
copy python36\python.exe python36\python36.exe
```

## 安装 Python 3.7:

这一节的内容与上一节相同，但使用的是 python 3.7。

```
**# download python 3.7**
invoke-webrequest -uri [https://www.python.org/ftp/python/3.7.9/python-3.7.9-amd64.exe](https://www.python.org/ftp/python/3.7.9/python-3.7.9-amd64.exe) -outfile $home/downloads/python-3.7.9.exe**# install python 3.7** invoke-item $home\downloads\python-3.7.9.exe**# add python 3.7 to the path environment variable** $new_paths = "$home\appdata\local\programs\python\python37\scripts\;
$home\appdata\local\programs\python\python37\"; $old_paths = [system.environment]::getenvironmentvariable('path','user'); [environment]::setenvironmentvariable("path", "$new_paths; $old_paths", "user")**# rename the python 3.7 executable file:**
copy python37\python.exe python37\python37.exe
```

## 安装 Python 3.8:

这一节的内容与上一节相同，但使用的是 python 3.8。

```
**# download python 3.8**
invoke-webrequest -uri [https://www.python.org/ftp/python/3.8.6/python-3.8.6-amd64.exe](https://www.python.org/ftp/python/3.8.6/python-3.8.6-amd64.exe) -outfile $home/downloads/python-3.8.6.exe**# install python 3.8** invoke-item $home\downloads\python-3.8.6.exe**# add python 3.8 to the path environment variable** $new_paths = "$home\appdata\local\programs\python\python38\scripts\;
$home\appdata\local\programs\python\python38\"; $old_paths = [system.environment]::getenvironmentvariable('path','user'); [environment]::setenvironmentvariable("path", "$new_paths; $old_paths", "user")**# rename the python 3.8 executable file:**
copy python38\python.exe python38\python38.exe
```

## 安装 Python 3.9:

这一节的内容与上一节相同，但使用的是 python 3.9。

```
**# download python 3.9**
invoke-webrequest -uri [https://www.python.org/ftp/python/3.9.6/python-3.9.6-amd64.exe](https://www.python.org/ftp/python/3.9.6/python-3.9.6-amd64.exe) -outfile $home/downloads/python-3.9.6.exe**# install python 3.9** invoke-item $home\downloads\python-3.9.6.exe**# add python 3.9 to the path environment variable** $new_paths = "$home\appdata\local\programs\python\python39\scripts\;
$home\appdata\local\programs\python\python39\"; $old_paths = [system.environment]::getenvironmentvariable('path','user'); [environment]::setenvironmentvariable("path", "$new_paths; $old_paths", "user")**# rename the python 3.9 executable file:**
copy python39\python.exe python39\python39.exe
```

## 安装要求:

本节重新加载 path 环境变量，在每个 python 版本上安装 virtualenv 库，并打开桌面目录。

```
**# reload the environment variables**
$env:path = [system.environment]::getenvironmentvariable("path","machine") + ";" + [system.environment]::getenvironmentvariable("path","user")**# install virtualenv on python 3.5**
python35 -m pip install virtualenv**# install virtualenv on python 3.6**
python36 -m pip install virtualenv**# install virtualenv on python 3.7**
python37 -m pip install virtualenv**# install virtualenv on python 3.8**
python38 -m pip install virtualenv**# install virtualenv on python 3.9**
python39 -m pip install virtualenv**# navigate to the desktop directory**
cd $home\desktop
```

## 创建虚拟环境:

该部分创建、激活和操作虚拟环境。

```
**# create the virtual environment with the desired python version** python -m virtualenv -p python35 venv35**# activate the virtual environment** venv35\scripts\activate**# check the python version**
python --version**# check the location of the python executable file** (get-command python).path**# install a package using the pip package manager** python -m pip install numpy**# list all the packages that are currently installed** python -m pip list**# uninstall a package using the pip package manager** python -m pip uninstall --yes scipy**# list all the packages that are currently installed** python -m pip list**# deactivate the python virtual environment** deactivate
```

> "最后，记得按住鼓掌按钮，订阅帮助，并获得定期更新."

## 附录:

这个博客的存在是为了提供完整的解决方案，回答你的问题，加速你与人工智能相关的进步。它提供了设置计算机和完成 fastai 课程前半部分所需的一切。它将让你接触到人工智能子领域中最先进的知识库。它也将涵盖 fastai 课程的后半部分。

## 教程:人工智能设置

本节提供了设置电脑所需的一切。

```
**# linux**
01\. [install and manage multiple python versions](https://medium.com/p/916990dabe4b)
02\. [install the nvidia cuda driver, toolkit, cudnn, and tensorrt](https://medium.com/p/cd5b3a4f824)
03\. [install the jupyter notebook server](https://medium.com/p/b2c14c47b446)
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
02\. [install the jupyter notebook server](https://medium.com/p/2a276f679e0)
03\. [install virtual environments in jupyter notebook](https://medium.com/p/e3de97491b3a)
04\. [install the python environment for ai and machine learning](https://medium.com/p/2b2353d7bcc3)
05\. [install the fastai course requirements](https://medium.com/p/90fdd524bc82)
```

## 教程:人工智能课程

本部分包含每课结束时对问卷的回答。

```
**# fastai** 01\. [chapter 1: your deep learning journey q&](https://medium.com/p/6f266bdb1340/)a
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