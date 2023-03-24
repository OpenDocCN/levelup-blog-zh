# 如何使用 TypeScript 编译器创建项目

> 原文：<https://levelup.gitconnected.com/how-to-create-projects-with-the-typescript-compiler-afd00313fb2d>

![](img/82dc083027cb42ae21dd6473f0dbf36b.png)

照片由[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将了解如何使用 TypeScript 编译器创建项目并编写一些代码。

我们还将了解如何安装包，版本号意味着什么。

此外，我们看看典型的 TypeScript 项目的项目结构。

# 入门指南

我们可以通过创建项目文件夹来创建文件夹。

然后在里面，我们运行:

```
npm init --yes
```

创建一个`package.json`文件。

然后，我们通过运行以下命令来安装节点包:

```
npm install --save-dev typescript
npm install --save-dev tsc-watch
```

分别安装 TypeScript 编译器和一个程序，以便在程序发生变化时重新加载我们的程序。

所有内容都将被安装到`node_modules`文件夹中。

然后我们添加一个`tsconfig.json`来添加我们的编译器选项，并添加:

```
{  
  "compilerOptions": {  
    "target": "es2018",  
    "outDir": "./dist",     
    "rootDir": "./src"  
  } 
}
```

然后我们构建的代码会在`dist`文件夹中，我们的代码应该在`src`文件夹中。

`target`是我们构建工件的目标 JavaScript 版本。

# 编写代码

然后，我们可以在`src`文件夹中创建一个`index.ts`文件，并开始编写一些代码。

例如，我们可以写:

```
const print = (msg: string): void => {
  console.log(msg);
};print("hello");
```

我们创建了一个接受`msg`参数的`print`函数。

冒号后的单词`string`是参数类型。

`void`是函数的返回类型。这意味着我们的函数不返回任何值。

# 项目结构

TypeScript 项目文件夹由以下各项组成:

*   `dist` —包含编译器输出的文件夹
*   `node_modules` —包含应用程序和开发工具所需的包的文件夹
*   `src` —包含将由 TypeScript 编译器编译的源代码文件的文件夹
*   `package.json` —包含项目的顶层包依赖项的文件夹
*   `package.json` —包含项目的包依赖关系的完整列表的文件
*   `tsconfig.json` —具有 TypeScript 编译器的配置设置的文件

# 节点程序包管理器

大多数 TypeScript 需要来自外部的依赖。

NPM 拥有最多的 JavaScript 和 TypeScript 项目的 TypeScrtipt 包。

由于 TypeScript 是 JavaScript 的超集，我们可以在 TypeScript 代码中使用任何 JavaScript 包。

NPM 遵循依赖链来计算每个包需要哪个版本，并自动下载所有内容。

用`--save-dev`或`-D`选项保存的任何东西都保存在`package.json`的`devDependencies`部分。

![](img/7cfd8f1ee54bbeb096f0958169613255.png)

照片由[埃米利亚诺·维托里奥西](https://unsplash.com/@emilianovittoriosi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 全球和本地软件包

包管理者可以安装包，使它们特定于单个项目。

这是默认选项。

需要在计算机上运行的一些软件包可以作为全局软件包安装。

例如，TypeScript 编译器将是一个全局包，因为我们在整个项目中都需要它。

节点包大多使用语义版本化。

还有一些符号以不同的方式表示包的版本。

例如，我们可以用`1.0.0`准确地表示版本号。

`*`接受要安装的任何版本的软件包。

`>1.0.0`或`>=1.0.0`意味着我们接受大于或等于给定版本的任何版本的包。

`<1.0.0`或`<=1.0.0`意味着我们接受小于或等于给定版本的版本。

`~1.0.0`表示即使补丁级别号不匹配，我们也接受要安装的版本。

所以`1.0.1`被认为等同于`1.0.0`是一个以`~`为前缀的版本号。

`^1.0.0`将接受任何版本，即使次要版本号或补丁号不匹配。

所以如果版本号前面有一个`^`，那么`1.0.0`和`1.1.1`被认为是等价的。

# 结论

节点包可以追溯到语义版本化。我们可以在`package.json`中指定是否需要精确匹配。

我们可以添加 TypeScript 编译器来开始一个 TypeScript 项目。

一旦有了这些，我们就可以将特定于 TypeScript 的代码添加到我们的项目中。

此外，我们可以指定 TypeScript 项目的构建目标版本。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)