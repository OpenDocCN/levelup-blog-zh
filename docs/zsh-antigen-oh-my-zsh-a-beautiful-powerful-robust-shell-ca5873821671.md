# Zsh +抗原+ Oh my Zsh =一个漂亮、强大、健壮的外壳

> 原文：<https://levelup.gitconnected.com/zsh-antigen-oh-my-zsh-a-beautiful-powerful-robust-shell-ca5873821671>

![](img/b83522a31b93b338713e36d23ca3df98.png)

## 快速指导，建立一个美丽的强大和强大的外壳与 ZSH，抗原和哦，我的 ZSH

我已经和默认的 plain shell 一起工作了很长一段时间，直到最近我意识到我不能再每天面对那个“丑陋又无聊的家伙”了。作为一名开发人员，我花在 shell 工作上的时间比花在我任何亲戚身上的时间都多，包括我的妈妈、我的女朋友或我的室友。因此，我需要一个更好的伙伴:

*   它应该很漂亮，这样我每天看几个小时都会觉得很舒服。
*   它应该是强大和智能的，这样它可以帮助我更快更方便地做事情。
*   它应该是稳定的，否则我将无法工作，直到它被修复。
*   它应该是可配置和可扩展的，这样我就可以很容易地添加或删除一些功能。

👉🏻经过一段时间对可用工具的研究和试验，我发现 Zsh +抗原+ Oh my Zsh 确实符合我的预期，是目前为止最好的解决方案。

![](img/f163e2ad15057e69fc63d76426ee6f58.png)

# Zsh

**Z shell**([**Zsh**](https://www.zsh.org))是一个扩展的 Unix shell，有很多改进，包括 [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) 、 [ksh](https://en.wikipedia.org/wiki/KornShell) 和 [tcsh](https://en.wikipedia.org/wiki/Tcsh) 的一些特性。

Zsh

**Zsh 非常受欢迎，它被发布到几乎所有可用的 Unix 发行版(Ubuntu、Centos、OSX 等)的默认软件包库中，因此您可以使用您的软件包管理器轻松安装 Zsh:**

```
*# Ubuntu*
sudo apt-get install -y zsh*# CentOS*
sudo yum install -y zsh*# OSX (Since OSX Catalina, Zsh has been installed with the OS)*
brew install zsh
```

# **抗原**

**正如我们所说，Zsh 支持可插拔性，因此用户可以安装插件并扩展其功能。但是 Zsh 本身并没有提供很好的插件管理机制，包括取插件、安装插件、更新插件、移除插件等。**

**幸运的是， [**抗原**](http://antigen.sharats.me) 很好的承担了那个责任。大多数 Zsh 插件都是以 git 库的形式发布的。Antigen 允许我们简单地指定远程存储库路径，然后当它第一次运行时，它会自动进行获取、安装。Antigen 还为我们提供了更新和删除插件的命令。**

## **安装**

**首先，下载抗原，只需运行:**

```
$ curl -L git.io/antigen > antigen.zsh
```

**然后，将下面几行粘贴到您的`~/.zshrc`(如果您没有该文件，请创建一个空文件):**

```
*# Load Antigen*
source /path-to-your/antigen.zsh*# Load Antigen configurations* antigen init ~/.antigenrc
```

## **安装ˌ使成形**

**将是你的抗原配置文件，其中列出了插件和主题。**

**以下是我的`~/.antigenrc`:**

```
*# Load oh-my-zsh library.*
antigen use oh-my-zsh*# Load bundles from the default repo (oh-my-zsh).*
antigen bundle git
antigen bundle command-not-found
antigen bundle docker*# Load bundles from external repos.*
antigen bundle zsh-users/zsh-completions
antigen bundle zsh-users/zsh-autosuggestions
antigen bundle zsh-users/zsh-syntax-highlighting*# Select theme.*
antigen theme denysdovhan/spaceship-prompt*# Tell Antigen that you're done.*
antigen apply
```

**现在跳过`antigen use oh-my-zsh`这一行，我们将在下一节讨论。**

**要指定您想要应用的插件:**

```
antigen bundle remote-repo/url
```

**比如`zsh-users/zsh-completions`、`zsh-users/zsh-autosuggestions`其实都是指向`github.com/zsh-users/zsh-completions`和`github.com/zsh-users/zsh-autosuggestions`。Github 是自动推断的，如果存储库托管在不同的域下，只需指定域。**

**要使用它们，请使用`antigen theme remote-repo/url`。**

**最后，您需要有一行`antigen apply`来冻结和应用所有上述配置。**

**我刚刚使用了几个插件，你可以在这里找到其他很棒的插件。**

# **哦，我的 Zsh**

**[**哦我的 Zsh**](https://github.com/robbyrussell/oh-my-zsh) 是一个 Zsh 配置框架，它嵌入了一堆不同的插件和主题，这样你就可以很容易地打开或关闭它们。**

**通过将`antigen use oh-my-zsh`添加到`~/.antigenrc`中，您可以轻松使用带有抗原的 Oh my Zsh。**

**然后打开一个插件或使用他们嵌入在哦我的 Zsh:**

```
*# Turn on an Oh my Zsh plugin*
antigen bundle plugin-name
antigen bundle git
antigen bundle command-not-found
antigen bundle docker*# Apply an Oh my Zsh theme*
antigen theme theme-name
antigen theme robbyrussell
```

**关于 Oh my Zsh [插件](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins)和[主题](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)的完整列表，请参考以下链接。**