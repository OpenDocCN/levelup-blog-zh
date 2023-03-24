# 使用 Python 的动态 Twitter 消息头

> 原文：<https://levelup.gitconnected.com/dynamic-twitter-header-using-python-6a73c6b17aad>

![](img/911fc1a111e4b2d41e3542b59ae8b486.png)

Twitter 标题是科技 Twitter tweep 中的一种狂热，尤其是那些“用 CSS 制作”的标题。尽管我对 CSS 相当熟悉，但我缺乏用它制作一个好的 CSS 标题的创造力，而且几乎不可能制作出与那里的 CSS 天才们不相上下的东西。(查看 [@Prathkum](https://twitter.com/Prathkum) ，他是 CSS 奇才，也是我的头部项目背后的灵感之一)。

因为“用 CSS 制作”是不可能的，所以我决定走一条不同的路线。Python。Python 是我日常使用的东西，从自动化简单的任务到制作机器人供我娱乐。鉴于我对 Python 的热爱，它是我的 *Twitter 头部实验*的明显选择。

我做的第一个类似于我将在博客中解释的那个。它显示了我的 Twitter 统计数据(追随者，关注，推文数量，我的名单，我的 Twitter 年龄等)。尽管它很酷，而且我用了一个月左右，我后来还是把它换成了一个幽默的加密货币。

第二个就像我说的，是一个加密货币笑话，每分钟更新美元汇率。(我在 Reddit 上也有那个[一样的笑话)。我用了这个标题一段时间，但后来把它改成了静态图像。](https://www.reddit.com/user/dJones176/comments/npoq3f/a_boy_asked_his_bitcoininvesting_dad_for_1/)

然后，几天前我看到了一个很酷的“用 CSS 制作”的标题，并考虑制作另一个动态的标题。我也意识到我没有就此制作过教程。所以，我来了，一举两得。

# 这个想法

这个想法非常简单，制作一个动态的 Twitter 标题，显示宣布标题的 Tweet 的赞和转发。由于当用户与推文交互时，标题会发生变化，所以更多的用户会与之交互，只是为了看到标题更新。我本以为在我发表这篇博客后，推特上的赞/转发会增加，但当我写这篇文章时，它已经成为我最喜欢的推特之一。

虽然我最初没有计划，但在收到我的 Twitter 上为数不多的观众的反馈后，我也在标题上添加了最近的 3 个关注者。

# 演示

在开始真正的教程之前，你可以在我的推特(@HAliPunjabi) 和[相关推文](https://twitter.com/HAliPunjabi/status/1435219303465324548)上查看我的标题

# 先决条件——Twitter 开发者账户和应用

在编写实际代码之前，您需要一个 Twitter 开发人员帐户，然后创建一个 Twitter 应用程序。使用创建的应用程序的密钥和令牌，你将能够更新你的标题，并从 Twitter 获得关于你的追随者和喜欢/转发任何推文的数据。

1.  去 [Twitter 开发者账号](https://developer.twitter.com/en/apply-for-access)申请访问。应该不会花太长时间获得批准。
2.  转到概述并创建一个新应用程序。给应用起你想要的名字，复制并保存他们提供的 API 密匙和秘密。我从现在开始将它们称为`CONSUMER_KEY`和`CONSUMER_SECRET`。
3.  将应用权限更改为`Read and Write`(或者`Read and Write and Direct Messages`，如果你的脚本需要的话)。
4.  转到密钥和令牌部分，生成访问令牌和密码。把这些也复制下来，保存在某个地方。从现在起，我将把它们称为`ACCESS_TOKEN`和`ACCESS_TOKEN_SECRET`。

# 写剧本

为了使教程(和代码)简单，我不会使用任何预先制作的图像，而是使用 Python 从头开始生成所有内容。

# 设置

1.  创建一个存储代码的目录
2.  创建一个文件`.env`来存储我们的密钥和令牌。它的内容应该是这样的

```
ACCESS_TOKEN=<ACCESS_TOKEN>
ACCESS_TOKEN_SECRET=<ACCESS_TOKEN_SECRET>
CONSUMER_KEY=<CONSUMER_KEY>
CONSUMER_SECRET=<CONSUMER_KEY>
```

3.创建一个名为`fonts`的目录，从[谷歌字体](https://fonts.google.com/specimen/Source+Code+Pro)下载`SourceCodePro-Regular.ttf`到其中

# 密码

[](https://github.com/haideralipunjabi/twitter-header-script) [## GitHub-haideralipunjabi/twitter-Header-script:使用 Python 的动态 Twitter 头

### Python 脚本生成一个 Twitter 标题，其中包含一条推文的点赞/转发统计数据，以及 3 个最新的关注者示例…

github.com](https://github.com/haideralipunjabi/twitter-header-script) 

Github 上有完整的代码[，所以我只解释其中重要的部分。](https://github.com/haideralipunjabi/twitter-header-script)

*   稍后将在代码中使用的变量和常数

```
# Load environment variables from .env file
load_dotenv()# Location of this file
parent_directory = os.path.dirname(os.path.abspath(__file__))# Colors to be used in the header
COLORS = {
    "FOREGROUND": "#000000",
    "BACKGROUND": "#FFFFFF",
}# Fonts to be used in the header
FONTS = {
    "TITLE": ImageFont.truetype(os.path.join(parent_directory, "fonts/SourceCodePro-Regular.ttf"), 48),
    "SUBTITLE": ImageFont.truetype(os.path.join(parent_directory, "fonts/SourceCodePro-Regular.ttf"), 30),
    "NUMBERS": ImageFont.truetype(os.path.join(parent_directory, "fonts/SourceCodePro-Regular.ttf"),  144),
    "FOOTER": ImageFont.truetype(os.path.join(parent_directory, "fonts/SourceCodePro-Regular.ttf"),  24),
}# ID of the Tweet to be used
PINNED_TWEET_ID = "1435219303465324548"
```

*   Twitter 认证

```
# Authenticate to Twitter and return the API object
def get_twitter_api():
    auth = tweepy.OAuthHandler(
        os.getenv("CONSUMER_KEY"), os.getenv("CONSUMER_SECRET"))
    auth.set_access_token(
        os.getenv("ACCESS_TOKEN"), os.getenv("ACCESS_TOKEN_SECRET"))
    return tweepy.API(auth)
```

*   获取所需数据

```
# Fetch the status and followers data
def get_status_data(api, status_id):
    status = api.get_status(id=status_id)
    followers = api.followers(
        count=3, skip_status=True)    # 3 Latest Followers
    return {
        "likes": status.favorite_count,
        "retweets": status.retweet_count,
        "followers": [{
            "username": follower.screen_name,
            "photo": follower.profile_image_url_https
        } for follower in followers]        # The username and profile picture only
    }
```

*   绘制页眉。坐标大部分是硬编码在标题的 1500x500px 左右。只有追随者的图像和他们周围的矩形是基于用户名的宽度计算的。

```
# Draw the Header
def draw_header(data):
    # Create a new Image 1500 x 500 with background color
    img = Image.new('RGB', (1500, 500), color=COLORS['BACKGROUND'])
    d = ImageDraw.Draw(img)  # Draw variable for drawing on the image
    # The rectangle which contains the test "My pinned tweet has"
    d.rectangle((30, 30, 680, 110), None, COLORS["FOREGROUND"], 5)
    # Text - My Pinned Tweet has
    d.text((355, 70), "My pinned tweet has",
           fill=COLORS["FOREGROUND"], font=FONTS["TITLE"], anchor="mm") # Likes and Likes Count
    d.text((500, 370), "LIKES",
           fill=COLORS["FOREGROUND"], font=FONTS["TITLE"], anchor="mm")
    d.text((500, 250), str(data["likes"]),
           fill=COLORS["FOREGROUND"], font=FONTS["NUMBERS"], anchor="mm")
    # Retweets and Retweets Count
    d.text((1000, 370), "RETWEETS",
           fill=COLORS["FOREGROUND"], font=FONTS["TITLE"], anchor="mm")
    d.text((1000, 250), str(data["retweets"]),
           fill=COLORS["FOREGROUND"], font=FONTS["NUMBERS"], anchor="mm") # Text - Latest Followers
    d.text((1300, 50), "Latest Followers",
           fill=COLORS["FOREGROUND"], font=FONTS["SUBTITLE"], anchor="mm") # Keeping track of widest line of text
    max_width = d.textsize("Latest Followers", font=FONTS["SUBTITLE"])[0]# Drawing the followers text and image
    for idx, follower in enumerate(data["followers"]):
        username_width = d.textsize(
            follower["username"], font=FONTS["SUBTITLE"])[0]
        if username_width + 50 > max_width:
            # 50 = Width of image (30) + gap between image and text (20)
            max_width = username_width + 50
        d.text((1320, 90 + 40*idx), follower["username"],
               fill=COLORS["FOREGROUND"], font=FONTS["SUBTITLE"], COPYanchor="mm")
        # Download image
        profile_image = Image.open(io.BytesIO(
            requests.get(follower["photo"]).content))
        # Resize image
        profile_image = profile_image.resize((30, 30))
        # Paste Image
        img.paste(profile_image, (1280 - (username_width//2), 75 + 40*idx)) # Draw rectangle around followers
    d.rectangle((1300 - (max_width/2) - 20, 30, 1300 +
                (max_width/2) + 20, 210), None, COLORS["FOREGROUND"], 5) # Footer Text
    d.multiline_text((750, 465), "Like / Retweet my pinned tweet to see my header update\nCheck pinned thread for more details",
                     fill=COLORS["FOREGROUND"], font=FONTS["FOOTER"], align="center", anchor="ms")
    # Save the image
    img.save(os.path.join(parent_directory, "header.png"))
```

*   驱动程序代码

```
# Driver Code
if __name__ == "__main__":
    api = get_twitter_api()     # Get the Authenticated Api
    # Draw the header using data of PINNED_TWEET_ID
    draw_header(get_status_data(api, PINNED_TWEET_ID))
    api.update_profile_banner(os.path.join(
        parent_directory, 'header.png'))  # Upload the header
```

# 执行

您可以运行这个脚本来更新标题。我建议将此作为一个 cronjob，至少每两分钟一次，以获得最佳效果。我把它放在我的 Raspberry Pi 上，但是你可以试着把它放在 Heroku 上，或者其他服务上。

# 其他相关资源

*   [Twitter 开发者文档](https://developer.twitter.com/en/docs)
*   [创建实时 Twitter 横幅！(NodeJS) — Devesh B](https://blog.deveshb.me/create-a-real-time-twitter-banner)
*   [Twitter 上的 Prathkum 获得了很棒的 CSS 标题](https://twitter.com/Prathkum)

[在我的博客上阅读这个故事](https://blog.haideralipunjabi.com/posts/dynamic-twitter-header-using-python/)

[在推特上关注我](https://twitter.com/HAliPunjabi)

[给我买杯咖啡](https://www.buymeacoffee.com/HAliPunjabi)