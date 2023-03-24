# 支持 Golang 和 gRPC 的比特币高清钱包(上)

> 原文：<https://levelup.gitconnected.com/bitcoin-hd-wallet-with-golang-and-grpc-part-l-56d8df05c602>

![](img/dc27c58f8fe9c5d4507e1f6c429cc1bf.png)

在这个教程系列中，我们将学习如何使用`Golang`创建一个高清比特币钱包。

本系列教程的想法不仅是展示如何创建钱包和获取余额，还展示了如何实现一个`grpc`服务器和一个`CLI`工具以及一个消费钱包的网络应用程序。

在这一部分，我们将看到如何实现我们的`grpc`服务器。

要求:

*   `go version go1.13.8 darwin/amd64`

此外，我会假设你已经对`Golang`有所了解，并理解比特币的主要概念，所以，我不会深究如何安装`Golang`或什么是比特币。

所以，让我们从定义我们的程序将为我们做什么开始。为了使本教程简短，我将把它分成两部分，在这一部分中，我们将创建服务和一个`CLI`工具，它将允许我们创建比特币密钥对(私钥和公钥)并检索我们钱包的余额。

**请注意，本教程仅用于教育目的，该软件尚未投入生产。**

我已经决定使用 [Blockcypher](https://www.blockcypher.com/) 来处理区块链请求，以及我发现的一个用于生成地址的 go 包，Blockcypher 允许你创建钱包，但我认为将 API 仅用于区块链请求会更好。

你也可以在这里找到本教程[的完整代码。](https://github.com/LuisAcerv/btchdwallet)

您将需要安装`grpc`，为此您可以遵循官方说明:[https://grpc.io/docs/languages/go/quickstart/](https://grpc.io/docs/languages/go/quickstart/)或执行以下操作:

打开您的终端，使用以下命令安装 Go 协议编译器插件(`protoc-gen-go`):

```
$ export GO111MODULE=on # Enable module mode 
$ go get github.com/golang/protobuf/protoc-gen-go
```

一旦您安装了`protoc-gen-go`，让我们创建我们的`proto`服务:

1.  在项目的根目录下创建一个名为`proto`的文件夹。
2.  在`proto`文件夹中创建一个名为`btchdwallet`的文件夹。
3.  在您的`btchdwallet`文件夹中创建一个名为`wallet.proto` 的文件，内容如下:

4.现在创建一个名为`Makefile`的新文件，内容如下:

5.从您的终端运行以下命令:

```
$ make build_protoc
```

该命令将在`./proto/btchdwallet/wallet.pb.go`创建一个新文件。现在我们可以开始构建我们的服务了。

现在我们已经准备好添加功能的`grpc`服务。在我们开始创建主文件之前，我们需要创建一些包含程序功能本身的其他脚本。

6.我们将创建一个名为 crypt 的小软件包，它将负责生成一个随机的 8 字节散列，我们将使用它来生成我们的助记符。为此，在您的项目根目录下创建一个名为 crypt 的新文件夹，并使用相同的名称和文件，并在其中使用以下内容进行扩展:

我在这里插一句。您可以使用最适合您的架构，但是请注意，本教程是一个系列的一部分，在以后的章节中，这个架构将比现在更有意义。说到这，我们继续…

7.现在我们有了我们的小地穴助手，我们将创建另一个名为 wallet 和 file 的文件夹，并在其中使用相同的名称和 go 扩展名。

我必须在这里再加一个括号。我使用这个 [**包**](http://github.com/wemeetagain/go-hdwallet) **来生成比特币密钥对。请记住，本教程只是为了了解我们如何在比特币的背景下实现一个** `***grpc***` **服务器和客户端。对于生产级的软件，你可能想用另一个工具，比如**[**block cypher**](https://www.blockcypher.com/)**API 或者别的什么。反正说到加密货币，一定要意识到代用户管理资产的风险。**

首先，我们有返回四个`strings`、比特币地址、公钥、私钥和助记短语的`CreateWallet`函数。请注意，我们没有在任何地方存储这些数据，因此用户必须记下这些信息并保存在安全的地方，因为我们以后无法恢复这些数据。

我们将在这里使用几个包，请确保使用以下命令将它们安装到您的项目中:

```
$ go get <package url>
```

现在我们需要添加我们的函数来检索我们的钱包。

decode wallet 函数接受一个参数，即助记符。使用助记符，我们可以解码我们的私人和公共密钥。

最后，我们将添加一个函数来获得地址平衡:

综上所述:

8.一旦我们有了钱包脚本，是时候创建我们的主脚本来实现我们的`grpc`服务器了。

这个脚本有三个功能，和我们在`wallet.proto` 脚本中写的一样。我们也为这些函数调用我们的 wallet 方法。

如果您使用`go run main.go`运行这个脚本，并且一切顺利，您应该在您的终端中看到以下内容:

```
Service running at port: :50055
```

在下一章中，我们将一步一步地看到如何为我们的`grpc`服务器实现一个客户端。

你可以在这里下载完整的代码，这里已经包含了一个小的`CLI`。要使用它，只需运行如下:

```
# Run the grpc server
$ go run main.go# In another terminal run:
go run client/client.go -m=create-wallet# Output:
New Wallet >>> Public Key: xpub661MyMwAqRbcG3fYrFtkZGesCkhTZWAwHDM2Q1DbeMH6CcQSkrL5qzYwnRkzwKKhrsjbngkC8EcNTBvQmBAJhMUVAXmU4qv8jzVFkhrqme1> Private Key: xprv9s21ZrQH143K3Zb5kEMkC8i8eiryA3T5uzRRbcoz61k7Kp5JDK1qJCETw9vxGBCe88qu57EKUu2hX54zeivPiZhCNQ5dV6CfKdhsCwMqm5j> Mnemonic: coral light army glare basket boil school egg couple payment flee goose# To get your wallet
go run client/client.go -m=get-wallet -mne="coral light army glare basket boil school egg couple payment flee goose"# To get your balance
go run client/client.go -m=get-balance -addr=1Go23sv8vR81YuV1hHGsUrdyjLcGVUpCDy
```

在接下来的章节中，我们将一步一步地讲述如何实现这个`CLI`。

黑客快乐！

如果你有任何意见，请发推文给我。