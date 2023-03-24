# 在 Mac 上安装多个 Python 版本

> 原文：<https://levelup.gitconnected.com/install-multiple-python-versions-on-mac-a58b1966825f>

## 系列:人工智能

## 带有说明和屏幕截图的精简演练

![](img/35524f222782465ca79aba997ca6ea8e.png)

图片由[程峰](https://unsplash.com/@chengfengrecord)

> [扩展指南](https://medium.com/p/ca01a5e398d4)使用术语和命令的定义来帮助您了解正在发生的事情。

## 开放终端:

1.  按“命令⌘ +空格键”
2.  输入“终端”
3.  按“回车键”

![](img/cc81b0c20dd1e9643c43dee8744228bf.png)

## 检查默认版本:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
python --version
```

![](img/1a653ff829950af6e163169177239178.png)

## 检查可执行文件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
which python
```

![](img/3c1056fca6072676b5d23e59171e495e.png)

## 安装自制软件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
/bin/bash -c "$(curl -fsSL [https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh](https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh))"
```

![](img/dc3e5821465f10162022f0b1dafe3657.png)

## 安装 Pyenv:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
brew install pyenv
```

![](img/6cec56ec9f3612765523eb77a9511d56.png)

## 安装 Pyenv 依赖项:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
brew install openssl readline sqlite3 xz zlib
```

![](img/0cceac169e001238a028cf1a9c0244bb.png)

## 查看 Python 版本:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
pyenv install --list
```

![](img/060ef570eedd204a405eeea95f41ed23.png)

## 安装 Python:

1.  从以下这些说明中找到版本
2.  复制提供的命令
3.  将命令粘贴到终端
4.  按“回车键”
5.  重复

```
**Python 3.5:**
pyenv install 3.5.4**Python 3.6:**
pyenv install 3.6.8**Python 3.7:**
pyenv install 3.7.9**Python 3.8:**
pyenv install 3.8.6**Python 3.9:**
pyenv install 3.9.0
```

![](img/3406a0efb49410ed2368655e5a6f67dc.png)

## 设定电脑的默认版本:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
pyenv global 3.8.6
```

![](img/0218f9419aafc06847772d18e9627608.png)

## 打开主目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
cd ~
```

![](img/055176978b9e6aa9736af7a24e43029c.png)

## 创建 Zsh 配置文件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
echo "" >> .zshrc
```

![](img/81ce0f931a794ab8a76f118028fa901a.png)

## 打开 Zsh 配置文件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
open -a textedit ~/.zshrc
```

![](img/298a1dff38b8019ff24268e25a4f5887.png)

## 编辑 Zsh 配置文件:

1.  从这些指令下面复制代码
2.  将代码粘贴到“文本编辑”中
3.  单击“文件”菜单
4.  点击“保存”

```
# Pyenv initialization
if command -v pyenv 1>/dev/null 2>&1; then
  eval "$(pyenv init -)"
fi
```

![](img/bf144c8c97b4f2b47173554a17ae60c4.png)

## 重启 Zsh:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
exec zsh
```

![](img/66bb8a992e90861a18e7980ffbc3ba37.png)

## 创建临时目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
mkdir temporary
```

![](img/eda051c0d514a02c3470f6a8f47f918e.png)

## 打开临时目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
cd temporary
```

![](img/366ff4485ea9ec3baf41df5064c38b03.png)

## 设置目录的默认版本:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
pyenv local 3.6.8
```

![](img/0c7cac2f6b1439fb68842f521c3b964e.png)

## 检查默认版本:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
python --version
```

![](img/610bccabd5823348b1470507a38f3b7e.png)

## 打开父目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
cd ..
```

![](img/38bd8616fff8e3802b70f05d22c54246.png)

## 检查默认版本:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
python --version
```

![](img/23dd2c5f02caaf18b9c06e329d830090.png)

## 打开临时目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
cd temporary
```

![](img/366ff4485ea9ec3baf41df5064c38b03.png)

## 创建虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
python -m venv venv36
```

![](img/591b7b6fb1327723a0005d51dfd1fd91.png)

## 激活虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
source ./venv36/bin/activate
```

![](img/d7a7e38bfae91500c3a7f8df5c6b75e6.png)

## 检查默认版本:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
python --version
```

![](img/e372347b8a9fef1d05b6d67e06768db1.png)

## 检查可执行文件:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
which python
```

![](img/ce07fb72aa1a7f6a28c059cf33ac9d1c.png)

## 停用虚拟环境:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车键”

```
deactivate
```

![](img/7294fb3fa5fdbe59f7206671f6f19895.png)

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