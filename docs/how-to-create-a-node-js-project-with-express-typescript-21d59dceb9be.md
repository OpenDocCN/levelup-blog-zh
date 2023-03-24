# 如何用 Express+TypeScript 创建 Node.js 项目

> 原文：<https://levelup.gitconnected.com/how-to-create-a-node-js-project-with-express-typescript-21d59dceb9be>

关于使用 TypeScript 和 Express 初始创建 Node.js 服务器的简短教程。

![](img/96ce5ac75eab5212f51094354b2563c2.png)

潘卡杰·帕特尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

为什么我要写这个非常短的教程？我的意图解释起来相当简单:以后，我会写一些需要这样一个项目的文章。因为我不想一遍又一遍地写它，所以对于那些不确定如何开始这样一个 TypeScript 项目的人，我将简单地参考本文。

让我们首先用 TypeScript 设置一个简单的 Node.js 项目。

首先，我们创建一个新的项目目录。

```
$ mkdir event-sourcing-example
$ cd event-sourcing-example/
```

现在我们初始化这个目录中的节点。对于标志 *-y* ，使用默认设置，这对于示例性实现应该是好的。

```
$ npm init -y
```

现在让我们安装*类型脚本*依赖项。我们在运行时不需要这些，因此将它们作为*开发依赖项安装。*另外，我们还需要 Express 来运行一个 Node.js 服务器。

```
$ yarn add express
$ yarn add typescript tslint @types/express -D
```

现在必须添加一个带有 TypeScript 配置的文件。

```
$ touch tsconfig.json
```

将以下内容添加到该文件中。

```
{
  "compilerOptions": {
    "module": "commonjs",
    "esModuleInterop": true,
    "target": "es6",
    "moduleResolution": "node",
    "sourceMap": true,
    "outDir": "dist"
  },
  "lib": ["es2015"]
}
```

向 package.json 添加/替换以下部分

```
"main": "dist/server.js",
"scripts": {
  "dev": "tsc && node dist/server.js",
  "test": "echo \"Error: no test specified\" && exit 1"
},
...
```

创建一个“server.ts”文件，其中包含 Express 服务器的代码，如下所示:

```
$ mkdir src && touch src/server.ts
```

为了确保一切都是正确，您可以检查您的项目是否如下所示:

```
.
|-- node_modules
|-- package.json
|-- src
|   `-- server.ts
|-- tsconfig.json
`-- yarn.lock
```

然后将该代码添加到 server.ts 文件中。

```
import express from 'express';const app = express();
const port = 9000;app.get('/', (req, res) => {
    res.send('Server is up and running!');
});app.listen(port, () => {
    ***console***.log(`Server is listening on ${port}`);
});
```

太好了，我们现在可以运行服务器了。

```
$ yarn run dev
```

在另一个终端窗口(或浏览器)中，我们可以检查服务器是否正在运行。

```
$ curl localhost:9000
```

如果一切正常，那么我们应该看到输出“服务器启动并运行！”。

就是这样！现在你可以开始实现你的 API 或 app 了。

祝你好运！