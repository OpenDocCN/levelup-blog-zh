# 了解如何在 Heroku 上部署 Elixir 应用程序

> 原文：<https://levelup.gitconnected.com/learn-how-to-deploy-elixir-apps-on-heroku-f238d718b893>

![](img/0c5039ea331edef356144425e24da3d4.png)

对于我们决定向世界推出的每一款应用，到时候都有一个重要的决定要做:在哪里部署它最好？

简短的回答是:“视情况而定”。

然而，长的答案将取决于你的需求是什么，你的预算，你当前应用程序的设置，公司的限制等等。

特别是，我认为一个真正便捷的方法是使用平台即服务(PaaS)选项，如 [Heroku](https://www.heroku.com/) 。

Heroku 是一个平台，可以让你从部署和维护应用程序的所有复杂性中抽象出来，并将精力集中在你想要构建的应用程序上。这里有一个来自 Heroku 的 Greg Nokes 的[视频](https://www.youtube.com/watch?v=_N8Zf_nPZkQ&feature=emb_logo&ab_channel=Heroku)解释了他们平台背后的概念。

Heroku 有这个[构建包](https://devcenter.heroku.com/articles/buildpacks)的想法，它将你的代码转换成一个可以在他们的平台上运行的 slug，并抽象出所有的复杂性。

它们使用这些构建包支持小范围的流行编程语言，但是 Elixir 不在其中。

但是并不是所有的希望都落空了，我们有能力使用定制的构建包来支持它。阿卡什·马诺哈尔和他的贡献者为我们建造的[已经可用](https://github.com/HashNuke/heroku-buildpack-elixir)。

如果这一切对你来说都是新的，不要担心，我们将很快开始把这些碎片放在一起。

让我们使用 Elixir 构建一个非常小的应用程序，它查询一个开放的 API 并通过 HTTP 以 JSON 格式显示数据。

在您开始之前，您需要一个 Heroku 帐户。你可以在[heroku.com](https://signup.heroku.com/)免费创建一个新的，并且有一个免费层可以用于我们的开发项目。

如果你对长生不老药不太熟悉，你可以查看我的 [hello-world-elixir](https://rarias.dev/hello-world-elixir/) 文章给你一个概述。

本文将分为 5 个部分

1.  创建您的 Elixir 应用程序框架。
2.  编写基本功能。
3.  测试它在本地工作。
4.  创建您的 Heroku 应用程序。
5.  部署和测试您的应用程序。

# 1.创建您的 Elixir 应用程序框架

创建 Elixir 应用程序有多种方法。例如，您可以使用一个可用的框架，或者您可以自己一个接一个地创建文件。在这个练习中，我们将使用方便的 [mix new](https://hexdocs.pm/mix/Mix.Tasks.New.html) 助手，它将为我们创建大部分初始结构。

转到 shell 上的项目文件夹，然后键入`mix new simple_app --sup`

```
❯ mix new simple_app --sup
* creating README.md
* creating .formatter.exs
* creating .gitignore
* creating mix.exs
* creating lib
* creating lib/simple_app.ex
* creating lib/simple_app/application.ex
* creating test
* creating test/test_helper.exs
* creating test/simple_app_test.exsYour Mix project was created successfully.
You can use "mix" to compile it, test it, and more: cd simple_app
    mix testRun "mix help" for more commands.
```

这将为您的应用程序创建一个基本框架，包括一个名为`SimpleApp`的模块和一个基本的主管。一定要检查你的灵药版本。不同版本的语法和结构可能略有不同。我在下面的练习中使用 1.11.3。

让我们先编译我们的应用程序来测试 hello 方法。

```
❯ cd simple_app
❯ mix compile
Compiling 2 files (.ex)
Generated simple_app app
```

我们可以使用交互控制台来手动测试或者仅仅使用`mix test`来使用测试套件

```
❯ iex -S mix
Erlang/OTP 23 [erts-11.1.7]Interactive Elixir (1.11.3) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> SimpleApp.hello()
:world
```

通过键入`SimpleApp.hello()`，我们调用了助手创建的`hello`方法，如果一切设置正确，我们应该会得到一个符号`:world`作为回报。

# 2.编写基本功能

对于这个应用程序，我们将使用 Coindesk 的公共 [API 来查询今天比特币的价格。如果你更喜欢使用不同的 API，你可以在这个](https://www.coindesk.com/coindesk-api) [Github repo](https://github.com/public-apis/public-apis#cryptocurrency) 上找到一个公共 API 列表。我们得到的数据对这个练习来说并不重要。

但是，在继续之前，您可能希望检查您选择的 API 是否可操作，并检查返回数据的格式。我想在我的 shell 中使用`curl`,但是`wget`、Postman 或者任何其他 UI 客户端应该可以完成这个任务。

```
❯ curl https://api.coindesk.com/v1/bpi/currentprice.json
```

为了通过 Elixir 集成 API，让我们使用 HTTP 包装器 [Tesla](https://github.com/teamon/tesla) 。那里有很多好的选择，比如古老的 Httpoison。然而，特斯拉有一些额外的好处。我不会详细介绍，因为这不是本文的目的，但是值得一试。

要安装 Tesla，我们需要首先将依赖项添加到我们的`mix.exs`文件中

```
defp deps do
  [
	{:tesla, "~> 1.4.0"},
	{:hackney, "~> 1.16.0"},
	{:jason, ">= 1.0.0"}
	...
  ]
end
```

注意，我还包括了`hackney`和`jason`。我们将使用 Hackey 作为适配器来实际执行我们的 http 调用。Tesla 使用`httpc`作为默认适配器，但 Hackney 在许多方面要好得多。

我们也需要`jason`,因为它是我们的中间件所需的 JSON 解析器。它也比其他 JSON 解析器性能更好，所以无论如何都是好的。

回到您的 shell 并安装新添加的依赖项

```
❯ mix deps.get
```

你可能还没有在你的系统中安装`hex`，所以如果你被提示这样做，只要说是。

Tesla 设置的最后一部分是将适配器添加到我们的配置文件中。如果还没有创建文件，就放在`config/config.exs`下。

然后我们可以添加 Hackney 适配器，这样您的配置文件应该如下所示

```
use Mix.Configconfig :tesla, adapter: Tesla.Adapter.Hackney
```

就这样，我们完成了配置。加入一个`mix compile`来衡量，以确保我们仍然可以继续，我们没有犯任何错误。

# API 调用类

为了通过 Tesla 查询比特币基地 API，我们将创建一个模型。

我喜欢将业务逻辑放在模型文件夹中，因为它可以帮助我在大型项目中保持条理。继续创建`lib/models/coinbase.ex`

在它里面，我们需要 3 样东西，我们的模块定义，我们的 Tesla 中间件配置和我们想要抽象的方法。

```
defmodule Coinbase do
  @moduledoc """  
  A module abstraction used to interact with the coinbase http API  
  """
  use Tesla plug Tesla.Middleware.BaseUrl, "https://api.coindesk.com"
  plug Tesla.Middleware.Headers, [{"Content-Type", "application/json"}]
  plug Tesla.Middleware.JSON @doc """  
  Returns the Bitcoin Price Index from coinbase  
  """
  def bpi_current_price do
    get("/v1/bpi/currentprice.json")
  end
end
```

我们可以马上在我们的`iex`控制台中测试这一点

```
❯ iex -S mix
Erlang/OTP 23Interactive Elixir (1.11.3) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> Coinbase.bpi_current_price
{:ok,
 %Tesla.Env{
   __client__: %Tesla.Client{adapter: nil, fun: nil, post: [], pre: []},
   __module__: Coinbase,
   body: ...,
   headers: [
     ...
   ],
   method: :get,
   opts: [],
   query: [],
   status: 200,
   url: "https://api.coindesk.com/v1/bpi/currentprice.json"
 }}
```

# 显示数据

我们的基本模型完成后，我们需要的最后一件事是以易于阅读的格式显示数据。

我们将使用轻型 http 服务器 [Cowboy](https://github.com/ninenines/cowboy) 和适配器 [Plug](https://github.com/elixir-plug/plug) 来返回我们的数据。

探索不同的服务器选项和框架及其优缺点超出了本文的范围，因此我们将坚持使用最简单的解决方案。

让我们回到我们的`mix.exs`文件，将 Plug and Cowboy 添加到我们的依赖项中

```
defp deps do
  [
	{:tesla, "~> 1.4.0"},
	{:hackney, "~> 1.16.0"},
	{:jason, ">= 1.0.0"},
	{:plug_cowboy, "~> 2.0"} # NEW!
	...
  ]
end
```

获得这些 dep，编译项目，我们准备好继续前进

```
❯ mix deps.get
❯ mix compile
```

现在，不用太关心组织，让我们添加一个小的路由器文件，将我们的端点暴露给世界，并调用我们之前创建的 API。

继续创建`lib/router.ex`并定义一个基本的插头端点

```
defmodule SimpleAppRouter do
  use Plug.Router plug :match
  plug :dispatch get "/bpi" do
    send_resp(conn, 200, "BPI price coming soon...")
  end match _ do
    send_resp(conn, 404, "Wrong place mate")
  end
end
```

如果您不熟悉它，可以在他们的[文档](https://github.com/elixir-plug/plug)中查看 Plug 的内部工作方式。现在，我希望上面的代码是不言自明的。我们需要知道的是，我们正在使用`Plug.Router`功能，并公开一个端点`/bpi`，我们将使用它来检索我们的数据并显示它。

我们现在可以告诉我们的监督树，我们的路由器已经准备就绪。

转到`lib/simple_app/application.ex`，在那里您将找到由`mix new`助手创建的应用程序文件。这里，我们将要求我们的应用程序启动监督树下的路由器。

您生成的文件将如下所示

```
defmodule SimpleApp.Application do
  # See https://hexdocs.pm/elixir/Application.html
  # for more information on OTP Applications
  @moduledoc """
  Application that starts the http router for `SimpleApp`.
  """ use Application @impl true
  def start(_type, _args) do
    children = [
      {Plug.Cowboy, scheme: :http, plug: SimpleAppRouter, options: [port: 8080]}
    ] # See https://hexdocs.pm/elixir/Supervisor.html
    # for other strategies and supported options
    opts = [strategy: :one_for_one, name: SimpleApp.Supervisor]
    Supervisor.start_link(children, opts)
  end
end
```

您可以通过运行您的服务器来测试它是否工作正常

```
❯ mix run --no-halt
```

要显示比特币基地的数据，我们需要做的就是返回到我们的`SimpleAppRouter`并添加比特币基地调用。您的路由器功能将如下所示

```
get "/bpi" do
  send_resp(conn, 200, Coinbase.bpi_current_price)
end
```

如果您还记得`bpi_current_price`函数响应的格式，您可能已经注意到它不仅包括响应的主体，还包括我们不需要显示的标题、进程状态等。所以要解决这个问题，回到`lib/models/coinbase.ex`并修改函数，改为只返回响应体

```
def bpi_current_price do
    {:ok, response} = get("/v1/bpi/currentprice.json")
    response.body
 end
```

请记住，为了简单起见，我们不做任何类型的验证或错误考虑。

# 3.测试它在本地工作

现在，应用程序终于准备好了，我们可以在我们的本地环境中测试它的工作。

继续运行您的服务器

```
❯ mix run --no-halt
```

然后在您的浏览器或 curl 客户端中，您应该能够在`[http://localhost:8080/bpi](http://localhost:8080/bpi)`获得结果

```
❯ curl http://localhost:8080/bpi
HTTP/1.1 200 OK
...
server: Cowboy{ ..."chartName":"Bitcoin","bpi":{"USD":{"code":"USD"... }
```

# 4.创建 Heroku 应用程序

在我们完全进入 Heroku 应用程序创建之前，代码中还有最后一件事需要修改。

注意我们如何使用端口`8080`作为默认端口，但是一旦在 Heroku 中，我们的应用程序端口将是动态的，并由它们决定。因此，我们需要从环境变量中读取端口。

为此，使用从系统环境变量中获取的端口来更新应用程序文件中的端口 8080。

```
children = [
  {Plug.Cowboy, 
		scheme: :http, 
		plug: SimpleAppRouter, 
		options: [port: (System.get_env("PORT") || "8080") |> String.to_integer()]
  }
]
```

如果`PORT`环境变量不存在，这有一个额外的好处，即默认为 8080，这在本地使用时很方便。

为了创建我们的 Heroku 应用程序，我们将使用 [heroku-cli](https://devcenter.heroku.com/articles/heroku-cli) 。然而，如果你觉得使用 UI 更舒服，你可以去你的 [Heroku 仪表板](https://dashboard.heroku.com/apps)并且步骤非常简单。

你首先需要一个 Heroku 账户，我假设你已经有了。如果你没有，就用你喜欢的电子邮件和密码在他们的网站上注册。

> 提示:如果您碰巧有一个现有的 Heroku 帐户，并且不想在本练习中使用，您可以使用 [heroku-accounts](https://github.com/heroku/heroku-accounts) 插件轻松管理多个帐户。

要使用 cli 创建一个新的 Heroku 应用程序，只需在 shell 中进入应用程序的根目录并键入

```
❯ heroku create simple-app-elixir
Creating ⬢ simple-app-elixir... done
https://simple-app-elixir.herokuapp.com/ | https://git.heroku.com/simple-app-elixir.git
```

其中,`simple-app-elixir`是您希望为您的应用程序指定的唯一名称。

因为在撰写本文时 Heroku 没有任何针对 Elixir 的默认 buildpack，所以我们需要将我们的应用程序的 buildpack 设置为 HashNuke 的版本。

```
❯ heroku buildpacks:set \
  https://github.com/HashNuke/heroku-buildpack-elixir.git -a simple-app-elixir
```

为了让这个 buildpack 在生产中工作，我们需要在 Heroku 应用程序上指定一个默认配置。在项目的根目录下，创建一个名为`elixir_buildpack.config`的文件，并用您的 OTP 和 Elixir 版本填充它。

```
erlang_version=23.0
elixir_version=1.11
always_rebuild=false
runtime_path=/app
```

我们需要做的最后一件事是告诉 Heroku 在部署应用程序时运行什么进程。为此我们可以使用一个`Procfile`。在我们的 Procfile 中，我们将指定我们希望`mix`运行我们的应用程序，该应用程序将依次运行 Cowboy 服务器。

在项目的根目录下创建另一个名为`Procfile`的文件(没有任何文件扩展名)，并在其中添加以下内容

```
web: mix run --no-halt
```

请注意，这与我们在本地运行服务器时使用的命令相同。如果没有找到概要文件，buildpack 实际上将使用这个相同的命令作为缺省命令，但是我们自己定义它是一个很好的实践。

# 5.部署和测试您的应用

现在我们准备部署。Heroku 有一个基于`git`的部署系统，这真的很方便，因为我们需要做的就是`git push`我们的代码来部署我们的应用程序。

在您的 shell 中，在项目的根目录下，继续将您的代码提交给 Heroku，就好像它是另一个常规的 git 存储库一样。您可能需要使用自己的 git URL 添加 Heroku remote

```
❯ git remote add heroku [https://git.heroku.com/simple-app-elixir.git](https://git.heroku.com/simple-app-elixir.git)
```

您的是在创建应用程序时打印的，或者也可以在您的 Heroku 应用程序的设置中找到。

```
# If you haven't initialised the repository yet
❯ git init
Initialized empty Git repository# Commit your code
❯ git add .
❯ git commit -m "SimpleApp initial commit. Coinbase API"
❯ git push heroku master
```

如果您的设置正确，设置了 SSH 密钥，并且没有任何其他配置问题，这应该已经部署了您的应用程序。

一旦你的代码被部署，Heroku 会给你的应用分配一个子域，类似于

```
https://simple-app-elixir.herokuapp.com/
```

取决于你读这篇文章的时间，我的可能是活跃的，也可能不是。无论哪种方式，一旦你的部署，你应该能够检查出来。

您可以在浏览器中或者通过 curl 测试我们的`/bpi`端点。

```
❯ curl https://simple-app-elixir.herokuapp.com/bpi
```

这个端点应该返回一个带有比特币基地比特币汇率的 json 对象，这样您的部署就完成了。恭喜你。👏

# 结论

如您所见，在 Heroku 中部署 Elixir 应用程序相当简单。该平台允许按需扩展，能够很好地处理 Elixir 的性能需求。

Heroku 的另一个优势是，您可以在同一个伞形平台下运行多种语言的应用程序，这使您可以拥有复杂的微服务架构来满足不同的需求，而无需任何额外的平台知识。

我在这个 [Github 库](https://github.com/ronald05arias/simple-app-elixir)上放了一个完整的工作版本的代码，以防你想和它比较或者只是克隆它并测试它。

编码快乐！