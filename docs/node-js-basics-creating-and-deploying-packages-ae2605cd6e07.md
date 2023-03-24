# Node.js 基础—创建和部署包

> 原文：<https://levelup.gitconnected.com/node-js-basics-creating-and-deploying-packages-ae2605cd6e07>

![](img/3a175a9cdc186a3d2619698910267d18.png)

照片由[🇨🇭·克劳迪奥·施瓦茨| @purzlbaum](https://unsplash.com/@purzlbaum?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我们将了解如何开始使用 Node.js 创建程序。

# 创建 NPM 包

为此，我们可以创建自己的 NPM 包，我们运行`npm add user`，这样我们就可以登录到 NPM 来创建我们的包。

输入命令后，我们必须输入用户名、密码和电子邮件地址，才能在 NPM 上创建帐户。

该电子邮件将是公开的。

然后我们运行`npm init`来创建我们包的`package.json`文件。

一旦我们回答了所有的问题，我们应该得到这样的结果:

```
{
  "name": "npm-example",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

一旦完成，我们就可以运行`npm publish`来发布我们的包。

一旦我们发布了我们的包，我们就可以运行:

```
npm install npm-example
```

安装我们的软件包。

为了更新我们包的版本，我们运行`npm version patch`。

然后我们可以运行`npm publish`来发布具有更高版本的包。

为了防止一些文件在包中发布，我们可以创建一个`.npmignore`文件，它遵循与`.gitignore`文件相同的规则。

# 结论

我们可以用`npm`轻松创建 NPM 包。

我们所要做的就是输入一些包数据并发布包。