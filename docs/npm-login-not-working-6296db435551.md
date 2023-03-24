# NPM 登录不起作用

> 原文：<https://levelup.gitconnected.com/npm-login-not-working-6296db435551>

![](img/237252c80d34c7df9f7a037ee7a269cb.png)

*Photo by*[*@ pinjasaur*](https://unsplash.com/@pinjasaur)*on Unsplash*

# TL；速度三角形定位法(dead reckoning)

[该解决方案](https://clavinjune.dev/en/blogs/npm-login-not-working/#the-solution)适用于`Node v16.13.0`和`NPM v8.1.3`

# 介绍

我并不总是讨厌使用 NPM，除非它开始下载大量的 node_modules 并给出不那么冗长的日志。今天，我和我的同事发现了一个 NPM 的登录错误，这个错误不太有趣而且很难调试。我花了大约一个小时才找到根本原因。

在这篇文章中，我将写下解决方案，以防你遇到同样的问题。

# 当前状况

在我现在的公司，我们使用`Nexus3`作为 NPM 模块的私有存储库。我也用`Node v12.22.4`和`NPM v8.1.3`在我的本地机器上工作。为了登录我们的 Nexus 存储库，我使用以下格式的`.npmrc`:

```
@myorg:registry=https://repo.myorg.com/repository/npm-privatealways-auth=true_auth={{ base64 of username:password redacted here }}
```

我们对此很满意。没问题。

# 问题

当我的同事想尝试使用`Node v16.13.0`时，问题就来了。当执行`npm i @myorg/utils`命令时，它开始说`401`。

```
npm ERR! code E401npm ERR! Unable to authenticate, need: BASIC realm="Sonatype Nexus Repository Manager"npm ERR! A complete log of this run can be found in:npm ERR!     /home/user/.npm/_logs/2021-11-18T06_37_02_136Z-debug.log
```

# 寻找解决方案

不知道是`Nexus 3`跟`Node v16`不兼容还是怎么的。所以我试着重新登录 Nexus 仓库。

```
$ npm login --registry=https://repo.myorg.com/repository/npm-private/ --scope=@myorg/npm notice Log in on https://repo.myorg.com/repository/npm-private/Username: {{ username }}Password:Email: (this IS public) {{ email@myorg.com }}Logged in as {{ username }} on https://repo.myorg.com/repository/npm-private/.
```

好了，现在我们登录了。但是我一用`npm whoami`检查，又说`401`。

```
$ npm whoami --registry=https://repo.myorg.com/repository/npm-private/npm ERR! code E401npm ERR! Unable to authenticate, need: BASIC realm="Sonatype Nexus Repository Manager"npm ERR! A complete log of this run can be found in:npm ERR!     /home/user/.npm/_logs/2021-11-18T06_49_38_788Z-debug.log
```

然后我检查我的`.npmrc`文件内容，发现节点 v16 有一个不同的`.npmrc`格式。以下是我当前的`.npmrc`文件内容:

```
//repo.myorg.com/repository/npm-private/:_authToken=NpmToken.{{ uuid redacted here }}
```

好奇怪的格式。它没有反映范围，而且我仍然对`_authToken`格式本身感到困惑。当然，作为一个优秀的开发人员，我们需要一个快速的手来搜索谷歌上的每个关键字来找出我们的错误。然后，我通过[@ a pattere](https://github.com/apottere)找到了[这个评论](https://github.com/npm/cli/issues/3284#issuecomment-846057616)。所以，我尝试手动重写我的`.npmrc`文件内容。这是我当前的`.npmrc`文件内容:

```
//repo.myorg.com/repository/npm-private/:always-auth=true//repo.myorg.com/repository/npm-private/:_auth={{ base64 of username:password redacted here }}
```

似乎很有希望，于是我又试着执行了一遍`npm i @myorg/utils`。结果失败了。

```
$ npm install @myorg/utilsnpm ERR! code E404npm ERR! 404 Not Found - GET https://registry.npmjs.org/@myorg%2futils - Not foundnpm ERR! 404npm ERR! 404  '@myorg/utils@^0.2.0' is not in this registry.npm ERR! 404 You should bug the author to publish it (or use the name yourself!)npm ERR! 404npm ERR! 404 Note that you can also install from anpm ERR! 404 tarball, folder, http url, or git url.npm ERR! A complete log of this run can be found in:npm ERR!     /home/user/.npm/_logs/2021-11-18T07_04_32_033Z-debug.log
```

是的，没找到。我认为这是因为范围没有反映在`.npmrc`文件中。

# 解决方案

所以我试着用老办法，手动重写内容。这是我当前和最终的`.npmrc`文件内容:

```
@myorg:registry=https://repo.myorg.com/repository/npm-private//repo.myorg.com/repository/npm-private/:always-auth=true//repo.myorg.com/repository/npm-private/:_auth={{ base64 of username:password redacted here }}
```

最后，我再次尝试了`npm i @myorg/utils`命令，它成功了。

```
$ npm install @myorg/utilsadded 1 package, and audited 2 packages in 2sfound 0 vulnerabilities
```

# 结论

所以，一直都是`npm login`命令。我仍然找不到关于新的`.npmrc`格式的完整文档，我可能会遗漏文档，或者根本就没有文档。如果你有同样的问题，我希望你能找到这篇文章，并能解决这个问题。

感谢您的阅读！

*最初发布于 2021 年 11 月 18 日*[*https://clavinjune . dev*](https://clavinjune.dev/en/blogs/npm-login-not-working/)*。*