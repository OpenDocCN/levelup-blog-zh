# 在世博会下创建 Jest。jsx 文件

> 原文：<https://levelup.gitconnected.com/setting-up-jest-under-expo-to-work-with-jsx-files-ba35a51bc25a>

![](img/9124f4b9a5490aa1549ff9bb413ab669.png)

我想了解 React 本地开发是创建跨平台应用程序的一种简单方法。我按照设置步骤进行了操作，我能够轻松地使用 Expo 来设置一个简单的应用程序，并让它在我的 iPhone 上运行。我想对我的项目做的一件事是用 Jest 添加单元测试。当您设置 Expo 时，它还包括 Jest 作为托管项目设置的一部分。这个简单的应用程序显示一切正常，但我想对我的项目结构做一些改变。

首先，我想把我所有的代码移动到一个 src 文件夹，里面有一个 components 文件夹。为此，我首先在项目中添加了一个 src 文件夹。然后，我将 App.js 文件移动到 src 文件夹中。因为 Expo 期望在项目的顶层文件夹中有一个 App.js 文件以便运行，所以我在顶层创建了一个新的 App.js 文件夹，它导入了实际文件的内容。所以我在顶层文件夹的 App.js 文件有如下内容:

```
import App from ‘./src/components/App’;
export default App;
```

这样，我的项目顶层就没有代码了，我可以测试我所有的代码。

接下来，我想使用。jsx 文件来跟踪哪些文件是简单的。js 文件以及哪些文件是。包含 React 本机代码的 jsx 文件。因此，在 src 文件夹中，我将 App.js 文件重命名为 App.jsx。我创建了一个 App.test.jsx 文件，内容如下:

```
import React from ‘react’;
import renderer from ‘react-test-renderer’;
import App from ‘./App’;describe(‘<App />’, () => {
    it(‘has 1 children’, () => {
        const tree = renderer.create(<App />).toJSON();
        expect(tree.children.length).toBe(1);
    });
});
```

当我对我的项目进行这些更改时，我无法让 Jest 工作。我一直收到这个错误:

```
({“Object.<anonymous>”:function(module,exports, require,__dirname,__filename,global,jest){import React from ‘react’;SyntaxError: Cannot use import statement outside a module
```

我花了很多时间研究这个错误，并尝试了很多方法来修复它。在看了很多其他项目并走进很多死胡同后，我找到了解决方案。我需要使用 babel-jest 来转换文件夹中的文件。

当前的 Expo 项目设置将所有 Jest 设置放在 package.json 文件中。默认情况下，它只包括以下几行:

```
"jest": {
    "preset": "jest-expo",
},
```

为了让我的更改生效，我需要为 Jest 添加另一个设置。下面是我对 package.json 文件中 Jest 设置的更改:

```
"jest": {
    "preset": "jest-expo",
    "transform": {
        "^.+\\.[jt]sx?$": "babel-jest"
    }
},
```

当我做了这个更改后，我可以运行 npm test 并让它测试我的文件。这是个好消息。

奇怪的是，当你查看 Jest 文档时，对于 transform，它显示我使用的转换应该是默认值。但出于某种原因，事实并非如此。要查看 Jest 的运行配置，可以使用 Jest 命令行选项— showConfig。在您的 shell 中，您可以运行以下命令:

`npm test — — showConfig`

这将显示 Jest 正在使用的所有当前配置。当我查看输出时，我发现 Expo 设置中有这个用于 babel-jest 的转换正则表达式:

```
"transform": [
    ["^.+\\.(js|ts|tsx)$",
    "/Users/foo/git/expo-app/node_modules/babel-jest/build/index.js"], ["^.+\\.(bmp|gif|jpg|jpeg|mp4|png|psd|svg|webp|ttf|otf|m4v|mov|mp4|mpeg|mpg|webm|aac|aiff|caf|m4a|mp3|wav|html|pdf|obj)$",
    "/Users/foo/git/expo-app/node_modules/jest-expo/src/preset/assetFileTransformer.js"]
],
```

你会注意到正则表达式是 bable-jest 的`“^.+\\.(js|ts|tsx)$”`。Jest 只会转换以结尾的文件。js，。注意这一点。正则表达式中完全没有 jsx 文件。所以我的改变增加了转变。jsx 文件放回到 Jest 的流程中。

希望这有助于您按照自己的方式设置 Expo 项目，并向您展示一个很好的调试标志，用于发现 Jest 的问题。