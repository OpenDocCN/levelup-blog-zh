# 如何使用 TypeScript 和 Node.js 设置后端项目

> 原文：<https://levelup.gitconnected.com/how-to-set-up-a-back-end-project-using-typescript-and-node-js-c245fc09e0a2>

![](img/becdd5985a76b7770d36e46cc5e1904a.png)

[萨莎·弗里明德](https://unsplash.com/@sashafreemind?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们将使用 TypeScript 构建一个后端应用启动器，将其编译为 JavaScript(ES2019)，并将其部署到 Heroku。

我们将使用:

*   Node.js 12
*   express:node . js 的 Web 框架

源代码:[d-dmy tro/my-typescript-app](https://github.com/d-dmytro/my-typescript-app)

让我们为项目创建一个文件夹:

```
mkdir my-typescript-app
cd my-typescript-app
```

运行`npm init`在这个文件夹中创建 package.json 文件。我已经为选项选择了默认值。

让我们安装 TypeScript 和 Node.js 的类型作为开发依赖项。

```
npm i -D typescript @types/node@12
```

我安装了 Node.js 12 的类型，因为这是我在这个项目中使用的版本。如果您使用的是不同的版本，请将“12”替换为您的版本号(例如`@types/node@13`)。

我们将源文件(TS 文件)存储在“src”文件夹中。所以，让我们来创造它。

```
mkdir src
```

现在，我们应该配置 TypeScript 编译器，以便它根据我们的需要编译我们的代码。

在项目的根目录下创建 tsconfig.json 文件，复制并粘贴以下代码:

```
{
  "compilerOptions": {
    "target": "es2019",
    "module": "commonjs",
    "moduleResolution": "node",
    "outDir": "dist",
    "strict": true,
    "esModuleInterop": true
  },
  "include": ["src/**/*"]
}
```

我们使用`target`选项来设置 ECMAScript 的版本，我们希望我们的类型脚本代码被编译到这个版本中。我选择“es2019”是因为 Node.js 12 支持它。你可以在 [https://node.green](https://node.green) 网站上查看 Node.js 各版本支持什么。

我们使用`module`选项告诉 TypeScript 编译器在编译的文件中应该使用哪个模块系统。Node.js 使用了 CommonJS 模块系统，所以我把“module”选项设置为“commonjs”。

我们使用`moduleResolution`选项告诉编译器如何解析模块。我将该选项设置为“node ”,因为我们使用的是 Node.js。因此，当编译器看到导入语句时，它会像 Node 一样解析导入。

我们使用`outDir`选项告诉编译器在哪里存储输出。我想把它存储在相对于我们项目根目录的“dist”文件夹中。

我们将`strict`选项设置为“true ”,以便启用严格的类型检查。这个选项使编码变得有点困难，但是我们编写的代码变得更加安全。您很快就会习惯这个选项，它肯定会提高代码库的质量。

`esModuleInterop`选项支持 TypeScript 的模块(es 模块)和 CommonJS 模块之间的互操作性。之前，我们不得不使用一种变通方法，使用`import * as express from ‘express’`语法将 CommonJS 模块导入到一个变量中。启用此选项后，我们可以导入 CommonJS 模块，就像我们在 es 项目中所做的一样:`import express from ‘express’`。

最后，我们使用`include`选项列出我们想要编译的文件。这个选项允许我们使用类似 glob 的模式来指定文件。值“src/* */** **表示“src”文件夹中的任何子文件夹和任何 TypeScript 文件。您可以在这里阅读有关此选项和其他包含/排除文件的方法的更多信息:[https://www . typescriptlang . org/docs/handbook/ts config-JSON . html # examples](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#examples)

让我们在“src”文件夹中创建“index.ts”文件，复制并粘贴以下代码:

```
import express from 'express';

const port = 3000;
const app = express();

app.get('/', (_req, res) => {
  res.end('Hello World!');
});

app.listen(port, (err: Error) => {
  if (err) throw err;
  console.log(`Ready on port ${port}`);
});
```

此代码在端口 3000 上启动 express 应用程序。你可以在 [http://localhost:3000](http://localhost:3000) 访问它

让我们安装 express:

```
npm i -S express
```

Express 不包含类型定义，所以我们将从第三方类型存储库中安装它们，名为 DefinitelyTyped:

```
npm i -D @types/express
```

现在，让我们运行 TypeScript 编译器来编译我们的应用程序。为此，将“构建”脚本添加到“package.json”中。

```
"scripts": {
  "build": "tsc"
}
```

让我们运行脚本:

```
npm run build
```

npm 将运行编译器二进制文件(tsc)。编译器读取我们的“tsconfig.json”并编译我们的应用程序。

让我们将“start”脚本添加到“package.json”来运行编译后的应用程序。

```
"scripts": {
  "start": "NODE_ENV=production node dist/index.js"
}
```

现在我们可以启动应用程序了:

```
npm start
```

在 [http://localhost:3000](http://localhost:3000) 的浏览器中访问它

在目前的设置下，每次我们编辑代码时，我们都必须手动编译并重启应用程序。

就开发者体验而言，这不是我们所期望的。因此，让我们在开发过程中自动执行编译和重启任务。

我们将在“监视”模式下运行 TypeScript 编译器。它将观察我们的源文件的变化，并在我们保存它们时重新编译它们。

我们将使用 Nodemon 运行编译后的“dist/index.js ”,而不是直接使用 Node。Nodemon 也会监视我们的文件的变化。当我们保存一个文件时，它将重新启动我们的脚本。

要在“监视”模式下运行编译器，我们使用以下命令:

```
npx tsc -w
```

现在，让我们安装 Nodemon:

```
npm i -D nodemon
```

要使用 Nodemon 运行我们的应用程序:

```
npx nodemon dist/index.js
```

默认情况下，Nodemon 监视当前文件夹中的所有文件。我们不希望它监视所有文件，因为“src”文件夹是由编译器监视的，如果我们将文件保存在“src”文件夹中，Nodemon 将重新启动应用程序，编译器将编译文件并触摸“dist”文件夹，Nodemon 将再次重新启动应用程序。所以，我们会有一次不必要的重启。

为了解决这个问题，我们应该使用 Nodemon 的"-w "选项来告诉它应该监视哪些文件。让我们使用这个选项让 Nodemon 只监视“dist”文件夹。

```
npx nodemon -w dist dist/index.js
```

因此，为了达到我们想要的开发体验，我们应该一起运行编译器和 Nodemon。为了做到这一点，我们可以使用名为“并发”的 NPM 包。

让我们安装它:

```
npm i -D concurrently
```

并发接受多个命令，一起运行它们并管理它们的进程。

让我们一起运行 tsc 和 nodemon:

```
npx concurrently -k -n COMPILER,NODEMON -c gray,blue "tsc -w" "nodemon -w dist dist/index.js"
```

“-k”选项告诉并发地杀死它启动的所有进程，如果其中一个进程死亡的话。

“-n”选项标记我们启动的进程的输出。

“-c”选项设置每个进程输出的颜色。

打开" src/index.ts "，把" Hello World！"字符串到其他东西，并在浏览器中重新加载应用程序的标签。您应该会立即看到新的字符串。

最后，让我们将命令添加到“package.json”中的“dev”脚本中:

```
"scripts": {
  "dev": "concurrently -k -n COMPILER,NODEMON -c gray,blue \"tsc -w\" \"nodemon -w dist dist/index.js\""
}
```

就这样，你得到了一个简单的 web 应用后端项目，你可以用它来开发你的 web 应用的 API。

# 添加 HTML 模板

我们将为我们的视图使用 [ejs](https://www.npmjs.com/package/ejs) 模板引擎。

首先，让我们安装 ejs:

```
npm i -S ejs
```

我们将 HTML 模板存储在“视图”文件夹中(这是 Express 应用程序的默认设置):

```
mkdir views
```

让我们创建索引视图:“views/index.ejs”

```
<h1><%= greeting %></h1>
```

最后，我们在索引路由上渲染一下(" src/index.ts "):

```
// Tell Express to use ejs to render views.
app.set('view engine', 'ejs');

app.get('/', (\_req, res) => {
  res.render('index', {
    greeting: 'Hello World!'
  });
});
```

# 部署到 Heroku

首先，你要有一个 Heroku 账号和 Heroku CLI。请看一下如何在他们的网站上安装 Heroku CLI:[https://devcenter.heroku.com/articles/heroku-cli](https://devcenter.heroku.com/articles/heroku-cli)

要将应用程序部署到 Heroku，我们应该将代码添加到 git repo 中。

在应用程序的文件夹中，让我们运行:

```
git init
```

让我们加上这个”。gitignore "文件来避免在回购中提交我们不需要的东西:

```
dist
node_modules
npm-debug.log
.DS_Store
```

将 Heroku 的“引擎”设置添加到“package.json”中:

```
"engines": {
  "node": "12.x"
}
```

Heroku 使用“PORT”env 变量告诉我们的应用程序应该监听哪个端口。让我们打开“src/index.ts ”,将定义端口号的那一行改为:

```
const port = parseInt(process.env.PORT, 10) || 3000;
```

让我们提交代码:

```
git add .
git commit -m "Starter code"
```

登录 Heroku:

```
heroku login
```

使用 Heroku 创建应用程序:

```
heroku create
```

这将在您的 heroku 帐户中创建应用程序，并将名为“Heroku”的 git 遥控器添加到我们的 repo 中。当我们将一些东西推送到这个遥控器时，Heroku 将部署应用程序。那么，让我们部署我们的应用程序:

```
git push heroku master
```

运行`heroku open`在浏览器中打开您的应用程序。

请在这里找到更多关于将 Node.js 应用程序部署到 Heroku 的信息:[https://devcenter.heroku.com/articles/deploying-nodejs](https://devcenter.heroku.com/articles/deploying-nodejs)

*原载于*[*https://DD . engineering*](https://dd.engineering/blog/how-to-set-up-a-back-end-project-using-typescript-and-node-js)*。*