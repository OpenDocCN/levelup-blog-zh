# 使用 GitHub 操作为您的 Android 应用程序签名

> 原文：<https://levelup.gitconnected.com/using-github-actions-to-sign-your-android-application-a67f73c056c8>

![](img/4098f2a5dbf260d20fdb31dcf5fa5464.png)

照片由 Pexels 的 Pixabay 拍摄

使用 *Github 动作*我们可以编译、运行测试，还可以**签署发布工件**。但是这是危险的，因为我们需要将我们的密钥库上传到公共存储库中。

作为开发人员，我们必须采取一定的预防措施，比如不要提交 API 密钥或密钥库，以及使用 *Github 动作*，因为 CI 不是这样做的借口。

## 将凭证和密钥库上传到私有存储库

为了有一个更加安全的环境并能够达到我们的目的，密钥库和与下面的例子结构相似的文件必须在一个私有的存储库中。

```
keyAlias=[your key alias]
keyPassword=[your key password]
storeFile=storeFile=../keystore/[you keystore filename]
storePassword=[your store password]
```

要将我们的私有存储库添加为子模块，我们必须在项目的根文件夹中使用 CLI 或 git GUI(如果它支持的话)。下一个命令的最后一个参数是包含密钥库存储库内容的文件夹，为简单起见，我们称之为“密钥库”。

```
git submodule add git@github.com:[user]/[repository].git keystore
```

这样，您就有了能够在本地签名的密钥库，以及能够在不丢失它的情况下改变您的计算机或格式化它的灵活性。

## 链接凭据文件并签署应用程序

然后，我们必须将下面的代码片段添加到我们的项目 *build.gradle* 中，以获得我们之前创建的文件。

```
def keyProps = new Properties()
def keyPropsFile = rootProject.file('keystore/[credentials file]')
if (keyPropsFile.exists()) {
    keyProps.load(new FileInputStream(keyPropsFile))
}
```

现在，必须修改 release flavor，以便它在构建时为文件分配这些变量，从而可以对应用程序进行签名。

```
signingConfigs **{** release **{** keyAlias keyProps['keyAlias']
        keyPassword keyProps['keyPassword']
        storeFile keyProps['storeFile'] ? file(keyProps['storeFile']) : null
        storePassword keyProps['storePassword']
    **}
}**
```

完成这些步骤后，您在签署应用程序时将不会遇到任何问题。如果您有任何问题，请检查之前的片段，以防您可能犯了打印错误。

*Git* **默认情况下不得跟踪**密钥库或配置文件。从 *build.gradle* 上传变更，让我们继续 *GitHub Actions* ！

## 配置 GitHub 操作以登录发布

下一步，您必须在 *GitHub* 上生成一个**个人访问令牌**。按照 [GitHub 文档](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line)中的步骤获取它，当你完成后，回到文章。出于这个原因，我们只需要配置来获得私有存储库的**控制权，**记住这一点。

一旦我们有了这个令牌，将它添加到**应用程序库**的**设置**中的**秘密**部分中。

最后，创建*。github/workflows* 目录中的文件夹，我们将在其中添加[文件来配置](https://gist.github.com/dagonco/0a75feed194881b571af1a46e90e4dfa) *GitHub 动作*以在每次提交到 master 时编译发布版本**。**