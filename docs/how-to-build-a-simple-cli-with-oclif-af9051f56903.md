# 如何用 oclif 构建一个简单的 CLI

> 原文：<https://levelup.gitconnected.com/how-to-build-a-simple-cli-with-oclif-af9051f56903>

![](img/f3429cdb7d6932b206367ffcb0f84feb.png)

由 [SpaceX](https://unsplash.com/@spacex?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/space-computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

## 咖啡店里的编码

## 不喜欢 shell 脚本？用 Node.js 构建 CLI 工具！

Salesforce 开发人员对开源社区贡献良多。在他们的众多贡献中，有一个重要的，但可能不太为人所知的项目，名为 oclif。 [Open CLI 框架](https://oclif.io/)于 2018 年初发布，此后发展成为 Salesforce CLI 和 Heroku CLI 的基础。

在这篇文章中，我们将提供一个 oclif 的简要概述，然后我们将介绍如何用 oclif 构建一个简单的 CLI。

# oclif 简史

Oclif 最初是一个内部 Heroku 项目。Heroku 一直专注于开发人员的体验，它的 CLI 为通过 API 使用服务设定了标准。毕竟，Heroku 是`git push heroku` for deployment 的创造者，这是一个现在在整个行业广泛使用的标准。

如果你曾经运行过`heroku ps`或者`sfdx auth:list`，那么你就使用过 oclif。从一开始，oclif 就被设计成一个开放的、可扩展的、轻量级的框架，用于快速构建简单和复杂的 CLI。

发布四年多后，oclif 已经成为构建 CLI 的权威框架。一些最受欢迎的 oclif 组件每周下载量超过一百万次。oclif 项目仍在积极开发中。

通过 oclif 建立的知名公司或项目包括:

*   [Salesforce](https://developer.salesforce.com/tools/sfdxcli)
*   [Heroku](https://devcenter.heroku.com/articles/heroku-cli)
*   [Twilio](https://www.twilio.com/blog/building-conference-cli-in-react)
*   [土坯萤火虫](https://www.adobe.io/project-firefly/docs/guides/#cli)
*   [流](https://www.npmjs.com/package/getstream-cli)

## 为什么今天一个开发者会选择 oclif？

人们想要构建 CLI 的原因有很多。也许你的公司有一个 API，你想让客户更容易使用它。也许您使用内部 API，并且希望通过 CLI 运行命令来自动化日常任务。在这些场景中，您总是可以编写 Powershell 或 Bash 脚本，或者从头开始构建自己的 CLI，但是 oclif 是最好的选择。

Oclif 构建于 Node.js 之上，运行于所有主流操作系统上，有多种分发选项。除了速度快之外，oclif 还是自文档化的，并支持插件，允许开发人员构建和共享可重用的功能。随着 oclif 的迅速普及，越来越多的库、插件和有用的包变得可用。

例如，`cli-ux`预打包了`@oclif/core`包，并提供了常见的 UX 功能，如微调器、表格和进度条，您可以将它们添加到您的 CLI 中。

很容易理解为什么 oclif 是成功的，并且应该成为构建 CLI 的选择。

# 介绍我们的迷你项目

让我们为您将要构建的 CLI 设置场景。你想为你的爱好之一建立你自己的命令行界面:**太空旅行**。

你如此热爱太空旅行，以至于你观看了每一次 SpaceX 的发射直播，你查看 HowManyPeopleAreInSpaceRightNow.com 页面的次数比你愿意承认的还要多。您希望通过构建一个关于太空旅行细节的 CLI 来简化这种困扰，从一个简单的命令开始，该命令将显示当前在太空中的人数。最近，您发现了一个名为 [Open Notify](http://open-notify.org/) 的服务，它有一个用于此目的的 API 端点。

我们将使用`oclif generate` 命令来创建我们的项目，这将为一个新的 CLI 项目提供一些合理的默认值。使用该命令创建的项目默认使用 TypeScript 这是我们的项目将使用的——但是也可以配置为使用普通的 JavaScript。

# 创建项目

首先，如果您还没有 Node.js，那么您需要在本地安装它。oclif 项目需要使用 Node.js 的活动 [LTS 版本。](https://nodejs.org/en/about/releases/)

您可以通过以下命令验证已安装的 Node.js 的版本:

```
/ $ **node -v**
v16.15.0
```

接下来，全局安装 oclif CLI:

```
/ $ **npm install -g oclif**
```

现在，是时候使用 generate 命令创建 oclif 项目了:

```
/ $ **oclif generate space-cli**

     _-----_
    |       |    ╭──────────────────────────╮
    |--(o)--|    │  Time to build an oclif  │
   `---------´   │    CLI! Version: 3.0.1   │
    ( _´U`_ )    ╰──────────────────────────╯
    /___A___\   /
     |  ~  |
   __'.___.'__
 ´   `  |° ´ Y `

Cloning into '/space-cli'...
```

此时，您将会看到一些设置问题。对于这个项目，您可以将它们全部留空以使用默认值(由括号指示)，或者您可以选择自己填写它们。最后一个问题将要求您选择一个包管理器。对于我们的示例，选择 **npm** 。

## 从 oclif 的 hello world 命令开始

从这里开始，oclif 将为您完成 CLI 项目的创建。在`bin/`文件夹中，您将找到一些节点脚本，您可以在开发时运行这些脚本来测试您的 CLI。这些脚本将从`dist/`文件夹中的构建文件运行命令。如果您只是按原样运行脚本，您将会看到类似以下消息的内容:

```
/ $ **cd space-cli/**/space-cli $ **./bin/run**
oclif example Hello World CLI

VERSION
  space-cli/0.0.0 darwin-arm64 node-v16.15.0

USAGE
  $ space-cli [COMMAND]

TOPICS
  hello    Say hello to the world and others
  plugins  List installed plugins.

COMMANDS
  hello    Say hello
  help     Display help for space-cli.
  plugins  List installed plugins.
```

默认情况下，如果您没有为 CLI 指定要运行的命令，它将显示帮助消息。让我们再试一次:

```
/space-cli $ **./bin/run hello**
 >   Error: Missing 1 required arg:
 >   person  Person to say hello to
 >   See more help with --help
```

这一次，我们收到了一个错误。我们缺少一个必需的参数:我们需要指定我们在问候谁！

```
/space-cli $ **./bin/run hello John**
 >   Error: Missing required flag:
 >    -f, --from FROM  Whom is saying hello
 >   See more help with --help
```

我们收到了另一条有用的错误消息。我们还需要指定欢迎者，这次用一个标志:

```
/space-cli $ **./bin/run hello John --from Jane**
hello John from Jane! (./src/commands/hello/index.ts)
```

最后，我们已经正确地问候了约翰，我们可以看看 hello 命令的代码，它可以在`src/commands/hello/index.ts`中找到。看起来是这样的:

```
import {Command, Flags} from '@oclif/core'

export default class Hello extends Command {
  static description = 'Say hello'

  static examples = [
    `$ oex hello friend --from oclif
hello friend from oclif! (./src/commands/hello/index.ts)
`,
  ]

  static flags = {
    from: Flags.string({char: 'f', description: 'Whom is saying hello', required: true}),
  }

  static args = [{name: 'person', description: 'Person to say hello to', required: true}]

  async run(): Promise<void> {
    const {args, flags} = await this.parse(Hello)

    this.log(`hello ${args.person} from ${flags.from}! (./src/commands/hello/index.ts)`)
  }
}
```

正如您所看到的，oclif 命令被简单地定义为一个带有`async run()`方法的类，不出所料，它包含了命令运行时执行的代码。此外，一些静态属性提供了额外的功能，尽管它们都是可选的。

*   `description`和`examples`属性用于帮助消息。
*   `flags`属性是一个定义命令可用标志的对象，其中对象的关键字对应于标志名。稍后我们将对此进行深入探讨。
*   最后，`args`是一个对象数组，表示命令可以接受的一些选项的参数。

`run()`方法解析参数和标志，然后使用`person`参数打印出一条消息，使用`this.log()`打印出`from`标志(这是`console.log`的非阻塞替代方案)。注意，标志和参数都用`required: true`配置，这是得到验证和有用的错误消息所需要的，就像我们在早期测试中看到的那样。

## 创建我们自己的命令

既然我们已经理解了命令的结构，我们就可以编写自己的命令了。我们称之为`humans`，它会打印出目前在太空中的人数。您可以删除`src/commands`中的 hello 文件夹，因为我们不再需要它了。oclif CLI 也可以帮助我们搭建新的命令:

```
/space-cli $ **oclif generate command humans**

     _-----_
    |       |    ╭──────────────────────────╮
    |--(o)--|    │    Adding a command to   │
   `---------´   │ space-cli Version: 3.0.1 │
    ( _´U`_ )    ╰──────────────────────────╯
    /___A___\   /
     |  ~  |     
   __'.___.'__   
 ´   `  |° ´ Y ` 

   create src\commands\humans.ts
   create test\commands\humans.test.ts

No change to package.json was detected. No package manager install will be executed.
```

现在我们有了一个可以编辑的`humans.ts`文件，我们可以开始编写命令了。我们将使用的 Open Notify API 端点可以在以下 URL 找到:`[http://open-notify.org/Open-Notify-API/People-In-Space/](http://open-notify.org/Open-Notify-API/People-In-Space/)`

正如您在描述中看到的，端点返回了一个简单的 JSON 响应，其中包含了当前太空中人类的详细信息。用以下代码替换`src/commands/humans.ts`中的代码:

```
import {Command} from '@oclif/core'
import {get} from 'node:http'

export default class HumanCommand extends Command {
  static description = 'Get the number of humans currently in space.'

  static examples = [
    '$ space-cli humans\nNumber of humans currently in space: 7',
  ]

  public async run(): Promise<void> {
    get('http://api.open-notify.org/astros.json', res => {
      res.on('data', d => {
        const details = JSON.parse(d)
        this.log(`Number of humans currently in space: ${details.number}`)
      })
    }).on('error', e => {
      this.error(e)
    })
  }
}
```

下面是我们在上面代码中所做工作的分解:

1.  使用`http`包向 Open Notify 端点发送请求。
2.  解析 JSON 响应。
3.  用消息输出号码。
4.  捕捉并打印我们可能遇到的任何错误。

对于这个命令的第一次迭代，我们不需要任何标志或参数，所以我们没有为它们定义任何属性。

## 测试我们的基本命令

现在，我们可以测试我们的新命令了。首先，我们必须重新构建`dist/`文件，然后我们可以像之前的 hello world 示例一样运行我们的命令:

```
/spacecli $ **npm run build**

> space-cli@0.0.0 build
> shx rm -rf dist && tsc -b

/spacecli $ **./bin/run humans**
Number of humans currently in space: 7
```

很简单，不是吗？您现在有了一个简单的 CLI 项目，通过 oclif 框架构建，它可以立即告诉您太空中的人数。

## 用标志和更好的用户界面增强我们的命令

知道目前有多少人在太空是件好事，但是我们可以得到更多的太空数据！我们使用的端点提供了更多关于宇航员的细节，包括他们的名字和他们乘坐的飞船。

我们将进一步使用我们的命令，演示如何使用标志，并给我们的命令一个更好的 UI。我们可以用`cli-ux`包将我们的数据输出为一个表格，这个包已经被卷进了`@oclif/core`(从版本`1.2.0`开始)。为了确保我们可以访问`cli-ux`，让我们更新我们的包。

```
/spacecli $ **npm update**
```

我们可以在我们的`humans`命令中添加一个可选的`--table`标志，以在表格中打印出这些数据。对于这个漂亮的输出，我们使用了`CliUx.ux.table()`函数。

```
import {Command, Flags, CliUx} from '@oclif/core'
import {get} from 'node:http'

export default class HumansCommand extends Command {
  static description = 'Get the number of humans currently in space.'

  static examples = [
    '$ space-cli\nNumber of humans currently in space: 7',
  ]

  static flags = {
    table: Flags.boolean({char: 't', description: 'display who is in space and where with a table'}),
  }

  public async run(): Promise<void> {
    const {flags} = await this.parse(HumansCommand)

    get('http://api.open-notify.org/astros.json', res => {
      res.on('data', d => {
        const details = JSON.parse(d)
        this.log(`Number of humans currently in space: ${details.number}`)
        if (flags.table) {
          CliUx.ux.table(details.people, {name: {}, craft: {}})
        }
      })
    }).on('error', e => {
      this.error(e)
    })
  }
}
```

在我们更新的代码中，我们的第一步是带回`flags`属性。这次我们定义一个布尔标志——它要么存在，要么不存在——与字符串标志相反，字符串标志以字符串作为参数。我们还为传入的 options 对象中的标志定义了描述和简写-t。

接下来，我们解析我们的`run`方法中的标志。如果存在，我们显示一个带有`CliUx.ux.table()`的表格。第一个参数`details.people`是我们希望在表中显示的数据，而第二个参数是定义表中列的对象。在本例中，我们定义了一个`name`和一个`craft`列，每个列都有一个空对象。(表列有一些[配置选项](https://oclif.io/docs/table)，但是在这种情况下我们不需要任何选项。)Oclif 会在我们传入的数据对象上寻找那些属性，并为我们处理其他的事情！

我们可以构建并重新运行带有新表标志的命令，看看会是什么样子:

```
/spacecli $ ./bin/run humans --table
Number of humans currently in space: 10
 Name                   Craft    
 ───────────────── ──────── 
 Oleg Artemyev          ISS      
 Denis Matveev          ISS      
 Sergey Korsakov        ISS      
 Kjell Lindgren         ISS      
 Bob Hines              ISS      
 Samantha Cristoforetti ISS      
 Jessica Watkins        ISS      
 Cai Xuzhe              Tiangong 
 Chen Dong              Tiangong 
 Liu Yang               Tiangong
```

漂亮！

## 自己添加更多的功能

至此，我们的示例项目已经完成，但是您可以轻松地在它的基础上构建更多的项目。Open Notify 服务提供了一个 API 端点来获取国际空间站的当前位置。您也可以添加这个功能，使用一个命令，比如运行时返回位置的`space-cli iss`。

## 分配呢？

您可能正在考虑发布选项，以分享您令人惊叹的新 CLI。您可以通过一个简单的命令将这个项目发布到 npm。您可以创建一个 tarball，在内部将项目分发给您的团队或同事。如果你想与 macOS 用户分享，你也可以创建一个自制公式。Oclif 可以在这些选项中帮助你。

# 结论

本文开始时，我们回顾了 oclif 的历史，以及为什么它应该是您创建 CLI 时的首选。它的一些优势包括速度、可扩展性和各种分发选项。我们学习了如何搭建 CLI 项目并向其中添加新命令，并构建了一个简单的 CLI 作为示例。

现在你已经有了知识和新工具，出去冒险吧。