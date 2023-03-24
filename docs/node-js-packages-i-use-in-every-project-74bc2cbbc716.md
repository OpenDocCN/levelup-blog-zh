# 我在每个项目中使用的 Node.js 包

> 原文：<https://levelup.gitconnected.com/node-js-packages-i-use-in-every-project-74bc2cbbc716>

![](img/8ebf06dea4261e8da9d86b4355c93d3a.png)

原图片由[赵家伟](https://unsplash.com/@jiaweizhao)在 [Unsplash](https://unsplash.com/photos/W-ypTC6R7_k) 上

以及为什么你也应该使用它们！

在我的职业生涯中，可能和你们中的许多人一样，我创建了几十个新的 JavaScript 项目，既有个人使用的，也有专业使用的。和所有事情一样，一旦你经常做某事，你就建立了共同的模式、惯例和最佳实践。在这篇文章中，我想谈谈我每次使用的最好的 Node.js 包，以及它们如何帮助您减少开发时间并立即扩展 JavaScript 应用程序的性能。

这个列表中的包适用于几乎所有的 JavaScript 项目，而不是特定于 React、Angular、Express、NestJS 等框架或库。我还排除了实用函数库，如 [lodash](https://lodash.com/) 或 [rambda](https://ramdajs.com/) ，或日期/时间转换库，如 [momentjs](https://momentjs.com/) 或 [date-fns](https://date-fns.org/) 。我已经排除了像 [Webpack](https://webpack.js.org/) 、 [ESBuild](https://esbuild.github.io/) 、 [Rollup](https://rollupjs.org/) 或[package](https://parceljs.org/)这样的捆绑器，因为它们对于您正在处理的项目类型来说是非常特定的。相反，我试图特别关注我用于**项目设置**、**开发**、**构建**和**维护**的包，这些你以前可能会忽略。虽然这里列出的一些你肯定已经知道了，但是其他的对你来说也是新的。如果您不同意某个选择或有其他建议，请告诉我！

# 代码格式

让我们从基础开始:*写好代码* ( [不管那是什么意思](/7-simple-attributes-of-good-code-70c24cd75ad7))。当您在团队中工作时，有时很难保持代码库的格式和编码风格一致。毕竟有些人更喜欢制表符而不是空格(怪物！)，有些用单引号表示字符串，有些用双引号。有时在 JavaScript 语句的末尾有一个分号，等等。这就是代码格式化和验证库的用武之地！有些工具甚至能够超越简单的格式和样式，而是能够积极地帮助您编写更好、更快、更安全的代码。

下面是我在每个 JavaScript 项目中使用的几个例子:

## 较美丽

[漂亮的](https://prettier.io/)是一个命令行工具，也作为 Visual Studio 代码和其他 ide 的[扩展而存在。它会自动格式化你的代码，并强制执行一个通用的编码风格(想想制表符和空格，引用风格等等)。).](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

漂亮是固执己见的，因此已经内置了许多格式规则，不一定需要配置。但是它和 [EditorConfig](https://editorconfig.org) 一起工作也很好，这是一个优势，因为许多 ide 支持开箱即用的 [EditorConfig](https://editorconfig.org) 格式选项，即使你没有安装更漂亮的扩展。以下是我目前正在做的项目中的一个，供参考:

```
# Editor configuration, see [https://editorconfig.org](https://editorconfig.org)
root = true[*]
charset = utf-8
indent_style = space
indent_size = 2
insert_final_newline = true
trim_trailing_whitespace = true
max_line_length = 120[*.ts]
quote_type = single[*.md]
max_line_length = off
trim_trailing_whitespace = false
```

## 埃斯林特

![](img/03bafa61013c1e8c9a62011c485a5ce6.png)

让我这么说吧:如果你不把 [ESLint](https://eslint.org/) 和 JavaScript [一起使用，那你就做错了](https://www.youtube.com/watch?v=GaAUS0GsG_M)！如果你不熟悉 linter 的功能，可以从 ESLint 主页上下载:**查找并修复你的 JavaScript 代码中的问题**。

ESLint 是最受欢迎的 JavaScript linter，它会自动发现并自动修复许多常见的代码错误。更好的是，ESLint 可以通过插件扩展为[添加对 TypeScript](https://github.com/typescript-eslint/typescript-eslint) ( `@typescript-eslint/eslint-plugin`和`@typescript-eslint/parser`)、[格式 JSON 文件](https://www.npmjs.com/package/eslint-plugin-json) ( `eslint-plugin-json`)的支持，并且[还可以使用`eslint-plugin-prettier`和`eslint-config-prettier`插件与更漂亮的](https://github.com/prettier/eslint-plugin-prettier)很好地集成。

写关于 ESLint 的文章很容易就能写满一篇博文。要获得流行插件的完整列表，请查看 [Awesome ESLint](https://github.com/dustinspecker/awesome-eslint) 列表。

## StandardJS 和 XO

这里对 [StandardJS](https://standardjs.com/) 和 [XO](https://github.com/xojs/xo) 的一个快速标注:你可以使用一个一体化的工具，比如 [StandardJS](https://standardjs.com/) 或 [XO](https://github.com/xojs/xo) ，而不是单独使用 Prettier 和 ESLint。两者都建立在 ESLint 的基础上，可以非常快速和容易地将格式和一致的编码风格应用到新项目中，而无需手动添加大量插件和配置。归根结底，选择哪一个取决于你的项目需要多大的灵活性，你喜欢什么样的“默认”风格，以及[你的编辑器是否支持它](https://standardjs.com/awesome.html#editor-plugins)。

## 强壮的

如果没有人去执行，规则还有什么价值？这就是[哈士奇](https://typicode.github.io/husky/#/)的用武之地！Husky 将 [git 挂钩](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)添加到您的项目中，无论开发人员何时提交或推送代码，它都可以用来运行 ESLint 和 Prettier above。通过这种方式，您可以确保提交到您的存储库中的任何代码都满足您之前定义的所有质量需求。

通常，我喜欢设置 Husky 在每个`git commit`运行 ESLint 和 Prettier，并在每个`git push`之前执行单元测试。毕竟，我喜欢遵循尽早提交、经常提交的策略，并且不想在每次提交时都运行全面的单元测试，从而减慢我的团队。

## 皮棉阶段

当 ESLint 和 Husky 一起使用时，每当您提交一个小的更改时，您很快就会对反复 Lint 整个项目所花费的时间感到恼火。 [Lint-Staged](https://github.com/okonet/lint-staged) 来救援了！直接调用 Lint-Staged 而不是 ESLint 将确保只有您提交的代码*更改*被格式化和 Lint 化，而不是整个代码库。

为了更漂亮地和 ESLint 一起运行，在`package.json`中将`lint-staged`添加到您的开发依赖项中，并通过添加一个`lint-staged`部分来配置它:

```
"lint-staged": {
 "src/*.{js,ts,md,yml,yaml,json,html,scss}": [
   "prettier --write",
   "eslint --cache --fix"
  ]
}
```

# 脚本

在许多情况下，您需要编写一些小的脚本片段来执行一些操作。以下是我更喜欢使用的工具:

## 里姆拉夫和 Mkdirp

![](img/036f4f07807aa2c11b7bf872f91c9bfa.png)

当您的开发团队同时使用 Windows 和 macOS 或 Linux 时，有时很难确保脚本在两个系统上都工作。想想 macOS/Linux 上的`rm -rf`和 Windows 上的`rmdir /s`就知道了。这两个库通过统一如何创建( [Mkdirp](https://github.com/substack/node-mkdirp) )和移除( [RimRaf](https://github.com/isaacs/rimraf) )文件夹层次结构帮了大忙。例如，在我执行 web 应用程序的干净重建之前，我用它来清理和重新创建我的`dist`或`output`文件夹。与`webpack`一起，你的`package.json`可能看起来像这样:

```
"scripts": {
  "prebuild": "rimraf dist",
  "build": "webpack --config build/webpack.config.js",
}
```

`npm`在`build`脚本之前自动调用`prebuild`脚本。在这个例子中，我们删除了 Webpack 的`dist`输出文件夹的内容，以移除先前构建中遗留的文件。

## 跨环境

当您的开发团队同时使用不同的操作系统时，处理环境变量也同样麻烦。 [Cross-Env](https://github.com/kentcdodds/cross-env) 通过提供统一的 CLI 来设置环境变量并与之交互，解决了这个问题。来自`cross-env`网站的例子展示了这一点:

```
"scripts": {
  "build": "cross-env NODE_ENV=production webpack --config build/webpack.config.js"
}
```

请注意，`cross-env`包处于维护模式，在 GitHub 中被标记为“archived”。不过，[这是好事](https://github.com/kentcdodds/cross-env/issues/257)！这意味着该工具被认为是功能完整和稳定的(理解为:“生产就绪”)，不会对其代码库进行进一步的更改或添加。

## Is-CI 和 Is-Docker

有些脚本应该只在本地运行，或者在持续集成环境中运行。为了帮助您轻松区分环境，请使用 [Is-CI](https://github.com/watson/is-ci) 。如果 CLI 工具从 GitHub 操作或 Azure 管道等 CI 环境中运行，它会成功，否则会失败。因此，您可以使用它来保护您的脚本，如下所示:

```
is-ci && echo "This is a CI server" || echo "Not a CI server"
```

如果您使用上面的 Husky，一个常见的模式是更新您在`package.json`中的`prepare`脚本，以便仅在您*而非*运行在 CI 服务器上时安装 Husky:

```
"scripts": {
  "prepare": "is-ci || husky install"
}
```

Is-Docker 以同样的方式工作，但是检查你是否在 Docker 容器中运行。

## 兼侍候

我想在本节中介绍的最后两个库非常适合彼此协同工作:[并发](https://www.npmjs.com/package/concurrently)和[等待](https://github.com/jeffbski/wait-on)。[并发](https://www.npmjs.com/package/concurrently)允许你并行启动几个任务。例如，如果你同时使用 Webpack 和 Nodemon，你可以使用`concurrently`同时启动它们:

```
"scripts": {
  "watch": "nodemon index.js",
  "dev-server": "webpack-dev-server",
  "start": "concurrently -k \"npm:watch\" \"npm:dev-server\""
}
```

Wait-On 解决了问题的另一面:[在继续执行脚本之前，等待特定任务完成](https://github.com/jeffbski/wait-on#why)。例如，如果您使用`concurrently`来启动数据库实例和后端服务，那么您可以使用`wait-on`来确保数据库在尝试连接之前启动并运行:

```
"scripts": {
  "start-db": "myDbServerCmd",
  "start-api": "wait-on tcp:27017 && myApiServerCmd",
  "start": "concurrently -k \"npm:start-db\" \"npm:start-api\""
}
```

# 类型安全

![](img/c2cb0588d78120d9986380a92ebb39d0.png)

如果你读过我过去写的任何东西，我是打字稿的忠实支持者应该不是秘密。Medium 上有很多关于 TypeScript 如何让你的生活变得更简单的帖子，所以我不会在这里重复这些观点。

## 类型同步

即使您使用 TypeScript，您可能也不知道一个叫做 [typesync](https://www.npmjs.com/package/typesync) 的很酷的小工具，它可以自动为您所有的本地包找到缺少的 TypeScript 类型定义。安装新的 NPM 包后，你只需运行`typesync`来添加你可能需要的任何类型定义:

```
npx typesync
```

并且，如果它发现一些缺失的依赖项，它会自动将它们作为开发依赖项添加到您的`package.json`中。只需再次运行`npm install`来安装它们。已经节省了我很多时间了！

# 结论

通过上面的列表，我希望我让您对 Node.js 包和工具有了一个很好的了解，我每天都在使用这些包和工具，并且在我从事的每个项目中都是如此。虽然他们中的一些可能是固执己见的选择，但他们都被证明对我来说一次又一次地工作良好和健壮。

然而，也要提醒一句:人们每天都发布新的 NPM 软件包，并不是所有的都是我所说的“生产就绪”。在向您的应用程序依赖项添加新的包时要小心，不要让它们显示出一个经过验证的跟踪记录。为了安全起见，请检查每周的高下载量、GitHub 明星的数量、多个不同的贡献者以及最近的提交历史。即使这样[你也不能百分百确定](https://cyware.com/news/analyzing-the-deadly-rise-in-npm-package-hijacking-78b24364)。