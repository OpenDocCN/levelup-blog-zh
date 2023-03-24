# 在 Elixir 中正确解码嵌套的 JSON

> 原文：<https://levelup.gitconnected.com/the-hidden-secrets-of-json-decoding-in-elixir-ae08cbf817f7>

![](img/057d8b56b0d566bfefa2bfb20f35ecbe.png)

# 介绍

当我为 Holodex API 开发一个新的 [HTTP 客户端时，我选择了由以下部分组成的典型堆栈:](https://github.com/DaniruKun/ex-holodex)

*   [httposin](https://github.com/edgurgel/httpoison)，由 [Hackney](https://github.com/benoitc/hackney) 提供支持的流行药剂 HTTP 客户端
*   Jason ，快速 JSON 编码器/解码器库

虽然`Jason`仍然在[基准](https://gist.github.com/michalmuskala/4d64a5a7696ca84ac7c169a0206640d5)中占据较高的位置，但它缺少某些功能，我将在下面演示。

# 问题是

当您使用 HTTP JSON API 资源时，您会收到一个 JSON 字符串，然后使用您选择的 JSON 解码器对其进行解码:

```
Jason.decode!(~s({"name":"Dan","age":42,"nationality":"Latvian"}))
%{"name" => "Dan", "age" => 42, "nationality" => "Latvian"}
```

然而，你最终得到的是一张普通的仙丹图，它有很多缺点:

*   默认情况下，键是**二进制**，而不是**原子**
*   你不能使用`map.key_name`语法，这不符合习惯，也不够自信
*   很难推断出系统域中数据的形状

# 解决方法

# 解决方案 A:在 HTTP 客户机中预处理响应体

像`HTTPoison`这样的 HTTP 客户端库显示的一个常见模式是简单地[定义一组您期望接收的字段，然后迭代映射键并手动将它们转换成原子](https://github.com/edgurgel/httpoison#wrapping-httpoisonbase)。

```
defmodule Holobot.Holofans.Client do
  @moduledoc """
  Holofans API HTTP client implementation, based on HTTPoison.
  """
  use HTTPoison.Base

  @expected_fields ~w(count total channels videos query comments)
  @api_version "v1"

  @impl true
  def process_request_url(url) do
    Application.fetch_env!(:holobot, :holofans_api) <> "#{@api_version}" <> url
  end

  @impl true
  def process_response_body(body) do
    body
    |> Poison.decode!()
    |> Map.take(@expected_fields)
    |> Enum.map(fn {k, v} -> {String.to_atom(k), v} end)
  end
end
```

然而，这种方法存在许多问题:

*   我们现在有了一个关于我们将收到的数据的**形状**的**过于具体的假设**(如果我们期望一个对象数组作为根实体呢？)
*   只有顶层的**键**被转换成**原子**，但是任何种类的**嵌套对象**都将保留为带有二进制键的映射(现在你将根据深度拥有不同的访问器语法！)
*   [String.to_atom/1](https://hexdocs.pm/elixir/1.12/String.html#to_atom/1) 会使您面临溢出全局 atom 表[的危险，在一个正常运行时间非常长并且处理许多请求的系统中，这可能会成为一个大问题](https://www.erlang.org/erlang-enhancement-proposals/eep-0020.html)
*   客户端回调变得**臃肿**，它应该非常小，并且只对您的请求和响应应用最小的转换。
*   因为它不是一个结构，所以不能利用像 [TypedStruct](https://github.com/ejpcmac/typed_struct) 这样方便的库。

此外，如果您确实想尝试从反序列化的数据中创建结构，您可能最终会像这样实现一个`builder`:

```
@spec build_record(map) :: t()
  def build_record(video) do
    %__MODULE__{
      yt_video_key: video["yt_video_key"],
      title: video["title"],
      status: video["status"],
      live_schedule: video["live_schedule"],
      live_start: video["live_start"],
      live_end: video["live_end"],
      live_viewers: video["live_viewers"],
      channel: video["channel"]["yt_channel_id"],
      is_uploaded: video["is_uploaded"],
      duration_secs: video["duration_secs"],
      is_captioned: video["is_captioned"]
    }
  end
```

当然，这很少是可行的方法，因为它会产生大量的重复。可以通过使用像 [ExConstructor](https://github.com/appcues/exconstructor) 这样的库来解决这个问题，但是它仍然没有解决嵌套结构的问题。

# 解决方案 B:使用 Poison 的内置对象解码功能

一位同事分享了 ElixirConf 最近关于我不知道的`Poison`的一个特性的精彩演讲:将 JSON 字符串**解码为您选择的结构**:

```
Poison.decode!(~s({"name": "Dan", "age": 42}), as: %Person{})
#=> %Person{name: "Dan", age: 42}
```

太好了！HTTP 客户端是干净的，我们可以动态地指定数据的形状，并且结构定义模块也没有助手函数。

看来`Poison`还利用 [String.to_existing_atom/1 函数](https://github.com/devinus/poison/blob/e5c0867aaf3c9e9cb6da424580dcd8e1a25081d0/lib/poison/parser.ex#L174)解决了**动态生成引擎盖下原子**的问题。

**更新**:看起来确保这种情况发生的最可靠的方法是将选项`keys: :atoms!`也传递给`Poison.decode/2`，例如:

```
Poison.decode!(~s({"name": "Dan", "age": 42}), %{as: %Person{}, keys: atoms!})
```

剩下的唯一问题是**嵌套数据**。这是该特性几乎没有文档可言的地方，但它以如下方式工作:

```
defmodule Holodex.Api.Videos do
  alias Holodex.Api.Client
  alias Holodex.Model.{Channel, Comment, Video}

  @list_of_videos_p [
	%Video{
	  channel: %Channel{},
	  clips: [%Video{}],
	  sources: [%Video{}],
	  refers: [%Video{}],
	  simulcasts: [%Video{}],
	  mentions: [%Channel{}]
	}
  ]

  with url <- build_videos_url(opts),
		   body <- Client.get!(url).body do
		Poison.decode!(body, %{as: @list_of_videos_p})
	  end
end

# Returns:
  [
	%Holodex.Model.Video{
	  available_at: "2024-08-11T11:05:00.000Z",
	  channel: %Holodex.Model.Channel{
		banner: nil,
		clip_count: nil,
		description: nil,
		english_name: "Planya",
		id: "UCQaGj_l3dqmGWJLEbEmwgFQ",
		...
	  },
	  id: "Sc5MRAvMm18",
	  live_viewers: nil,
	  mentions: nil,
	  ...
	},
  ...
  ]
```

我们期望一个由`Video`对象组成的数组，其中可能还包含一个嵌套的`Channel`，以及嵌套在不同字段下的`Video`和`Channel`的数组。然后我们定义一个**模式**供解码器捕获(在本例中`@list_of_videos_p`作为模块属性以供重用并保持函数干净)。**模式**简单地定义了数据如何映射到您选择的**结构**。接下来剩下的就是为你的结构定义**类型规格**，现在你也可以充分利用类型规格:

```
@spec list_videos(opts()) ::
          {:ok, [Video.t()]} | {:error, HTTPoison.Error.t()} | {:error, Exception.t()}
  def list_videos(opts \\ %{}) do
    with url <- build_videos_url(opts),
         {:ok, response} <- Client.get(url),
         {:ok, decoded} <- Poison.decode(response.body, %{as: @list_of_videos_p}) do
      {:ok, decoded}
    end
  end
```

# 结论

当然，这个解决方案不是免费的午餐:`Poison`仍然比它的主要竞争对手`Jason`慢一点。但是，在大多数解码外部数据的情况下，`Poison`的这个主要特性比 CPU 时间或内存使用更有价值。

还有一个和`Poison.decode/2`一起使用关键字列表 args 的问题(透析器会抱怨)，我[在这里](https://github.com/devinus/poison/issues/199)提出过。我还在这里提出了关于`as`选项使用[的文档质量差的问题，我希望也能解决这个问题。](https://github.com/devinus/poison/issues/200)

如果您对如何做得更好有更多建议，请随时联系

*原载于*[*https://danpetrov . XYZ*](https://danpetrov.xyz/elixir/programming/2021/10/11/decoding-nested-json-the-right-way-in-elixir.html)*。*