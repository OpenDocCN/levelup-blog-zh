# 使用 Visual Studio 代码增强 React 开发人员的体验

> 原文：<https://levelup.gitconnected.com/enhance-your-react-developer-experience-with-visual-studio-code-43ce139a724>

## 享受更好的林挺、测试和调试

![](img/bf2dd8a812ec400ef3d74d279f7d9c0e.png)

诺亚·西利曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 介绍

我在这里向您展示如何破解 Visual Studio 代码，并让您的 React 开发者体验一路达到最棒的*。*

***剧透预警！**通过回顾[这个提交](https://github.com/bacongravy/create-react-app-vscode-dx/commit/53bd9bf6c548156db7efed37f0eafc6c092b9a80)，你可以看到本教程中所做的修改。*

## *Visual Studio 代码*

*我喜欢 Visual Studio 代码编辑器。它对 JavaScript 和 TypeScript 有很好的支持。从表面上看，它可能看起来像一个简单的文本编辑器，但是通过正确的扩展和配置，它的功能可以与专业 ide 相媲美:智能代码完成、连续林挺、连续样式化、连续测试和交互式调试。*

## *反应*

*React 库是构建前端 web 应用程序的一种流行而有趣的方式。 [Create React App](https://create-react-app.dev) 项目是快速上手 React 的一个神奇方式。Create React App 生成的项目包括现代林挺、编译、捆绑和测试，所有这些都已配置就绪，但具有以命令行为中心的开发人员体验。我将向您展示如何在 Visual Studio 代码中释放项目的潜力。*

## *入门指南*

*您可以将以下技术应用于 Create React App 生成的现有项目，但如果您想在阅读时有一种简单的方法，您可以生成一个新的项目。*

***用随机生成的名字新建一个项目，进入项目目录**:*

```
*PROJECT=$(npx project-name-generator -o dashed)
npx create-react-app $PROJECT
cd $PROJECT*
```

***亲提示！**这些技术中的许多可能适用于 React 项目*而不是由 Create React App 生成的*。*

# *智能代码完成*

*Visual Studio 代码包括一个称为 IntelliSense 的智能代码完成功能。如果你在你的项目中使用 TypeScript，那么你已经达到了最棒的水平。恭喜你！如果您在项目中使用 JavaScript，那么您仍然可以通过在所有 JavaScript 文件中启用类型脚本检查来进入下一个级别，如 Visual Studio 代码文档中关于[类型检查 JavaScript](https://code.visualstudio.com/docs/nodejs/working-with-javascript#_type-checking-javascript) 的描述。但是您不需要阅读文档，因为我将向您展示如何操作！*

***如果需要，在您的工作区中创建一个** `**.vscode**` **目录，然后在该目录中创建或更新一个** `**settings.json**` **文件，内容如下:***

```
*{
  "javascript.implicitProjectConfig.checkJs": true
}*
```

***亲提示**！如果您的项目中已经有一个`jsconfig.json`文件或一个`tsconfig.json`文件，那么您应该在那里设置`"checkJs": true`。*

*除了增强智能感知之外，`checkJs`设置还为 JavaScript 文件启用了 TypeScript linter。如果您在使用 Create React App 生成项目时没有指定 TypeScript 模板，那么在启用此功能后，您可能会注意到 Visual Studio 代码抱怨在您的测试中使用了全局变量。这是因为我们还没有为 Jest 安装 TypeScript 类型，TypeScript 语言服务不知道这些全局变量是允许的。TypeScript 模板中还缺少其他类型。*

*为 Jest 和其他依赖项安装缺少的类型脚本类型:*

```
*npm install @types/jest @types/node @types/react @types/react-dom*
```

***注意！**直到[这个 pull 请求](https://github.com/facebook/create-react-app/pull/8406)被合并并释放，Create React App 模板安装的版本`@testing-library/jest-dom`在 Visual Studio 代码期望找到的位置不包含 TypeScript 类型信息。如果在您阅读本教程时 Create React App 模板尚未更新，或者您正在使用一个较旧的项目，则您必须自己升级该包，以使类型信息可用于 Visual Studio 代码。**升级至**的最新版本`**@testing-library/jest-dom**`:*

```
*npm install @testing-library/jest-dom@latest*
```

# *连续林挺*

*使用 Create React App 创建的项目具有[内置的林挺规则](https://github.com/facebook/create-react-app/tree/master/packages/eslint-config-react-app)，当您运行开发服务器或构建项目时会自动检查这些规则。通过在 Visual Studio 代码中启用连续林挺，您可以将开发提升到下一个级别。连续林挺让您在键入时看到应用于代码的林挺规则。要启用此功能，您需要为 Visual Studio 代码安装 [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 扩展。*

*如果你想让你的林挺达到最棒的状态，我建议你也启用保存时修复行为，每次你保存时，每个可修复的林挺问题都会自动修复。**用以下内容创建或更新**`**.vscode/settings.json**`**:***

```
*{
  "editor.codeActionsOnSave": { "source.fixAll.eslint": true }
}*
```

# *连续造型*

*Create React App 提供的 ESLint 的`react-app`配置相对来说是不独立的。有许多可能的 ESLint 规则它并没有表明立场，尤其是那些风格上的规则。虽然您可以尝试在您的配置中启用单独的 ESLint 规则来强制实施特定的样式，但是将您的代码格式化提升到下一个级别的更简单的方法是使用[appellister](https://prettier.io)，这是一个固执己见的代码格式化程序。*

*更漂亮的不仅仅是配置规则，它还扩展了 ESLint 的功能。更漂亮地删除所有原始样式，并确保所有输出的代码符合一致的风格。它获取你的代码并从头开始重新打印，同时考虑到代码行的长度。为了将 pretty 与我们项目的 ESLint 配置集成，以使样式违规显示为林挺错误，首先**安装 pretty 并集成**:*

```
*npm install -D prettier
npm install -D eslint-config-prettier
npm install -D eslint-plugin-prettier*
```

*然后**用以下内容**创建一个 `**.prettierrc**` **文件:***

```
*{
  "singleQuote": true
}*
```

*这是与使用 Create React App 生成的项目中的现有样式相匹配所需的最低配置。更多可能性请查看关于选项的[更漂亮的文档。](https://prettier.io/docs/en/options.html)*

*最后，**在** `**package.json**`中更新您的 ESLint 配置:*

```
*"eslintConfig": {
  "extends": [
    "react-app",
    "plugin:prettier/recommended",
    "prettier/flowtype",
    "prettier/react"
  ]
},*
```

***普罗提普！**在一些项目中，ESLint 配置可能存在于`.eslintrc`而不是`package.json`中。*

*这是允许 Prettier 扩展 ESLint 的最小配置，并适当地覆盖所有需要改变的来自`react-app`的规则。*

*beauty 添加的格式规则都可以被 ESLint 修复，所以为了达到最大的效果，请确保**启用 ESLint 的保存时修复行为，如上面**的连续林挺部分所述。现在你的代码将会在每次保存时被自动样式化。*

# *连续测试*

*Create React App 生成的项目包括 Jest 测试运行程序，并被配置为在运行`npm test`时以观察模式运行 Jest。每当您的代码发生变化时，您的测试都会自动重新运行。Jest 已经是下一个级别了，但是您可以通过将它与 Visual Studio 代码集成来将这种体验提升到最棒的水平。*

*Visual Studio 代码有一个扩展，每当您的工作区打开时，它会自动在监视模式下为您运行 Jest。该扩展在编辑器中与测试代码一起显示结果，并在问题查看器中收集测试失败。该扩展还支持使用 Visual Studio 代码中内置的 NodeJS 调试器来调试测试。*

***安装**[**Jest**](https://marketplace.visualstudio.com/items?itemName=Orta.vscode-jest)**扩展**将持续测试集成到 Visual Studio 代码中。该扩展自动识别用 Create React App 创建的项目，不需要任何配置。*

# *交互调试*

*调试 web 应用程序的传统方式是使用 [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) ，它是作为 [Chrome](https://www.google.com/chrome/) web 浏览器的一部分提供的，包括一个交互式调试器和控制台。DevTools 很棒，但是如果你想在调试的时候修改代码，使用它们需要在浏览器和编辑器之间切换。为了让调试更上一层楼，您可以将 Visual Studio 代码的内置调试器连接到在 web 浏览器中运行的代码，并直接从 Visual Studio 代码中调试您的应用程序。在 [Visual Studio 代码文档](https://code.visualstudio.com/docs/nodejs/reactjs-tutorial#_debugging-react)和 [Create React App 文档](https://create-react-app.dev/docs/setting-up-your-editor#visual-studio-code)中有简单的例子。*

*这些例子不错，但是你可以做得更好——好得多。以下有关介质的教程演示如何启用调试工作流，该工作流将在开始调试会话时自动启动项目的开发服务器并启动 web 浏览器，并在结束调试会话时停止开发服务器。**查看并跟随:***

*[](https://medium.com/better-programming/take-your-create-react-app-debugging-workflow-to-the-next-level-9c40cd1904bd) [## 让您的 Create React 应用程序调试工作流程更上一层楼

### 像专家一样调试 VS 代码

medium.com](https://medium.com/better-programming/take-your-create-react-app-debugging-workflow-to-the-next-level-9c40cd1904bd) 

在本教程的最后，你会发现一个调试配置文件，它为 *maximum awesome* 设置了 Edge、Chrome 和 Jest。

# 结论

我希望您学到了一些关于如何使用 Visual Studio 代码增强 React 开发体验的新知识。编码快乐！*