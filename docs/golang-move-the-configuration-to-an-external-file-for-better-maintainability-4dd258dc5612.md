# Golang:将配置移动到外部文件以获得更好的可维护性

> 原文：<https://levelup.gitconnected.com/golang-move-the-configuration-to-an-external-file-for-better-maintainability-4dd258dc5612>

## 开发人员倾向于为不同的开发环境使用不同的可执行文件。这个方案将各种配置转移到一个 YAML 文件中，这使得维护各种环境的配置和拥有一个单独的构建变得容易。

如果项目驻留在代码中，就很难管理项目的配置。在团队中工作时，开发人员会在项目中不断添加各种配置。

我总是把它移到一个外部文件中，以便把所有的配置保存在一个地方。这使得维护稍微容易一些，并且对于所有环境都有一个可执行文件。

![](img/17ff1378e748a994d2af25ecf4e938d8.png)

图片来自 Pixabay

## 在外部文件中配置的优势

*   可维护性
*   一个适用于所有环境的可执行文件
*   配置的改变不需要新的构建

## 怎么会？

我们将把配置转移到一个 YAML 文件中，用 Revel 和 GORM 构建 Golang 项目。

该方案可应用于任何 Golang 项目。

Github 项目链接:

[](https://github.com/amritsingh/golang_tutorials/tree/main/revel/config_to_yaml) [## amritsingh/golang _ 教程

### 在 GitHub 上创建一个帐户，为 amritsingh/golang_tutorials 开发做贡献。

github.com](https://github.com/amritsingh/golang_tutorials/tree/main/revel/config_to_yaml) 

我们将把数据库连接字符串从`conf/app.conf`文件移动到一个新文件`config.yml`

`conf/app.conf`中的数据库连接字符串是这样的

```
...[dev]
db.info = user1:tmppwd@tcp(localhost:3306)/sampleapp?parseTime=True
```

删除上述两行，并将以下内容添加到`config.yml`

```
db: user1:tmppwd@tcp(localhost:3306)/sampleapp?parseTime=True
```

现在我们需要读取配置文件`config.yml`。在`app`文件夹中新建一个目录`helpers`。在`app/helpers`中创建一个新文件`app_config.go`

上面的代码读取`config.yml`文件并填充一个名为`AppConfig`的变量。如果您希望在`config.yml`中添加更多配置，您需要改变结构`Cfg`。

我们需要在项目初始化时读取配置，以便在您想要创建 DB 连接时可以使用配置。

在`app/init.go`中，在初始化方法中读取配置。创建数据库连接时，从`AppConfig`中读取连接字符串。

## 包装它

根据我的经验，拥有一个单独的配置文件对可维护性总是有帮助的。这个概念可以应用于任何 Golang 项目，不管使用什么框架。最后，要看程序员更喜欢什么。

*喜欢自己体验媒介？考虑通过注册会员* *来支持我和其他作家* [***。会员每月只需 5 美元，它支持我们，作家，没有额外的费用。如果你这样做，我会收到一部分费用，不会多花你多少钱。谢谢大家！***](https://singhamrit.medium.com/membership)