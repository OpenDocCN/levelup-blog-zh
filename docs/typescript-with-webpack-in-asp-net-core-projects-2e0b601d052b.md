# ASP.NET 核心项目中使用 WebPack 的类型脚本

> 原文：<https://levelup.gitconnected.com/typescript-with-webpack-in-asp-net-core-projects-2e0b601d052b>

![](img/cd373823145acb36303ddbdd68b44b01.png)

# 动机

假设您正在开发一个 ASP.NET 核心 web 应用程序，它解决了一些与业务相关的任务。你知道，一些用户输入数据并得到一些报告的表单。

尽管这样的项目可能不需要客户端的任何复杂逻辑，但您可能仍然需要编写一些 JavaScript 代码，以使用户与您的应用程序的交互更加方便和愉快。例如，你可能需要一个简单的删除条目的弹出提示，因为用一个单独的页面是不太合适的。或者，您希望进行客户端验证。或者……它真的可以是任何其他客户端任务，你说吧。

当然，您可以在每个页面上添加几行脚本(使用普通的 JavaScript 或者使用传统的 JQuery ),但是当您的项目变大时，很难维护所有这些小部分。此外，其中一些部分做相同的事情，所以您要么需要在许多地方复制它们(糟糕的决定)，要么最终创建一个包含项目中使用的所有客户端函数、结构和类的小库。

本文描述了如何用最少的努力为您的 ASP.NET 核心项目创建这样一个 JS 库，并且以一种为进一步的变更提供更好支持的方式。

# **解决方案**

长话短说，我们将把所有的客户端代码放在单独的文件中(包括类、函数、数据结构等等)。)然后在 [WebPack 5](https://webpack.js.org) 的帮助下进行捆绑。生成的脚本可以直接包含在你的 *_Layout.cshtml* 中(因此，它将在你的 web 应用程序的所有页面上可用)，或者你可以只在必要的页面上包含它。

此外，我们将使用 TypeScript 而不是纯 JavaScript，因为，你知道，它是现代的和闪亮的，静态类型很好，允许我们在编译时捕捉许多错误。

此外，您可以将本文视为对客户端开发的快速介绍。尤其是如果你是一个. NET 开发人员，仍然倾向于只做后端，并且害怕所有那些花哨的客户端东西(就像我不久前一样)。

# **入门**

在这里，我们将描述设置将小型 TypeScript 库与您自己的代码捆绑在一起的配置所需的步骤。

为了简化起见，我们的库现在只包含一个函数。

## **第 0 步:安装节点。JS**

我很确定你已经安装了。万一你没有-请做吧。我们需要节点。您的开发/构建机器上的 JS 版本 10.13.0(或更新版本)。

## 第一步:创建 ***`ClientScript`* 子文件夹**

我们将把所有的脚本和配置文件放到主项目文件夹的一个单独的子文件夹 *ClientScript* 中。

它类似于 ASP.NET 核心可用的大多数 SPA(单页应用程序)模板中使用的 *ClientApp* 子文件夹。

## 第二步:**添加配置文件**

我们需要 3 个配置文件:

*   *package.json* 定义我们的包和所有依赖项。
*   *webpack.config.js f* 或 webpack 配置。
*   *tsconfig.json* 进行打字稿设置。

现在，您可以按原样复制这些文件。我们将在后面描述它们中的每一个。

package.json tsconfig.json 和 webpack.config.js 文件

## **第三步:添加打字稿文件**

为了简单起见，在这个初始阶段，我们的库将只包含一个函数 *hello()* ，它只是将“Hello world”打印到浏览器的控制台。这是我们需要的两个文件，以实现出色的:)功能:

这里的`hello.ts`包含了我们打包到`funcs`名称空间中的函数，所以我们可以把它叫做`MYAPP.funcs.hello()`，而`index.ts`是我们的入口点。以前的 TypeScript 文件将不包含任何函数或类。它将定义我们的代码的哪些部分(函数、接口、类等)将向外界公开。因为我们只有一个具有“真正”功能的文件，所以我们的`index.ts`只包含一行代码，它只导出(“揭示”)来自`hello.ts`的所有公共(导出)代码

## 第四步:建立你的图书馆

就是这样。我们已经准备好构建我们的包脚本了。为此，请打开您的终端程序，移动到“ClientScript”文件夹并运行以下两个命令:

`> npm install`

然后:

`> npm run build`

第一个将安装所有必要的 NPM 库(在您的 *package.json* 的`dependencies`和`devDependencies`部分列出的库)。您将需要在第一次构建之前运行它，然后只有当您添加一个新的依赖项(另一个 NPM 包)时才运行它。

第二个命令实际上运行 WebPack，它将您的 TypeScript 文件编译(或者更准确地说是“transpiles”)为 JavaScript，然后将所有 JS 代码捆绑到一个文件 *app-client.js、*中，最后将该文件放入 web 项目的 *wwwroot/js* 文件夹中，如 *webpack.config.js* 配置文件中所定义。

根据该文件的`output/library`部分，新包的所有函数或结构都可以通过`MYAPP`全局变量来访问。

## **第五步:将最终脚本附加到你的应用程序上**

