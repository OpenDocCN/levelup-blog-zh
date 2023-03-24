# 在 Ubuntu 22.10 上安装 Go

> 原文：<https://levelup.gitconnected.com/installing-go-on-ubuntu-b443a8f0eb55>

## 权威指南

## 这篇博客展示了在 Ubuntu 22.10 上安装 Go 1.20 的步骤

![](img/a6a8f6c009cb79af1698e5978164645f.png)

这篇博客将介绍如何在 Ubuntu 上安装 Go 1.19

# 1.安装 ubuntu 更新

```
sudo apt-get update
sudo apt-get -y upgrade
```

# 2.下载 Go 二进制文件

下一个合乎逻辑的步骤是下载 Go 二进制文件，最新的软件将在[https://golang.org/dl/](https://golang.org/dl/)可用，并安装 ubuntu 版本，在终端中运行以下命令

```
mkdir temp
cd temp ( to the folder you created in previous step)
wget [https://dl.google.com/go/go1.20.linux-amd64.tar.gz](https://dl.google.com/go/go1.14.1.linux-amd64.tar.gz)
```

提取下载的 tar 并安装到系统中所需的位置。但是一般来说，最好按照文档将它安装在`/user/local/go`下。在终端中运行以下命令进行安装

```
sudo tar -xvf go1.20.linux-amd64.tar.gz
sudo mv go /usr/local
```

# 3.环境设置

我们正在设置的三个 Go 语言环境变量是`GOROOT`、`GOPATH`和`PATH`。

`GOROOT`围棋的路径是安装在机器上的吗

`GOPATH`是工作目录的位置。

在`.profile`文件的末尾加上上面表示全局变量的文件。根据您的 shell 配置，您可能希望将它添加到一个`.zshrc`或`.bashrc`文件中。

```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

# 4.续订 shell 会话

```
source ~/.profile
```

# 5.最后检查

Go 现在应该已经成功安装在计算机上，以检查它是否正在运行下面的命令

```
go version
```

如果安装和配置得当，您应该会在终端上看到下面的输出

```
go version go1.20
```

为了检查本安装中设置的环境，执行命令`go env`，您应该会看到如下输出

```
GO111MODULE=""
GOARCH="amd64"
GOBIN=""
GOCACHE="/home/xxx/.cache/go-build"
GOENV="/home/xxx/.config/go/env"
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="linux"
GOINSECURE=""
GONOPROXY=""
GONOSUMDB=""
GOOS="linux"
GOPATH="/home/rockey/go"
GOPRIVATE=""
GOPROXY="https://proxy.golang.org,direct"
GOROOT="/usr/local/go"
GOSUMDB="sum.golang.org"
GOTMPDIR=""
GOTOOLDIR="/usr/local/go/pkg/tool/linux_amd64"
GCCGO="gccgo"
AR="ar"
CC="gcc"
CXX="g++"
CGO_ENABLED="1"
GOMOD="/home/xxx/snap/exercism/current/exercism/go/two-fer/go.mod"
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
PKG_CONFIG="pkg-config"
GOGCCFLAGS="-fPIC -m64 -pthread -fmessage-length=0 -fdebug-prefix-map=/tmp/go-build151852922=/tmp/go-build -gno-record-gcc-switches"
```

进一步阅读的资源:

1.  [https://golang.org/](https://golang.org/)(公文)
2.  [https://go.dev/](https://go.dev/)(开发者门户)
3.  由了不起的人经营的 https://changelog.com/gotime