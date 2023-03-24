# 分步操作:首次设置 Github 帐户

> 原文：<https://levelup.gitconnected.com/step-by-step-setting-github-account-for-the-first-time-8a04887c709d>

![](img/e134dcec15f020709b827e2c776ca60b.png)

演职员表: [@synkevych](http://twitter.com/synkevych) [链接](https://unsplash.com/photos/wX2L8L-fGeA)

当您在一台新机器上设置 git 时，您应该做一些事情。Github 有很棒的文档，但是为了完整起见，我将以更完整和简洁的形式重申。

我假设你有一个 github 账户。因为我们将在您的新机器上进行设置。

# 工作流程的线框是

*   获取并安装 github
*   将用户帐户详细信息添加到本地安装中
*   生成一个 rsa 密钥(允许 github 将您的本地机器识别为经过认证的机器)
*   将 rsa 密钥(公钥)添加到 github 帐户。
*   从本地机器确认 github 认证。

让我们开始吧。

## **安装 github**

根据你的操作系统，你需要下载并安装 github。这里，我假设您使用 Powershell (linux 子系统)在 linux、OSX 或 windows 上工作。无论操作系统是什么，所有的步骤都应该几乎相同。

## 向您的安装添加用户帐户详细信息

打开终端，输入下面两行代码。这应该会将您的 github 帐户的 user.name 和 user.email 设置为全局配置。

```
git config --global user.name **My-github-username** git config --global user.email **your_email@gmail.com**
```

## 创建身份验证密钥

让我们在本地设置身份验证方法。首先，我们必须生成 rsa 密钥。为此，请在终端中输入以下命令。

```
ssh-keygen -t rsa -C **your_email@gmail.com**
```

该命令创建两个文件`~/.ssh/id_rsa`和`~/.ssh/id_rsa.pub`。

如果它要求你创建一个密码来加密这个文件，这取决于你。否则，按 enter 键保留文件的默认名称(如上所示)，再按两次 enter 键保留空白密码。如果需要，您可以随时设置密码。

## 向 github 帐户添加认证密钥

如果你在 mac pbcopy 上，可以很容易地将公钥复制到剪贴板，如果不是，打开 rsa 公共文件。

```
less ~/.ssh/id_rsa.pub
```

或者使用

```
cat ~/.ssh/id_rsa.pub
```

在 mac 上

```
pbcopy < ~/.ssh/id_rsa
```

它应该会将以你的电子邮件地址结尾的整个密钥复制到你机器的剪贴板中。

## 向您的 github 帐户添加密钥

我们需要将该密钥添加到您的 github 帐户中。进入你的 github [账户设置](https://github.com/settings/profile)

*   点击左侧的 [SSH 和 GPG 键](https://github.com/settings/ssh)
*   点击右侧的“新建 SSH 密钥”
*   给这个键起一个你想起的名字(通常是你想关联的机器名)。请记住，密钥是特定于机器的。
*   将公钥(从剪贴板)粘贴到大文本框中

好了，现在我们所要做的就是从我们试图设置 github 帐户的本地机器上确认你的帐户认证成功。

## 确认一切是否正常

让我们转到终端，键入以下命令来测试它:

```
ssh -T git@github.com
```

它应该会返回一条很好的消息，比如

```
Hi username! You've successfully authenticated, but Github does not provide shell access
```

就是这样！如果您看到该消息，则您已经完成了 github 帐户的本地设置/认证。

![](img/d5edd4011b8dd05b8fafadf6e8adbe85.png)

演职员表 [@tateisimikito](http://twitter.com/tateisimikito) [链接](https://unsplash.com/photos/bJhT_8nbUA0)

如果这篇文章对你有帮助

请按下“喜欢”按钮来分享一些爱。

请 [***成为会员***](https://ithinkbot.com/membership) 和[***订阅***](https://ithinkbot.com/subscribe) 获取更简洁的教程。