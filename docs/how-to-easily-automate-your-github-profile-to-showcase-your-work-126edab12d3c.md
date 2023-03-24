# 如何轻松自动化您的 Github 个人资料以展示您的作品

> 原文：<https://levelup.gitconnected.com/how-to-easily-automate-your-github-profile-to-showcase-your-work-126edab12d3c>

## 包括 Github 活动、媒体文章和 YouTube 频道

![](img/752751eb432182383907a4abf7bbd8b6.png)

我要坦白一件事。更新 Github 个人资料的时候我很懒。我喜欢创作内容(在这里和 Youtube 上都是)。我知道我应该从我的 GitHub 个人资料中链接到它。但是当涉及到实际的*做*连接任何地方的繁重工作时，我就是不愿意被打扰，尽管我知道这是有意义的。但是我订阅了“**自动化所有事情**”的口头禅。如果我能在这个过程中学到东西，这就是双赢。

Github 给了我们自动化流程的方法，使用一个叫做 Github Actions 的工具。让我们看看我们能做些什么。我们将:

[创建配置文件库](#77b0)
[设置 Markscribe 模板](#554b)
[获取媒体的 RSS 提要](#3de9)
[获取 YouTube 内容& stats(使用 Shields.io)](#5d85)
[配置 GitHub 动作](#eced)
∘ [创建个人访问令牌](#fdd5)
∘ [创建 GitHub 动作](#482c)
[高枕无忧](#b17e)

(如果你想一窥最终结果，你可以看到我的[自动更新个人资料](https://github.com/Kodaps)。)

# 创建配置文件存储库

如果您还没有这样做，第一步是在 GitHub 中建立一个特殊的存储库，作为您的配置文件存储库(即存储库中的 README.md 文件提供了配置文件页面的内容)。这真的很简单:对于个人帐户，创建一个与 GitHub 用户名同名的库。对于组织概要文件，**创建一个名为。github** 。

GitHub 现在会告诉你，你已经找到了一个特殊的资源库。耶\o/第一步是**将该库本地克隆到您的计算机上**。一旦完成，我们就准备继续前进。下面的所有工作都是在存储库中完成的。

# 设置 Markscribe 模板

我们将使用 Markscribe，一个使用 go 模板语言语法生成 markdown 文件的模板系统。如果你不知道如何使用 go 模板语法，不要担心，我也不知道。如果您看一下示例模板，就会明白发生了什么。事实上，第一步是创建一个`template/README.md.tpl`文件，并将示例模板的[内容复制到其中。该模板看起来像这样:](https://raw.githubusercontent.com/muesli/markscribe/master/templates/github-profile.tpl)

```
### Hi there 👋

#### 👷 Check out what I'm currently working on
{{range recentContributions 10}}
- [{{.Repo.Name}}]({{.Repo.URL}}) - {{.Repo.Description}} ({{humanize .OccurredAt}})
{{- end}}[... and other Github related content ...] 

#### 💬 Feedback

Say Hello, I don't bite!

#### 📫 How to reach me

- Twitter: https://twitter.com/...
- Fediverse: https://mastodon.social/@...
- Blog: https://...
```

请随意在顶部添加标题图像，并确保您配置了模板的“如何联系我”部分。

# 获取媒体的 RSS 源

Markscribe 还支持连接到 RSS 提要。好消息是你可以连接到 YouTube 和 here to Medium 来检索 RSS 提要。通用 RSS 部分的基本结构如下所示:

```
#### 📜 My recent blog posts
{{range rss "https://.../posts/index.xml" 5}}
- [{{.Title}}]({{.URL}}) ({{humanize .PublishedAt}})
{{- end}}
```

这里只需要用一个有效值替换 url ("https://…/posts/index.xml ")。

让我们从简单的案例开始。要获取您的 Medium 文章的 RSS 提要，只需在“https://medium.com/feed/@ <username>”中添加您的用户名。例如，我的订阅源是 https://medium.com/feed/@gosev 的</username>

# 获取 YouTube 内容和统计数据(使用 Shields.io)

获取 YouTube 内容很简单，至少在最初是这样。获取频道最新视频的 RSS 源可以通过以下方式设置:

```
https://www.youtube.com/feeds/videos.xml?channel_id=<channelId>
```

然而，让我们不要满足于如此基本的东西。毕竟，你也可以在你的 GitHub 个人资料中显示图片，这就为一些有趣的事情留下了空间。例如，如果您添加:

```
<img src="https://img.shields.io/youtube/channel/subscribers/<id>?style=for-the-badge"></img>
```

您最终会得到一个如下所示的徽章(在标题下):

![](img/7ca39980df4b43f52c8a02f9da323b77.png)

使用视频的 URL，我们也能够推导出缩略图的 URL。因为如果我们**从第 32 个字符开始切分 URL** ，我们将获得视频 Id，缩略图可以在以下位置找到:

```
https://img.youtube.com/vi/<videoId>/default.jpg
```

现在剩下的就是**使用 HTML 将结果格式化为一个表格**，缩略图在左边，标题在右边，并添加一个 shield.io 徽章，显示视频的浏览量。有一段代码是这样的:

```
## 📜 🇫🇷 My recent French videos
<img src="https://img.shields.io/youtube/channel/subscribers/UCzdX32OIhpfrdxQRhN2s98w?style=for-the-badge"></img>
<table>
{{range rss "https://www.youtube.com/feeds/videos.xml?channel_id=UCzdX32OIhpfrdxQRhN2s98w" 10}}
<tr>
  <td>
    <img src="https://img.youtube.com/vi/{{slice .URL 32}}/default.jpg"></img>
  </td>
  <td>
    <a href="{{.URL}}">{{.Title}}</a> ({{humanize .PublishedAt}})  
    <br/>
    <img src="https://img.shields.io/youtube/views/{{slice .URL 32}}?style=flat-square"> </img>
  </td>
</tr>
{{- end}}
</table>
```

这最终看起来像这样:

![](img/1516b375af4bffd3826d159599550a47.png)

现在我们已经设置好了…是时候自动更新配置文件了！

# 配置 GitHub 动作

## 创建个人访问令牌

首先要做的事情(如果你还没有的话)是在 GitHub 上设置一个**个人访问令牌**。你需要去`Developer settings` > `Personal access tokens` > `Generate new token`。您将需要以下范围:`repo:status`、`public_repo`、`read:user`、`read:org`。给它一个无限期的生命周期。它看起来会像这样:

```
ghp_zabwMCx576kdcvStiyG19D96J2jWnd4MDgQI
```

你可能想把它保存在安全的地方，因为 GitHub 不会再给你看了。然后转到您的档案库，然后转到`Settings` > `Secrets` > `Actions`并点击`New Repository Secret`。在这里，您将需要**创建一个名为`PERSONAL_GITHUB_TOKEN`的新秘密**，并粘贴您刚刚创建的令牌。

## 创建 GitHub 操作

现在我们只需要设置 GitHub 动作。为此，我们只需要创建一个名为`.github`的文件夹，并在该文件夹中创建另一个名为`workflows`的文件夹。

在里面。github/workflows 文件夹创建一个名为“readme-scribe.yaml”的文件。(参见此处的[和](https://raw.githubusercontent.com/Kodaps/.github/main/.github/workflows/readme-scribe.yml))该操作被配置为每小时运行一次，并且它有一个包含三个简单步骤的作业:

*   **结帐**仓库代码
*   **使用个人访问令牌呈现模板**
*   将代码推回存储库

注意:如果这个概要文件是为一个组织准备的(就像我的一样)，确保在渲染模板步骤中更新 README 输出文件的路径。

```
name: Update READMEon:
  push:      # run @ every push
  schedule:  # run @ every hour
    - cron: "0 */1 * * *"jobs:
  markscribe:
    runs-on: ubuntu-latest
    steps:
      - name: **Checkout**
        uses: actions/checkout@v2
        with:
          ref: main
      - name: **Render Template**
        uses: muesli/readme-scribe@master
        env:
          GITHUB_TOKEN: ${{ secrets.PERSONAL_GITHUB_TOKEN }}
        with:
          template: "templates/README.md.tpl"
          # Use "profile/README.md" instead on an org profile
          writeTo: "README.md"
      - name: **Push**
        run: | 
          git config user.name readme-scribe 🤖
          git config user.email actions@github.com
          git add .
          git commit -m "generated profile"
          git push origin main
```

现在提交文件并把它推送到远程存储库。一切都应该正常。您可以通过转到您的配置文件存储库中的 Actions 选项卡并查看注销的操作来进行检查。

# 坐下来放松(或不放松)

现在，你应该有一个自动更新的个人资料，显示你在 GitHub 中的工作，你在这里的内容，以及你可能制作的任何 YouTube 视频。是时候放松一下了…或者回到制作内容来充实个人资料！