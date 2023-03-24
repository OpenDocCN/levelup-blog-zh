# 加速 JavaScript 开发的 NPM CLI 技巧

> 原文：<https://levelup.gitconnected.com/npm-cli-tricks-to-speed-up-your-javascript-development-7b7bc1d894cf>

使用 NPM CLI 技巧加速您的开发

![](img/654b2306800a84ad53fa708d5a8e8db5.png)

使用 NPM CLI 技巧加速您的 JavaScript 开发

这里列出了一些 NPM CLI 技巧，无论你在做什么项目，都可以让你使用 NPM 加速 JavaScript 开发。

以下是清单:

1.  一些 NPM CLI 速记员
2.  获取指定包的路径
3.  要查看软件包注册表信息
4.  打开应用程序的主页
5.  打开包的主页
6.  列出可用的 npm 脚本
7.  获取全局安装包列表
8.  获取应用程序中已安装软件包的列表
9.  同时处理一个应用程序和一个依赖项
10.  检查过时的依赖关系
11.  `npx`运行本地安装的可执行文件
12.  删除 devDependencies

# 1.一些 NPM CLI 速记员

我们大多数人每天都在使用它们，一天几次。

安装软件包:

*   `npm install <package>` ➡️ `npm i <package>`

全局安装软件包:

*   `npm install --global <package>` ➡️ `npm i -g <package>`

列出依赖关系:

*   `npm list` ➡️ `npm ls`

将软件包安装并保存为依赖项

*   `npm i --save <package>` ➡️ `npm i -S <package>`

将包安装并保存为 devDependency

*   `npm i --save-dev <package>` ➡️ `npm i -D <package>`

# 2.获取指定包的路径

要获取指定包的路径:

```
npm ls <package>
```

例如:

```
⇒ npm ls react  
[react-app-es6-jest@0.0.1](mailto:react-app-es6-jest@0.0.1) /Users/username/Code/WorkSpace/react-app-es6-jest 
└── [react@16.12.0](mailto:react@16.12.0)
```

最重要的是，嵌套包也将*显示指定包的路径。*

当你从一个嵌套的包中得到一个错误消息，并且你想找出是哪个应用依赖导致了这个错误时，这个命令非常有用。

比如来自`vinyl-fs`的一个错误，在你的 app **package.json** 中没有列出:

嵌套依赖关系的错误

要找出导致这种情况的应用程序依赖:

```
⇒ npm ls vinyl-fs 
[my-app@13.2.6](mailto:my-app@13.2.6) /Users/admin/Code/my-app 
└─┬ [gulp@3.9.1](mailto:gulp@3.9.1)
  └── [vinyl-fs@0.3.14](mailto:vinyl-fs@0.3.14)
```

# 3.要查看软件包注册表信息

有时您想要检查软件包的注册表信息，例如:

*   当前安装的版本
*   最新版本
*   包依赖关系
*   许可证
*   保持器
*   发布时间
*   等等

然后，`npm view <package>`是显示这些信息的最快方式。

例如，要显示`express`的包注册表信息:

npm 查看包注册表信息

# 4.打开应用程序的主页

为此，只需运行`npm home`并在浏览器中观察它的打开。

# 5.打开包的主页

与上一个非常相似，要打开一个包的主页，只需运行:

```
npm home <package>
```

例如，`npm home express`将在你的浏览器中打开[http://expressjs.com/](http://expressjs.com/)，这是在它的注册表信息中。

# 6.列出可用的 npm 脚本

一种发现方法是打开 **package.json** 文件并检查`scripts`部分。

我们可以做得更好，只要简单地运行`npm run`并获得所有可用脚本的列表。

列出可用的 npm 脚本

# 7.获取全局安装包列表

您可能安装了一些全球最流行的包，但没有在 app package.json 中定义它们。

```
npm ls -g --depth 0
```

`--depth 0`或`--depth=0`是为了避免包含嵌套依赖关系。这是结果的一个例子:

列出全局安装的软件包

# 8.获取应用程序中已安装软件包的列表

与上一个类似，有时我们想检查应用程序的 **package.json** 中有哪些依赖项

```
npm ls --depth 0
```

这是一个结果的例子

列出本地安装的软件包

# 9.npm 链接，用于同时处理应用程序和依赖项

当您同时处理应用程序和自定义依赖项时，您可能需要

*   调试应用程序中的一个问题，该问题可能存在于自定义依赖项中
*   使用自定义依赖项中的新更改检查您的应用程序是否正常运行。

您可以手动将自定义依赖项复制到应用程序的 **node_modules** 中，但是这里有一个更好更方便的方法:`npm link`

如何使用`npm link`

1.  在终端中，将`cd`放到自定义依赖项目的文件夹中，并运行命令`npm link`。这创建了一个**符号链接**，它使得依赖项目在全球范围内可用。
2.  然后，将 cd 放入您的应用程序文件夹(依赖于自定义依赖项项目的文件夹)。在根目录中，运行命令`npm link <name_of_custom_dependency>`，这将告诉应用程序使用定制依赖项的全局符号链接。

就是这样。并启动您的应用程序，您应该能够看到您的应用程序正在使用您的本地自定义依赖项运行。如果您编辑、更改和重新构建自定义依赖项，您的应用程序应该会接受任何更改(**注意:**您需要重新启动应用程序来接受更改或使用类似`nodemon`的库)。

如何清除`npm link`

1.  转到应用程序文件夹的根目录并运行`npm unlink <name_of_custom_dependency>`，这将移除本地依赖。要恢复正常，您应该再次运行`npm install`来再次从存储库中获取依赖项。
2.  如果你想要这个**符号链接**被全局移除，那么`cd`进入自定义依赖项目文件夹并运行`npm unlink`

如何查看所有全球可用的`npm link` ( **符号链接**)依赖关系

可以跑`npm ls --depth 0`。例如，两个本地依赖项 **pdf-viewer** 和**数据增强**在全球范围内可用。

查看所有全局可用的 npm 链接依赖项

# 10.检查过时的依赖关系

您可以在一个项目中运行`outdated`命令，它将检查 npm 注册表以查看您的包是否过期。它将在您的命令行中打印出当前版本、所需版本和最新版本的列表。

npm 过时，检查过时的依赖关系

# 11.npx 在本地运行-安装可执行文件

我们在项目中安装了一个包，它带有一个可执行文件。在过去，只有当我们通过 **npm 脚本**或使用`./node_modules/.bin/<command>`手动路径运行它时，它才起作用。例如，摩卡套餐

```
"test": "mocha ."
```

或者在终端中运行以下命令

```
./node_modules/.bin/mocha .
```

这些都可以，但是都不太理想。

对于那些使用 **npm > = 5.2.0** 的人来说，你可能会注意到它会在通常的 npm 旁边安装一个新的二进制文件:`npx`。`npx`给你我认为最好的解决方案:

```
npx mocha .
```

使用本地可执行文件只需要做这些。

# 12.删除 devDependencies

要删除 devDependencies，只需运行

```
npm prune --production
```