# Node.js 中带有 TypeScript 的路径别名

> 原文：<https://levelup.gitconnected.com/path-aliases-with-typescript-in-node-js-230803e3f200>

![](img/11ee1bfcded60f97e740adbe73b6dd95.png)

在本文中，我将指导您在 TypeScript 项目中设置路径别名，并向您展示如何清理代码。

## 问题是

在 Node.js(或一般的 TS/JS)中，您可以将模块导入到代码中。这可能如下所示:

```
import { User } from '../../user/model';
import { Article } from '../../article/model';import { Cache } from '../../../../cache';
import { MongoDB } from '../../../../mongodb';
```

注意这些点`../../`来访问父目录。

我们这里的问题是，你的项目树越深，就需要越多的`../`来访问更高层的模块。老实说，这看起来不太干净，很难理解，并且会使任何代码重组都非常痛苦。幸运的是，我们可以改变这种情况。

解决方案:**路径别名**

## 什么是路径别名？

在 TypeScript 中，您可以借助路径别名来避免这些看起来“糟糕”的导入。使用路径别名，您可以声明映射到应用程序中某个绝对路径的别名。

这里有一个简单的例子:

```
import { User } from '@components/user/model';
import { Article } from '@conponents/article/model';import { Cache } from '@services/cache';
import { MongoDB } from '@services/mongodb';
```

在这种情况下，这两个别名是

*   `@components`那就映射到`‘./src/api/components’`
*   `@services`映射到`‘./src/services’`

## 设置

让我们深入了解一下，并设置一些路径别名。假设我们有一个 Node.js 应用程序，其项目结构如下:

```
my-node-app
└───src
   │
   └───api
   │   │
   │   └───components
   │   │   │
   │   │   └───article
   │   │   │
   │   │   └───user
   │   │
   │   │   server.ts
   │
   │
   └───services
   │   │    cache.ts
   │   │    mongodb.ts
   │    
   │   index.ts
```

## **第一步:更新 tsconfig.json**

首先，我们必须在 tsconfig.json 文件中声明路径别名:

```
"baseUrl": "./src",
"paths": {
    "[@c](http://twitter.com/modules)omponents/*": ["api/components/*"],
    "[@services](http://twitter.com/services)/*": ["services/*"]
}
```

现在，您可以在应用程序中为模块导入使用新的路径别名。在你的 IDE /编辑器(在我的例子中是 VSC)中，或者在你编译代码的时候，不应该出现任何错误。

然而，我们还没有完成。正如我所说的，当你试图将 TS 代码编译成 JS 时，你不会看到任何错误。但是一旦运行编译好的 JS 代码，就会出现错误。例如:

> 错误:找不到模块“@components/user”

这是因为 JS 不能为声明的路径别名解析模块。

## **第二步:安装模块别名包**

接下来，我们将安装一个名为 [module-alias](https://www.npmjs.com/package/module-alias) 的 npm 包。

```
npm i --save module-alias
```

这个模块在编译的 JS 文件中注册路径别名。因此，我们需要对我们的 package.json 进行一些更改:

```
"_moduleAliases": {
    "[@c](http://twitter.com/modules)omponents": "dist/api/components",
    "[@services](http://twitter.com/services)": "dist/services"
  }
```

注意‘dist’是编译后的 JS 文件所在的文件夹。

最后但同样重要的是，我们必须在应用程序中注册路径别名。
将下面一行添加到启动文件的顶部:

```
import 'module-alias/register';
```

就是这样！最后，当您编译和执行代码时，您不应该看到任何导入错误。

**本文最初发表在我的博客上。看一看。**

 [## Node.js 中带有 TypeScript 的路径别名

### 2019 . 02 . 06 几天前，我在我的 TypeScript Node.js 项目中包含了路径别名。因为他们制定了代码…

拉斯瓦切特.德夫](https://larswaechter.dev/blog/nodejs-ts-path-aliases/) 

我目前正在做一个兼职项目，在这里你可以看到路径别名的作用。

[](https://github.com/Aionic-Apps/aionic-core) [## Aionic-Apps/aionic-core

### Aionic 为项目管理和协作提供开源应用程序。我们的重点是简化和…

github.com](https://github.com/Aionic-Apps/aionic-core)