要使用我们的脚本，您只需将它作为任何其他 JS 文件包含在您的页面中:

`<script src="/js/app-client.min.js"></script>`

您可以将该行添加到 *_Layout.cshtml* (使其在您的 web 应用程序的所有页面上都可用)或者只添加到视图或 Razor 页面中的必要位置。

现在，您可以从我们新的 JS 库中调用函数:

```
<script>
MYAPP.funcs.hello();
</script>
```

# **配置范围**

使用 TypeScript 和 WebPack 的一个很好的特性是将代码组织成模块，然后使用 WebPack 的配置和 TypeScript 的`namespace`结构将这些模块组合成名称空间。

有几种可能的选择。

## **1。使用模块名及其别名**

您可以将函数和类放在一个模块中，然后“按原样”或使用别名导出该模块。

例如，如果我们有下面的*对话框*模块:

我们在我们的 *index.ts* 中使用此出口声明:

```
export * from ‘./dialogs’;
```

然后我们的`Dialog`类和`showDialog()`函数将在`MYAPP`名称空间下作为`MYAPP.Dialog`和`MYAPP.showDialog()`可用。

您还可以为“对话”模块指定一个别名:

```
export * as dlg from './dialogs';
```

现在我们的类和函数将相应地作为`MYAPP.dlg.Dialog`和`MYAPP.dlg.showDialog()`被访问。

## **2。使用** `**namespace**` **子句**

您还可以使用`namespace`子句，然后重新导出导入的模块，这样即使在不同的模块中，所有属于同一名称空间的函数、变量和类型都将被合并在一起。

例如，我们有以下两个模块:

现在，如果我们在“index.ts”模块中放置下面两行代码

```
export * from ‘./dialogs’;
export * from ‘./widgets’;
```

我们将能够在`MYAPP.ui`名称空间下访问所有那些导出的函数和类。例如:`MYAPP.ui.renderWidget1()`

# **使用第三方库**

也许这种设置的最大优势(对于一个“hello world”函数来说，这似乎有点复杂)是可以使用来自 [NPM 库](https://www.npmjs.com/)上成千上万可用的第三方 JS 库。

为了演示这种可能性，我们将稍微修改我们的`hello()`函数，使它接受一个参数(`name`)，并将短语“ *Hello，{ name }”*打印到控制台。在打印之前，存储在`name`变量中的字符串将被众所周知的 *lodash* 库中的函数`capitalize()`大写。

以下是我们实现这一目标应该采取的步骤:

## 步骤 1: **将“lodash”库添加到您的 package.json 中**

只需在您的 *ClientScript* 文件夹中打开一个终端，并键入:

```
npm install lodash
```

作为该操作的结果，您将在您的 *package.json* 文件的`dependencies`部分看到类似如下的内容:

```
"dependencies": {
  "lodash": "^4.17.21"
}
```

注意:实际版本号可以不同。

## **步骤 2:在 hello.ts 文件中导入“lodash”函数**

在 *hello.ts* 的开头加上下面一行:

```
import * as _ from ‘lodash’;
```

## **3。修改“hello()”功能**

现在我们可以通过使用`_`全局变量来使用所有 *lodash* 库函数(这是使用 *lodash* 函数的默认方式，因为它不是 NPM 库。

因此，我们的`hello()`函数现在看起来如下:

此外，我们将修改页面上的函数调用:

```
<script>
MYAPP.funcs.hello('sergiy');
</script>
```

当我们重新构建脚本(`>npm run build`)，运行应用程序，并打开主页时，我们将在浏览器的控制台面板中看到以下字符串:

```
> Hello, Sergiy
```

# **观看模式**

没有必要在每次修改脚本或者向项目中添加新的包时都运行 *build* 命令。相反，您可以使用特殊的*监视*模式(它是由 WebPack 提供的开箱即用模式)，因此每当我们的源文件或项目配置中的某些内容发生更改时，WebPack 都会自动重新构建您的项目。要启动观察模式，只需在你的终端窗口输入

```
> npm run watch
```

# **结论**

正如我们所看到的，使用 TypeScript 和 WebPack 为您的 ASP.NET 核心项目构建普通的 JS 客户端脚本有很多好处:

*   具有最新 JavaScript 特性所有优点的强类型类型脚本代码:类、箭头函数、模块、作用域和承诺。
*   代码编辑器(如 Visual Studio 代码)通过语法高亮、智能感知等提供更好的支持。
*   有可能使用带有类型定义的三方库。
*   WebPack 生成的更加紧凑和优化的 JS 5 代码。
*   热重新加载您在代码编辑器中所做的更改。
*   更好的调试体验。可以调试原始的 TypeScript 代码，而不是 web 应用程序可用的最小化 JS 代码。

还有一个注意事项。我们在本文中使用了 *WebPack* ,因为它是目前最流行的模块捆绑器。然而，我相信我们可以很快得到与任何其他捆绑器相同的结果，如 *Browserify* 、 *Parcel* 或 *Rollup* 。

请让我知道(通过我的 Twitter [@korzhs](https://twitter.com/korzhs) 或在评论中)这篇文章对你是否有价值和有帮助。

编码快乐！