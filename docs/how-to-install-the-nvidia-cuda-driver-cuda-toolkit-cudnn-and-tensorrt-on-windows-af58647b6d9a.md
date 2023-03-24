# 如何在 Windows 上安装 NVIDIA CUDA 驱动程序、CUDA 工具包、CuDNN 和 TensorRT

> 原文：<https://levelup.gitconnected.com/how-to-install-the-nvidia-cuda-driver-cuda-toolkit-cudnn-and-tensorrt-on-windows-af58647b6d9a>

## 编程:

## 漂亮简单的教程，一步一步的指导

![](img/827f74c96dd5ce62d6a614f3a95529af.png)

图片由[任然](https://unsplash.com/@renran)

## 总结:

本文安装了使用 NVIDIA GPUs 训练模型和运行批量推理所需的驱动程序和程序。它下载并安装 CUDA 驱动程序、CUDA 工具包和 CUDA 工具包更新。它下载、解压并移动 CuDNN 和 TensorRT 文件到 CUDA 目录中。它还配置、构建和运行 BlackScholes 示例来测试 GPU。

## 目录:

1.  [安装要求](#74da)
2.  [安装 CUDA 驱动](#4aab)
3.  [安装 CUDA 工具包 10](#8a8a)
4.  [安装 CUDA 工具包 11](#39ea)
5.  [安装 CuDNN 库](#078b)
6.  [安装 TensorRT 库](#cc54)
7.  [在 CUDA 样本上测试 GPU](#8d01)

## 附录:

1.  [教程:人工智能设置](#50ec)
2.  [教程:人工智能课程](#c3c2)
3.  教程:人工智能库

## 安装要求:

本节下载并安装支持 C 和 C++的 Visual Studio。

```
**# open the powershell shell** 1\. press “⊞ windows”
2\. enter “powershell” into the search bar
3\. right-click "windows powershell"
4\. click “run as administrator”**# download the visual studio 2019 installer**
invoke-webrequest -outfile "$home\downloads\vsc.exe" -uri [https://download.visualstudio.microsoft.com/download/pr/45dfa82b-c1f8-4c27-a5a0-1fa7a864ae21/9dd77a8d1121fd4382494e40840faeba0d7339a594a1603f0573d0013b0f0fa5/vs_Community.exe](https://download.visualstudio.microsoft.com/download/pr/45dfa82b-c1f8-4c27-a5a0-1fa7a864ae21/9dd77a8d1121fd4382494e40840faeba0d7339a594a1603f0573d0013b0f0fa5/vs_Community.exe)**# open the visual studio 2019 installer**
invoke-item "$home\downloads\vsc.exe"**# install visual studio 2019**
1\. check “desktop development with c++”
2\. click "install"
```

## 安装 CUDA 驱动程序:

本节下载并安装当时最新的 CUDA 驱动程序。

```
**# download the cuda driver installer**
invoke-webrequest -outfile "$home\downloads\cuda_driver.exe" -uri [https://us.download.nvidia.com/Windows/471.68/471.68-desktop-win10-win11-64bit-international-nsd-dch-whql.exe](https://us.download.nvidia.com/Windows/471.68/471.68-desktop-win10-win11-64bit-international-nsd-dch-whql.exe)**# open the cuda driver installer**
invoke-item "$home\downloads\cuda_driver.exe"**# install the cuda driver**
1\. select “nvidia graphics driver”
2\. click "agree & continue"
3\. click "next"
```

## 安装 CUDA 工具包 10:

本节下载并安装 CUDA Toolkit 10 和更新。

```
**# download the cuda toolkit 10 installer**
invoke-webrequest -outfile "$home\downloads\cuda_toolkit_10.exe" [https://developer.download.nvidia.com/compute/cuda/10.2/Prod/network_installers/cuda_10.2.89_win10_network.exe](https://developer.download.nvidia.com/compute/cuda/10.2/Prod/network_installers/cuda_10.2.89_win10_network.exe)**# open the cuda toolkit 10 installer** invoke-item "$home\downloads\cuda_toolkit_10.exe"**# install cuda toolkit 10** 1\. click "agree & continue"
2\. click "next"
3\. select custom (advanced)
4\. click "next"
5\. uncheck “nvidia geforce experience components”
6\. uncheck “driver components”
7\. uncheck “other components”
8\. click "next"**# download the cuda 10 update 1installer** invoke-webrequest -outfile "$home\downloads\cuda_10_update_1.exe" [https://developer.download.nvidia.com/compute/cuda/10.2/Prod/patches/1/cuda_10.2.1_win10.exe](https://developer.download.nvidia.com/compute/cuda/10.2/Prod/patches/1/cuda_10.2.1_win10.exe)**# open the cuda 10 update 1 installer** invoke-item "$home\downloads\cuda_10_update_1.exe"**# install the cuda 10 update 1**
1\. click "agree & continue"
2\. click "next"**# download the cuda 10 update 2 installer** invoke-webrequest -outfile "$home\downloads\cuda_10_update_2.exe" [https://developer.download.nvidia.com/compute/cuda/10.2/Prod/patches/2/cuda_10.2.2_win10.exe](https://developer.download.nvidia.com/compute/cuda/10.2/Prod/patches/2/cuda_10.2.2_win10.exe)**# open the cuda 10 update 2 installer** invoke-item "$home\downloads\cuda_10_update_2.exe"**# install the cuda 10 update 2**
1\. click "agree & continue"
2\. click "next"
```

## 安装 CUDA 工具包 11:

本节下载并安装 CUDA Toolkit 11。

```
**# download the cuda toolkit 11 installer**
invoke-webrequest -outfile "$home\downloads\cuda_toolkit_11.exe" [https://developer.download.nvidia.com/compute/cuda/11.4.1/network_installers/cuda_11.4.1_win10_network.exe](https://developer.download.nvidia.com/compute/cuda/11.4.1/network_installers/cuda_11.4.1_win10_network.exe)**# open the cuda toolkit 11 installer** invoke-item "$home\downloads\cuda_toolkit_11.exe"**# install cuda toolkit 11** 1\. click "agree & continue"
2\. click "next"
3\. select custom (advanced)
4\. click "next"
5\. uncheck “nvidia geforce experience components”
6\. uncheck “driver components”
7\. uncheck “other components”
8\. click "next"
```

## 安装 CuDNN 库:

本节加入 NVIDIA 开发者计划，下载 CuDNN 库，并将文件解压缩并移动到 CUDA 目录中。

```
**# join the nvidia developer program**
start-process iexplore "[https://developer.nvidia.com/developer-program](https://developer.nvidia.com/developer-program)"**# download the cudnn library for cuda toolkit 10** start-process iexplore [https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.2.2/10.2_07062021/cudnn-10.2-windows10-x64-v8.2.2.26.zip](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.2.2/10.2_07062021/cudnn-10.2-windows10-x64-v8.2.2.26.zip)**# unzip the cudnn library for cuda toolkit 10**
expand-archive "$home\downloads\cudnn-10.2-windows10-x64-v8.2.2.26.zip" -destinationpath "$home\downloads\cudnn_cuda_toolkit_10\"**# move the dll files**
move-item "$home\downloads\cudnn_cuda_toolkit_10\cuda\bin\cudnn*.dll" "c:\program files\nvidia gpu computing toolkit\cuda\v10.2\bin\"**# move the h files** move-item "$home\downloads\cudnn_cuda_toolkit_10\cuda\include\cudnn*.h" "c:\program files\nvidia gpu computing toolkit\cuda\v10.2\include\"**# move the lib files** move-item "$home\downloads\cudnn_cuda_toolkit_10\cuda\lib\x64\cudnn*.lib" "c:\program files\nvidia gpu computing toolkit\cuda\v10.2\lib\x64"**# download the cudnn library for cuda toolkit 11** start-process iexplore [https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.2.2/11.4_07062021/cudnn-11.4-windows-x64-v8.2.2.26.zip](https://developer.nvidia.com/compute/machine-learning/cudnn/secure/8.2.2/11.4_07062021/cudnn-11.4-windows-x64-v8.2.2.26.zip)**# unzip the cudnn library for cuda toolkit 11**
expand-archive "$home\downloads\cudnn-11.4-windows-x64-v8.2.2.26.zip" -destinationpath "$home\downloads\cudnn_cuda_toolkit_11\"**# move the dll files**
move-item "$home\downloads\cudnn_cuda_toolkit_11\cuda\bin\cudnn*.dll" "c:\program files\nvidia gpu computing toolkit\cuda\v11.4\bin\"**# move the h files** move-item "$home\downloads\cudnn_cuda_toolkit_11\cuda\include\cudnn*.h" "c:\program files\nvidia gpu computing toolkit\cuda\v11.4\include\"**# move the lib files** move-item "$home\downloads\cudnn_cuda_toolkit_11\cuda\lib\x64\cudnn*.lib" "c:\program files\nvidia gpu computing toolkit\cuda\v11.4\lib\x64"
```

## 安装 TensorRT 库:

本节下载 TensorRT 库，将文件解压缩并移动到 CUDA 目录中，并安装几个必需的 python 程序。

```
**# download** **the tensorrt library for cuda toolkit 10** start-process iexplore [https://developer.nvidia.com/compute/machine-learning/tensorrt/secure/8.0.1/zip/tensorrt-8.0.1.6.windows10.x86_64.cuda-10.2.cudnn8.2.zip](https://developer.nvidia.com/compute/machine-learning/tensorrt/secure/8.0.1/zip/tensorrt-8.0.1.6.windows10.x86_64.cuda-10.2.cudnn8.2.zip)**# unzip the tensorrt library for cuda 10** expand-archive "$home\downloads\[tensorrt-8.0.1.6.windows10.x86_64.cuda-10.2.cudnn8.2.zip](https://developer.nvidia.com/compute/machine-learning/tensorrt/secure/8.0.1/zip/tensorrt-8.0.1.6.windows10.x86_64.cuda-10.2.cudnn8.2.zip)" "$home\downloads\tensorrt_cuda_toolkit_10\"**# move the dll files**
move-item "$home\downloads\tensorrt_cuda_toolkit_10\tensorrt-8.0.1.6\lib\*.dll" "c:\program files\nvidia gpu computing toolkit\cuda\v10.2\bin\"**# download** **the tensorrt library for cuda toolkit 11**
start-process iexplore [https://developer.nvidia.com/compute/machine-learning/tensorrt/secure/8.0.1/zip/tensorrt-8.0.1.6.windows10.x86_64.cuda-11.3.cudnn8.2.zip](https://developer.nvidia.com/compute/machine-learning/tensorrt/secure/8.0.1/zip/tensorrt-8.0.1.6.windows10.x86_64.cuda-11.3.cudnn8.2.zip)**# unzip the tensorrt library for cuda 11** expand-archive "$home\downloads\[tensorrt-8.0.1.6.windows10.x86_64.cuda-11.3.cudnn8.2.zip](https://developer.nvidia.com/compute/machine-learning/tensorrt/secure/8.0.1/zip/tensorrt-8.0.1.6.windows10.x86_64.cuda-11.3.cudnn8.2.zip)" "$home\downloads\tensorrt_cuda_toolkit_11\"**# move the dll files**
move-item "$home\downloads\tensorrt_cuda_toolkit_11\tensorrt-8.0.1.6\lib\*.dll" "c:\program files\nvidia gpu computing toolkit\cuda\v11.4\bin\"**# install graph surgeon**
python -m pip install "$home\downloads\tensorrt_cuda_toolkit_11\tensorrt-8.0.1.6\graphsurgeon\graphsurgeon-0.4.5-py2.py3-none-any.whl"**# install onnx graph surgeon** python -m pip install "$home\downloads\tensorrt_cuda_toolkit_11\tensorrt-8.0.1.6\onnx_graphsurgeon\onnx_graphsurgeon-0.3.10-py2.py3-none-any.whl"**# install universal framework format**
python -m pip install "$home\downloads\tensorrt_cuda_toolkit_11\tensorrt-8.0.1.6\uff\uff-0.6.9-py2.py3-none-any.whl"
```

## 在 CUDA 示例上测试 GPU:

本节配置、构建和运行 BlackScholes 示例。

```
**# open the visual studio file**
start-process "c:\programdata\nvidia corporation\cuda samples\v11.4\4_finance\blackscholes\blackscholes_vs2019.sln"**# edit the linker input properties** 1\. click the "project" menu
2\. click "properties"
3\. double-click "linker"
4\. click "input"
5\. click "additional dependencies"
6\. click the "down arrow" button
7\. click "edit"**# add the cudnn library** 1\. type "cudnn.lib" at the bottom of the additional dependencies
2\. click "ok"**# add the cuda toolkit 11 directory** 1\. click "cuda c/c++"
2\. double-click "cuda toolkit custom dir"
3\. enter "c:\program files\nvidia gpu computing toolkit\cuda\v11.4"
4\. click "ok"**# build the sample**
1\. click the “build” menu
2\. click “build solution”**# run the sample** cmd /k "c:\programdata\nvidia corporation\cuda samples\v11.4\bin\win64\debug\blackscholes.exe"
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