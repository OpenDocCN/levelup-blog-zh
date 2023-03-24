# Node + TypeScript + Mocha + ESLint

> 原文：<https://levelup.gitconnected.com/node-typescript-mocha-eslint-efad2e3d2943>

*使用 Node、TypeScript、Mocha 和 ESLint 设置编程项目的指南。*

![](img/ee7840ba6afd998fc577bc2550f0883f.png)

[斯坦利戴](https://unsplash.com/@stanleydai?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

应该建立编程项目以使开发变得容易。要做到这一点，项目应该具备以下能力。

1.  在开发过程中观察源文件。
2.  在开发过程中观察测试文件。
3.  提交时的 Lint 文件。
4.  将文件建立到一个`dist`文件夹中(并提供给他们)。

建立一个项目是主观的，但我希望这能给你一个好的起点。

如果你想跳转到代码，使用我创建的名为[node-typescript-mocha-eslint](https://github.com/christo8989/node-typescript-mocha-eslint)的 git 模板。这不是一个非常原始的名字，但它完成了任务。

*注:回购中的文件可能会发生变化。检查回购的最新代码。*

# 安装软件包

当您从模板创建项目时，使用`npm`或`yarn`来安装这些包。

```
npm install
yarn install
```

在这一点上，两者之间的差异可以忽略不计，所以只需选择一个。

# 在开发过程中观察源代码

在开发过程中，您会希望在保存更改时重新编译程序。为此，启动一个开发服务器，它将在您编码时保持运行。

下面的命令做了完全相同的事情。

```
npm start
npm run start:devyarn start
yarn start:dev
```

这些命令通过 nodemon 用名为`.nodemonrc.json`的配置文件启动一个节点服务器，从文件`src/index.ts`开始。

## `.nodemonrc.json`

```
{
  "restartable": "rs",
  "ignore": [".git", "node_modules/", "dist/"],
  "watch": ["src/"],
  "execMap": {
    "ts": "node -r ts-node/register"
  },
  "ext": "js,json,ts"
}
```

nodemon 包使开发服务器在您编码时保持运行。

1.  `"restartable": "rs"`指定重新启动已经运行的服务器。
2.  `"ignore": [...]`查找更改时忽略文件/文件夹列表。
3.  `"watch": [...]`在查找更改时监视文件/文件夹列表。
4.  `"execMap": {...}`是一个键/值，其中键是文件类型，值是构建这些文件的命令。对我们来说，我们只需要它来传输打字稿文件。
5.  `"ext": "..."`是一个逗号分隔的值，标识在查找更改时要监视的文件类型。

让我们更深入地研究 TypeScript 配置，以便理解`execMap`在做什么。

## tsconfig.json

一般来说，默认配置将带您去您需要去的地方。然而，我已经修改了这个文件，以便能够用 es5 语法导入模块。

```
import { hello } from '~/hello'
```

因此，这些选项中的大多数都支持该语法。

```
{
  "ts-node": {
    "compiler": "ttypescript"
  },
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "outDir": "./dist",
    "baseUrl": "./src",
    "paths": {
      "~/*": ["*"]
    }, 
    "plugins": [
      { "transform": "typescript-transform-paths" }
    ],
  }
}
```

1.  使用`ts-node`时，`"compiler": "ttypescript"`将使用`ttsc`而不是`tsc`来传输打字稿文件。
2.  `"target": "es5"`是要转换的 javascript 版本。
3.  `"module": "commonjs"`是进口到 transpile 的款式。
4.  是文件传输到的目录。
5.  `"baseUrl": "./src"`是项目的文件夹，将被识别为传输的根文件夹。
6.  `"paths": {...}`允许您指定映射到项目中文件夹/文件的特殊路径。这些路径相对于 baseUrl。
7.  `"plugins": {...}`定义你想要包含的插件，以帮助转换代码。

为了让`typescript-transform-paths`工作，我们必须使用`ttsc`来传输类型脚本文件。这就是为什么`ts-node`被配置为使用`ttsc`作为编译器。

插件`typescript-transform-paths`执行下面的转换。

```
import { hello } from "~/hello"// will transpile tovar hello_1 = require("./hello");
```

对于`tsconfig.json`文件有更多的选项。如果你想深入研究，我推荐你去探索文档。

如果你已经做到这一步，我们已经涵盖了大部分的设置。其他一切都建立在这个基础之上。

# 在开发过程中观察测试

当您进行更改时，测试命令将运行并重新运行测试。这有助于我们毫无争议地根据测试进行编码。

```
npm test
yarn test// Alternative
npm test -- --watch=false
yarn test --watch=false
```

我很失望 mocha 不能在不添加更多脚本的情况下测试单个文件；本来想写一个类似 `*npm test src/index.test.ts*` *的命令。我还发现它不用* `*fdescribe*` *。有其他方法可以用 Mocha 测试一个文件，但是我推荐使用 Jest 或者其他测试包。我们还是继续吧。*

## . mocharc.json

```
{
  "require": ["ts-node/register"],
  "watch-files": ["./src/**/*.ts"]
}
```

1.  `"require": ["ts-node/register"]`将使用 ts-node 传输测试代码。
2.  `"watch-files": [...]`是一个文件夹/文件的列表，在我们编码时要注意其中的变化。我们希望观察测试和代码的变化。

# 提交并推动变更

我在这个模板中添加了 eslint、husky 和 lint-staged，因为林挺是保持代码整洁和捕捉简单的恶魔错误的最简单的方法之一。

## . eslintrc.json

```
{
  "env": {
    "node": true,
    "es2021": true
  },
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaVersion": 12,
    "sourceType": "module"
  },
  "plugins": [
    "@typescript-eslint"
  ],
  "rules": {
    "comma-dangle": ["error", "always-multiline"],
    "indent": ["error", 2],
    "linebreak-style": ["error", "windows"],
    "quotes": ["error", "single"],
    "semi": ["error", "always"]
  }
}
```

1.  `"env": {...}`是棉绒识别的环境列表。这些环境并不相互排斥，因此鼓励使用多个值。
2.  `"extends": [...]`将导入指定的配置。
3.  `"parser": "@typescript-eslint/parser"`指定使用什么解析器。
4.  `"parserOptions": {...}`指定解析器的选项。
5.  `"plugins": [...]`指定要使用的插件。
6.  `"rules": {...}`是规则列表。我建议研究所有的规则来个性化你的编码风格。

*值得注意的是，tslint 已被弃用。所以即使我们有一个 TypeScript 项目，你也会想使用 eslint。*

首先，您可以手动 lint 代码。

```
npm run lint
yarn lint// Alternatives
npm run lint -- --fix=false
yarn lint --fix=false
```

这是为了修复代码，并对无法修复的代码给出警告或错误。

另一种方法是禁用修复，这在拉式请求批准步骤中很有用。

其次，当您提交代码时，lint 将自动运行。

```
"husky": {
  "hooks": {
    "pre-commit": "lint-staged",
    "post-commit": "git update-index --again"
  }
},
"lint-staged": {
  "*.ts": [
    "eslint --fix",
    "git add"
  ]
}
```

在提交`"pre-commit"`之前，如果没有错误，linter 将修复所有文件，然后运行`git add`。

然后提交发生，因为这是我们执行的命令。

然后`"post-commit"`运行。这件东西可能不需要，但我把它留着以防万一。似乎有一个问题可以阻止`git add`发生。点击阅读更多相关信息。

# 为生产构建项目

构建命令将删除`./dist`文件夹(tsconfig 的 outDir ),并使用`ttypescript`或`ttsc`运行构建。

```
npm run build
yarn build
```

生产构建使用生产 tsconfig 文件。这允许我们从产品构建中排除测试文件。

## tsconfig.prod.json

```
{
  "extends": "./tsconfig.json",
  "exclude": ["src/**/*.test.ts"]
}
```

1.  `"extends": "./tsconfig.json"`将从`tsconfig.json`文件导入配置。
2.  `"exclude": [...]`是要从构建中排除的文件夹/文件列表。

# 运行生产版本

构建生产环境后，我们可以使用以下命令运行生产服务器。

如果代码不是首先用 build 命令构建的，这将不起作用。

```
npm run start:prod
yarn start:prod
```

有一个单独的命令来运行开发服务器和生产服务器，因为这些服务器有不同的要求。通常，您将在开发中使用 nodemon，在生产中使用 node(最好使用 Docker + Kubernetes 之类的工具)。

# 这些脚本都是从哪里来的？

如果你没有听说过 npm，那么你能走到这一步是一个惊喜。

如果您不熟悉`package.json`文件，那么我建议您看看脚本部分。

注意，这一节列出了上面所有的命令和执行的底层脚本。

```
"scripts": {
  "start": "npm run start:dev", // or "yarn start:dev"
  "test": "mocha --config .mocharc.json --watch src/**/*.test.ts",
  "lint": "eslint src/**/*.ts --fix",
  "build": "rimraf dist && ttsc --build tsconfig.prod.json",
  "start:dev": "nodemon --config .nodemonrc.json src/index.ts",
  "start:prod": "node dist/index.js"
}
```

脚本部分就是为什么我们可以编写像`npm start`这样的命令来启动整个开发服务器。否则，我们将不得不写出执行的实际命令，`nodemon --config .nodemonrc.json src/index.ts`，这很麻烦。

另外，`package.json`命令可以运行其他`package.json`命令。

```
. npm start
|
v
. npm run start:dev
|
v
. nodemon --config .nodemonrc.json src/index.ts
```

# 结论

希望能够更容易地识别和修复脚本问题，并升级脚本以改善您的开发体验。有几个变化可以改善这个设置。

尽管我不需要 Babel 来完成这个设置，但我很好奇它是否会让设置变得更容易，或者是否会让 transpilation 更快。

第二，我不喜欢摩卡的设置，所以我想换出那个库；可能是开玩笑。

最后，如果您不知道配置难题的每一部分，那也没关系。请随意派生、使用、修改[node-typescript-mocha-eslint](https://github.com/christo8989/node-typescript-mocha-eslint)模板来改善您的开发体验。