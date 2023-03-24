# 构建 React 组件库

> 原文：<https://levelup.gitconnected.com/building-a-react-component-library-e4eb7985ff5d>

![](img/5be75cecc9003f8ae1f7b9ed05eff3b4.png)

您是否想要一个内聚的框架来在您的组织内部构建美观且易访问的应用程序？使用 React 构建组件库可以大大降低非前端同事构建 web 和移动应用程序的门槛，这些应用程序不仅可以工作，而且看起来和其他应用程序一样好。

# 设置项目

仅仅几年前，构建组件库还是一件非常麻烦的事情。但是现在，您可以简单地使用预先构建的模板来立即开始。正如您使用类似 [create-react-app](https://create-react-app.dev) 的东西来创建成熟的 react 应用程序一样，您也可以使用 [create-react-library](https://www.npmjs.com/package/create-react-library) 来创建 React 组件库。

要开始，如果你想使用[纱](https://yarnpkg.com)作为你的包管理器，只需运行以下命令。

```
yarn create react-library your-project-name
```

如果您想使用 [NPM](https://www.npmjs.com) 作为您的软件包管理器，您可以通过运行以下命令在您的机器上全局安装软件包。

```
npm install -g create-react-library
```

并运行以下命令来创建实际的项目。

```
create-react-library your-project-name
```

如果您想使用 Typescript 而不是 Javascript 来构建 React 库，可以在上面的命令后面添加 template 选项。

```
yarn create react-library **--template=typescript** your-project-name// or if using NPMcreate-react-library **--template=typescript** your-project-name
```

# 建筑物

现在你应该有了一个基本的文件夹结构并开始运行了。您可以进入**示例**文件夹并运行 yarn start 来启动项目。这将启动示例项目，它基本上只是 React 库中的一个 React 应用程序，您可以使用它在本地测试您的包。

现在，您可以在项目中创建任何想要的文件夹结构。只要您从 *src/index.tsx* 中导出组件，就可以开始了。这将让您在从 npm 之类的地方安装之后，只导入您想要使用的特定组件。

```
*//* *src/index.tsx*import './styles.css'
export { useModal } from './lib/modal'
export { useNotification } from './lib/notification'
export { useMessage } from './lib/message'
export { default as Autocomplete } from './components/autocomplete'
export { default as Typography } from './components/typography'
export { default as Avatar } from './components/avatar'
export { default as Badge } from './components/badge'
export { default as Button } from './components/button'
export { default as Card } from './components/card'
export { default as Checkbox } from './components/checkbox'
export { default as Switch } from './components/switch'
export { default as Container } from './components/container'
export { default as Divider } from './components/divider'
export { default as Dropdown } from './components/dropdown'
export { default as Grid } from './components/grid'
export { default as Input } from './components/input'
export { default as List } from './components/list'
export { default as Message } from './components/message'
export { default as Modal } from './components/modal'
export { default as Notification } from './components/notification'
export { default as PopConfirm } from './components/popconfirm'
export { default as Popover } from './components/popover'
export { default as Progress } from './components/progress'
export { default as Select } from './components/select'
export { default as Spinner } from './components/spinner'
export { default as Stack } from './components/stack'
export { default as Table } from './components/table'
export { default as Tag } from './components/tag'
export { default as Toast } from './components/toast'
export { default as Tooltip } from './components/tooltip'
export { default as Rating } from './components/rating'
export { default as Drawer } from './components/drawer'
```

## 式样

谈到造型，有许多不同的选择可以考虑。您可以使用类似于[样式的组件](https://styled-components.com)来分别获得每个组件的即时范围样式，或者您可以简单地使用使用类和 id 的全局样式表。注意，如果您决定使用后者，您的用户必须确保包含来自编译的 dist 文件夹的样式表。例如，在发布您的项目后，您将会有一个类似如下的 dist 文件夹。

```
├── components
├── index.css
├── index.d.ts
├── index.js
├── index.js.map
├── index.modern.js
├── index.modern.js.map
└── lib
```

当使用项目时，用户必须导入样式文件，即使您直接在项目中导入样式表。

```
import '{YOUR_PROJECT_NAME}/dist/index.css'
```

# 出版

当您实现了一组组件并准备好将您的包发布到 NPM 时，我建议仔细检查您的 package.json 文件，确保 name 和 description 字段设置完全符合您的需要。此外，确保该版本是您实际想要开始的版本号。最初，它将被设置为 1.0.0，但您可能希望将其更改为 0.0.1。这些是您应该仔细检查的字段，如果这些字段不存在，请填写。

*   版本
*   名字
*   描述
*   许可证
*   关键词
*   作者
*   贮藏室ˌ仓库
*   主页

另外，请确保**主、模块、**和**文件**字段设置如下。如果设置不正确，您的软件包可能无法正常工作。

```
"main": "dist/index.js",
"module": "dist/index.modern.js",
"files": [
  "dist"
],
```

要最终发布项目，请确保名称是唯一的，并且没有其他 NPM 包具有类似的名称。然后运行以下命令将您的包发布到注册表。要阅读更多关于在 NPM 注册中心发布和管理软件包的信息，请查看 npm-publish 上的官方[文档。](https://docs.npmjs.com/cli/publish)

```
npm publish
```

如果发布成功，你现在应该能够访问 NPM 网站，并在那里看到你新发布的包。

https://www.npmjs.com/package/{你的项目名称}

# 维持

现在，您已经成功发布了您的第一个 React 组件库。这真的很棒，虽然不是过程的最后一步。管理 React 组件库不仅仅是发布它并让它闲置在那里。如果你输入了正确的关键字，你很快就会看到有人开始安装你的软件包。我看到在发布的第一周就有 2000 个安装的高峰。这可能会很伤脑筋，你可能会觉得有责任正确地维护这个包，并通过 GitHub 回答每一个进来的问题。

这里有几个花絮可以帮助你维护这个项目。

*   编写清晰而有用的提交文件
    这对于阅读 GitHub 提交日志的人来说肯定非常有帮助，可以了解项目的进展情况。只要这样做，你就可以大大降低收到的私人信息和问题的数量。
*   **快速移动并使用语义版本** 尽可能频繁地提交和发布。如果你提交了少量的代码，你可以很容易地回到过去，看看在错误和不确定性的情况下哪里出错了。还可以通过使用 [yarn](https://classic.yarnpkg.com/en/docs/cli/version/) 或 [npm](https://docs.npmjs.com/cli/version) 命令来尝试使用语义版本控制。
*   编写清晰良好的文档
    管理 React 组件库或任何种类的包的一个重要部分是记录功能。我强烈建议构建一个外部文档站点，像其他用户一样通过 npm 使用您的包，来记录每个组件的用法。

# 祝你好运！💎

如果您有任何疑问或问题，请考虑在下面的评论区留下您的问题和想法，我将确保给您回复！