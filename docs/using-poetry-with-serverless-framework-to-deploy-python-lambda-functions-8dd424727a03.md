# 使用诗歌和无服务器框架部署 Python Lambda 函数

> 原文：<https://levelup.gitconnected.com/using-poetry-with-serverless-framework-to-deploy-python-lambda-functions-8dd424727a03>

![](img/3c1529368e149173d388396798438001.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[信托“Tru”kat sande](https://unsplash.com/@iamtru?utm_source=medium&utm_medium=referral)拍摄的照片

我们正朝着使用诗歌进行依赖性管理和隔离我们的 Python 环境的方向发展，而不是使用 pipenv。我们之前部署 Lambda 函数的设置涉及到 pipenv 和 Zappa。由于我们正在重新审视这个设置，我发现自己正在努力在 AWS Lambda 上部署 Python 函数。这主要是因为在没有像 Zappa 或 Serverless 这样的框架的情况下，我试图按照 [AWS 文档](https://docs.aws.amazon.com/lambda/latest/dg/lambda-python-how-to-create-deployment-package.html#python-package-venv)为 AWS Lambda 手动打包和部署我的 Python 脚本(在一个诗歌虚拟环境中)。没有这些框架，我无法让它正常工作。

关于如何使用[无服务器框架](https://serverless.com/)部署 Python Lambda 函数，已经有很多可用的指导方针，使用不同类型的依赖管理器和框架来隔离您的 Python 环境。现有的指南并没有真正涵盖将无服务器与诗歌结合使用的逐步说明，所以这就是我写这篇文章的原因，实际上它非常简单！

这些步骤基于[这篇文章](https://serverless.com/blog/serverless-python-packaging/)，但是他们使用 virtualenv 隔离他们的 Python 环境。

## 先决条件

*   你已经安装了[诗歌](https://python-poetry.org/)
*   您已经安装了 [Node.js](https://nodejs.org/en/download/)
*   您已经安装了 [awscli](https://aws.amazon.com/cli/) 并用一个(默认的)概要文件对其进行了配置
*   您已经全局安装了[无服务器](https://serverless.com/framework/docs/getting-started/)(我选择通过 npm 安装)
*   您有一个想要部署到 Lambda 的项目或脚本(使用诗歌进行隔离并作为依赖项管理器)

如果你还没有一个项目，并且你想编码，你可以创建一个新的文件夹，init poeties(`poetry init`)在那个文件夹中，并添加我提到的文章中提供的`handler.py`文件。您还想在您的环境中添加 numpy 作为依赖项(`poetry add numpy==1.18.1`)。然后，您将得到如下所示的文件结构:

```
numpy-test/
    handler.py
    pyproject.toml
```

# 添加 serverless.yml

要使用无服务器，您需要一个配置文件。该配置需要作为一个名为`serverless.yml`的 YAML 文件提供，它需要存储在您的项目文件夹的根目录中。你可以在[这里](https://serverless.com/framework/docs/providers/aws/guide/serverless.yml/)找到所有可用的属性。

如果您使用自己的 Python 项目或脚本，您应该根据自己的需要进行编辑。此外，这不应该在生产中使用，这只是为了说明如何使用最小配置设置 Lambda。

```
*# serverless.yml* service: numpy-test

provider:
  name: aws
  runtime: python3.6

functions:
  numpy:
    handler: handler.main

plugins:
  - serverless-python-requirements

custom:
  pythonRequirements:
    dockerizePip: non-linux
```

根据您是否正在使用 AWS 概要文件，您还可以在这个 YAML 文件中提供一个概要文件。

```
provider:
  ...
  profile: PROFILENAMEHERE
  ...
```

# 初始化 npm 并安装 python 需求插件

无服务器是一个节点包，我们想在当前设置中安装`serverless-python-requirements`插件。

运行项目文件夹根目录下的`npm init`和`npm install --save serverless-python-requirements`。然后，您将拥有以下结构:

```
numpy-test/
    node_modules/
        ....
    handler.py 
    package.json 
    package-lock.json 
    pyproject.toml
```

# 将依赖项导出为 requirements.txt

将项目依赖项导出为 requirements.txt。如果不这样做，它会说`Serverless: Generating requirements.txt from pyproject.toml`，但是由于某种原因，这不起作用，所以在部署之前导出需求应该可以做到。

```
poetry export -f requirements.txt > requirements.txt --without-hashes
```

# 展开 Lambda

我们准备好部署 Lambda 功能了。在部署(`poetry shell`)之前，确保您已经激活了正确的 Python 环境(项目的一个)。您也可以通过运行`poetry run python handler.py`来测试功能。

如果工作正常，使用以下命令部署 Lambda:

```
serverless deploy
```

这将输出:

```
numpy-test % serverless deploy                                   
Serverless: Generating requirements.txt from pyproject.toml...
Serverless: Parsed requirements.txt from pyproject.toml in numpy-test/.serverless/requirements.txt...
Serverless: Using static cache of requirements found at serverless-python-requirements/80ac7cc0113af153aa922ca2df559693ec011f4167582c1a351d76f2527d265f_slspyc ...
Serverless: Packaging service...
Serverless: Excluding development dependencies...
....
```

# 调用 Lambda

然后，您可以使用以下命令调用部署的 Lambda 函数:

```
serverless invoke -f numpy --log
```

这将输出:

```
numpy-test % serverless invoke -f numpy --log 
null 
-------------------------------------------------------------------- START RequestId: 82df6fa0-76ac-462c-a3f9-ab2935af9e33 Version: $LATEST 
Your numpy array: 
[[ 0  1  2  3  4]  
 [ 5  6  7  8  9]  
 [10 11 12 13 14]] 
END RequestId: 82df6fa0-76ac-462c-a3f9-ab2935af9e33 
REPORT RequestId: 82df6fa0-76ac-462c-a3f9-ab2935af9e33  Duration: 2.32 ms       Billed Duration: 100 ms Memory Size: 1024 MB    Max Memory Used: 86 MB  Init Duration: 597.45 ms
```

# 搞定了。

现在，您已经使用无服务器和诗歌部署了 Python Lambda 函数。如果您对本文有任何问题、建议或评论，请告诉我！

# 2021 年 7 月更新

这篇文章写于 2020 年 2 月。与此同时，AWS Lambda 也可以处理容器图像，我强烈建议结合 Terraform 来研究这一点。

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题

### 掌握编码面试的过程

技术开发](https://skilled.dev)