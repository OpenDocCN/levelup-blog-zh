# 开始使用 AWS CLI 所需的全部知识

> 原文：<https://levelup.gitconnected.com/all-you-need-to-know-to-get-started-with-aws-cli-13c484aee49d>

![](img/2b3519889c2337095d88038e01065c04.png)

[天一马](https://unsplash.com/@tma)照片

AWS 命令行界面是一个开源工具，使您能够使用命令行 shell 中的命令与 AWS 服务进行交互。

AWS CLI 提供对 AWS 服务的公共 API 的直接访问。您可以使用 AWS CLI 探索服务的功能，并开发 shell 脚本来管理您的资源。

# 安装 AWS CLI

安装 AWS CLI 相当容易，关于如何在您的操作系统上安装 aws-cli 的分步指南可以在他们的[官方文档中找到。](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)

# 配置 AWS CLI

一旦安装了 AWS CLI，就会创建一个秘密目录`~/.aws/`。

要配置您的帐户，最简单的方法是使用命令:

```
$ aws configure
```

输入该命令后，您需要提供四条信息。ID 访问密钥、秘密访问密钥、区域名称和输出格式。

AWS CLI 将信息存储在一个名为 *default 的*概要文件*中，该概要文件是一个设置集合。*每当您运行未明确指定要使用的配置文件的 AWS CLI 命令时，都会使用默认配置文件中的信息。

`AWS Access Key ID`和`AWS Secret Access Key`是你的 AWS 凭证。它们与决定您拥有哪些权限的 AWS 身份和访问管理(IAM)用户或角色相关联。它们可以通过 AWS 管理控制台为 IAM 用户创建，或者您可以使用类似于 [google-auth](https://github.com/googleapis/google-auth-library-python) 的东西来获得您的 AWS 帐户的临时访问凭证。

Regions 标识存储服务器的 AWS 区域。

输出格式指定如何存储您执行的命令的结果。它可以是 json 字符串、yaml、文本或表格。

## **创建多个配置文件**

如果使用命令`aws configure`，结果是一个名为`default`的单一概要文件。可以创建附加配置，这些配置可以通过指定`--profile`选项和指定名称的名称来引用。

下面的示例创建了一个名为`staging`的配置文件，这些凭证可以来自与其他配置文件完全不同的帐户和地区。

```
 $ aws configure --profile staging
```

然后可以在~/中找到暂存配置文件。AWS/配置文件。

## 配置设置

AWS CLI 通过按以下顺序调用提供程序来查找凭据和配置设置，并在找到一组要使用的凭据时停止:

```
1\. Command line options - you can specify --region, --output and --profile as parameters on the command line2\. Environment variables - you can store values in the environment variables, AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, and AWS_SESSION_TOKEN. If they are present, they are used.3\. CLI credentials file - this is one of the files that is updated once you run the command aws configure. The file is located at ~/.aws/credentials on Linux or MacOS. This file can contain the credential details for the default profile and any named profiles4\. CLI configuration file - this is another file updated when you run the command aws configure, This file is located at ~/.aws/credentials and can contain the credential details for the default profile and any other named profiles. 
```

# AWS CLI 中的命令结构

AWS 命令行界面(AWS CLI)在命令行上使用多部分结构，必须按以下顺序指定:

1.  对`aws`程序的基本调用。
2.  顶层的*命令*，通常对应于 AWS CLI 支持的 AWS 服务。
3.  指定执行哪个操作的*子命令*。
4.  操作所需的常规 CLI 选项或参数。只要遵循前三个部分，您可以按任何顺序指定它们。如果一个独占参数被多次指定，只有最后一个值适用。

```
$ **aws <***command***> <***subcommand***> [***options and parameters***]**
```

参数可以接受各种类型的输入值，比如数字、字符串、列表、映射和 JSON 结构。支持什么取决于您指定的命令和子命令。

您可以键入:`$ aws ec2 help`来查找哪些命令适用于服务 ec2，以获取信息并对其进行操作。这同样适用于所有其他 AWS 服务，如 S3，DynamoDB，SNS 等。

这基本上是您从命令行访问和开始使用 AWS 服务所需的全部内容。欲了解更多信息，请访问 AWS 官方文档。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)