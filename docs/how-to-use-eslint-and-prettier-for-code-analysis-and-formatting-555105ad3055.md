# 如何使用 ESLint 和更漂亮的代码分析和格式化

> 原文：<https://levelup.gitconnected.com/how-to-use-eslint-and-prettier-for-code-analysis-and-formatting-555105ad3055>

![](img/35bacd10b4c76b5450eae642bd4671a9.png)

当在开发人员的代码库中进行代码分析和格式化时，ESLint 和 appearlier 几乎是两个最受欢迎的工具。他们非常擅长自己的工作，这就是为什么他们往往是项目设置的重要组成部分。

两者的区别在于，ESLint 通常负责查找和报告 ECMAScript/JavaScript 代码内部的不同模式。ESLint 被设计成只处理 JavaScript 文件，它在确保代码库的一致性和无明显错误方面非常成功。

另一方面，更漂亮的是固执己见的代码格式化程序。与 ESLint 不同，它支持多种语言，如 JavaScript、HTML、CSS、GraphQL、Markdown 和许多其他语言。这两种工具在开发人员社区中都得到了很好的支持，并且在大多数代码编辑器或 ide(例如 Visual Studio Code)中都有它们的扩展。

**Visual Studio 代码市场**
[ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
[更漂亮](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

**网站**
[https://prettier.io/](https://prettier.io/)
[https://eslint.org/](https://eslint.org/)

# 为什么应该使用 linter 和代码格式化程序

林挺是一种在应用程序准备投入生产之前，在开发模式下修复代码中问题的方法。许多这样的修复可以自动完成，你可以定制整个过程来满足你的团队的需求。当使用 Prettier 时，你可以自动格式化文件中的代码，这样可以节省你大量的时间和精力。

这也是你在代码审查中需要担心的一件事，因为它保证对每个人都是一样的。它在整个团队中强制执行相同的风格和代码质量，因此不会像在本地设置时那样出现冲突。

这是团队在处理项目时遵循的一个常见过程。通常有一个 ESLint 和 Prettier 文件，旁边有一个 ignore 文件，很像一个`.gitignore`文件，每个开发人员在使用 GitHub 这样的服务进行版本控制时都应该熟悉这个文件。主文件的格式可以是 JavaScript、YAML 或 JSON。我在这些例子中使用 JSON。

请参见下面的示例文件，它们都在一个项目中:

`.eslintignore`
`.eslintrc.json`
`.prettierignore`


# 创建 ESLint 和更漂亮的工作流设置

ESLint 和 because 文件可能会相互冲突，因为有时它们最终会检查相同的规则，这可能会导致重复。幸运的是，让他们两个一起工作是可能的。

# Visual Studio 代码设置

首先你需要安装 ESLint 和更漂亮的扩展。

然后进入代码->首选项->设置

您可以使用搜索框来搜索您安装的 ESLint 和更漂亮的扩展。保留默认的 ESLint 设置应该没问题，但是如果你想的话，你可以改变它们。更漂亮可能需要一些全局设置的变化，但你想怎么定制它。

这些是我拥有的最著名的。

*   更漂亮:半✅
*   更漂亮:单引号✅
*   更漂亮:结尾逗号 **es5**

在设置页面上搜索**格式**。

确保这些设置是正确的，您可能需要向下滚动以找到默认的格式化程序:

*   编辑:关于拯救✅的格式
*   编辑器:默认格式化程序**更漂亮—代码格式化程序**

# 普通 JavaScript/NodeJS 设置

打开命令行，然后转到像桌面这样的目录。运行下面的命令来设置您的项目。

```
mkdir backend
cd backend
npm init -y
npm install eslint eslint-config-prettier eslint-plugin-prettier --save-dev
```

现在，在同一个文件夹中运行下面的代码，并完成设置。

```
npm init @eslint/config
```

这些是我选择的设置:

✔:你喜欢用 ESLint 吗？— **检查语法，发现问题，并实施代码风格**
✔你的项目使用什么类型的模块？—**commonjs(require/exports)**
✔你的项目使用哪个框架？——**这些都不是**
✔你的项目使用 TypeScript 吗？——**否**
✔你的代码在哪里运行？— **Node**
✔你想如何为你的项目定义一种风格？——**使用流行风格指南**
✔你想遵循哪种风格指南？—[**Airbnb**](https://github.com/airbnb/javascript)
✔你希望你的配置文件是什么格式？— **JSON**

检查 eslint-config-Airbnb-base @ latest 的对等相关性

您选择的配置需要以下依赖项:

eslint-config-Airbnb-base @最新 eslint@⁷.32.0 || ^8.2.0

✔，你想现在安装吗？— **是**
✔你想用哪个包管理器？— **国家预防机制**

现在运行下面代码块中的命令，为 Prettier 创建文件。

```
npm install --save-dev --save-exact prettier
echo {}> .prettierrc.json
```

打开您的`.eslintrc.json`文件并添加这些配置设置。漂亮需要在数组的最后，否则它不会正常工作。

```
"extends": ["airbnb-base", "plugin:prettier/recommended"],"plugins": ["prettier"],
```

接下来，打开`.prettierrc.json`文件，复制并粘贴这些设置。您可以在[漂亮选项](https://prettier.io/docs/en/options.html)文档中了解这些设置。这只是我的设置，你可以根据自己的喜好创建自己的设置。

```
{
  "printWidth": 100, "semi": true, "singleQuote": true, "tabWidth": 4, "useTabs": false, "trailingComma": "none", "endOfLine": "auto"
}
```

最后，运行下面的代码为 ESLint 和 Prettier 创建一些忽略文件。它们的工作方式就像一个`.gitignore`文件，所以你在里面的任何条目都将被忽略，所以它们不会被打印或格式化。

```
touch .prettierignore .eslintignore
```

只需将相同的代码复制并粘贴到`.prettierignore`和`.eslintignore`文件中。

```
node_modules
package.lock.json
build
```

# 使用 ESLint 和更漂亮

让我们做一个快速测试。在你的编辑器中或者在终端中使用下面的命令创建一个`index.js`文件。

```
touch index.js
```

将这段 JavaScript 代码添加到文件中。

```
const x = 100;console.log(x);const test = (a, b) => {
  return a + b;
};
```

在代码编辑器中，您应该在底部的 Problems 选项卡中看到一些错误和警告。如果你通过在所有地方添加空格或制表符来降低代码的可读性，然后保存文件。漂亮点的人应该清理和修理一切。

在 console.log 和测试函数名下面应该有一条曲线。如果将鼠标悬停在它们上面，可以看到分配给它们的 ESLint 规则。转到`.eslintrc.json`文件，在底部添加这些规则。

```
"rules": {"no-console": "off","no-unused-vars": "off"}
```

现在，如果您返回到`index.js`文件，警告应该会消失！您可以在 [ESLint 规则](https://eslint.org/docs/rules/)文档中找到所有规则。也可以通过进入[漂亮选项](https://prettier.io/docs/en/options.html)页面来更改`.prettierrc.json`文件中的规则/选项。

## 重新启动 ESLint 服务器

有时，林挺在做出更改后无法工作。要在 Visual Studio 代码中解决这个问题，请运行命令 **Shift+CMD+P** 来显示命令面板，然后搜索**ESLint:Restart ESLint Server**。这将使林挺在所有文件中正常工作。

请记住，每次添加/删除规则或进行更改时，您可能都需要重新启动 ESLint 服务器。否则，规则可能不起作用，并可能导致 ESLint 和 Prettier 发生冲突。

# 反应堆设置

同样的设置也适用于 React 等其他 JavaScript 框架。你只需要选择合适的设置。请参见下面的示例。

记得选择 **JavaScript 模块(导入/导出)**，因为这是 React 使用的模块，代码将在浏览器中运行。

```
npx create-react-app my-app
cd my-app
npm install eslint eslint-config-prettier eslint-plugin-prettier --save-dev
npm init @eslint/config
```

现在运行下面代码块中的命令，为 Prettier 创建文件。

```
npm install --save-dev --save-exact prettier
echo {}> .prettierrc.json
```

打开您的`.eslintrc.json`文件并添加这些配置设置。漂亮需要在数组的最后，否则它不会正常工作。

```
"extends": ["plugin:react/recommended","airbnb","plugin:prettier/recommended"],"plugins": ["react", "prettier"],
```

接下来，打开`.prettierrc.json`文件，复制并粘贴这些设置。你可以在[漂亮选项](https://prettier.io/docs/en/options.html)文档中了解这些设置。这只是我的设置，你可以根据自己的喜好创建自己的设置。

```
{
  "printWidth": 100, "semi": true, "singleQuote": true, "tabWidth": 4, "useTabs": false, "trailingComma": "none", "endOfLine": "auto"
}
```

最后，运行下面的代码为 ESLint 和 Prettier 创建一些忽略文件。它们的工作方式就像一个`.gitignore`文件，所以你在里面的任何条目都将被忽略，这样它们就不会被打印或格式化。

```
touch .prettierignore .eslintignore
```

只需将相同的代码复制并粘贴到`.prettierignore`和`.eslintignore`文件中。

```
node_modules
package-lock.json
build
```

现在，如果您打开`App.js`文件，您应该会在下面的问题选项卡中看到警告和错误。如果您想要禁用规则，请遵循前面的步骤，并在 [ESLint 规则](https://eslint.org/docs/rules/)文档中找到规则。