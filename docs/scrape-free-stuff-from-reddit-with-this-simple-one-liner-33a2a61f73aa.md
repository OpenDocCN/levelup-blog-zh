# 用这个简单的一行程序从 Reddit 上刮下免费的东西

> 原文：<https://levelup.gitconnected.com/scrape-free-stuff-from-reddit-with-this-simple-one-liner-33a2a61f73aa>

![](img/6f9a75fbd64cdb0b4e523b9ea6ed2276.png)

唐·坎瓦设计

从网上搜集资料很有趣，尤其是当你得到有用的东西时。

说到有用，从网上抓取一些有价值的东西可能需要一些手工劳动。

手工操作既费时又麻烦。开发者喜欢相反的情况。自动化。

现在，让我们从 Reddit 上搜集有价值的数据，并获得推广免费东西的帖子。可能是免费课程，免费产品，或者其他什么。

在本教程中，您将使用终端从 Reddit 获取数据。我会解释如何抓取像 Udemy 这样的 subreddits 来获取 Reddit 用户发布的最新优惠券。我对它进行了定制，这样你就可以在任何其他子编辑上应用它。

# 最小示例

我们一步一步来，看看怎么刮 Reddit。

首先，您需要 API 凭证来获取这些信息吗？不，你只需要知道你想要的终点。然后使用命令行实用程序通过使用`curl`或`wget`从该端点获取请求。

```
**$** curl -sA 'udemy subreddit scraper' 'https://www.reddit.com/r/udemy/top.json?t=month'
```

这一行返回了上个月来自 *udemy* 子编辑的热门数据的 JSON。

您可以在这些选项中使用`curl`:

*   `-s`以静默模式下载
*   `-A`以字符串形式添加用户代理

# 获取子编辑帖子的标题

如果您想看到 JSON 格式的层次结构，请使用最后的`jq`程序管道:

```
**$** curl -sA 'udemy subreddit scraper' 'https://www.reddit.com/r/udemy/top.json?t=month' **|** **jq** '.'
```

让我们更具体地获取那些热门 Reddit 帖子的标题。

为此，获取*标题*键的值。它位于父*数据*的内部，父*数据*是*子*散列列表的一个叶。*孩子*实际上是父*数据*的孩子。

```
**$** curl -sA 'udemy subreddit scraper' 'https://www.reddit.com/r/udemy/top.json?t=month' **|** **jq** '.data.children[].data.title'
```

# 得到免费的东西

现在，我们用*免费*关键词过滤标题。

假设只有当 Reddit 发帖人在标题中明确写下关键字' *Free* '时，免费的东西才存在:

```
**$** curl -sA 'udemy subreddit scraper' 'https://www.reddit.com/r/udemy/top.json?t=month' **|** **jq** '.data.children[].data | select(.title|test("Free")).title'
"Top 30 Udemy Paid Courses For Free With Certificate 19 March 2022"
"Best 50 FreeCourses for 18 March 2022"
"Best 44 Udemy Paid Courses For Free With Certificate 12 March 2022"
"Free Courses for 05 March 2022"
"Hurry up, limited time only ! 40 Free Courses for 04 March 2022"
"Udemy Paid Courses For Free With Certificate 06 March 2022"
"3D modeling for Free"
```

在这里，您使用了`select`来过滤子*标题*的具体值。你用`test`函数来处理正则表达式。这个正则表达式包含了 *Free* 单词及其后面的任何字符。

但是这个命令有一个问题。我们没有任何结果的标题有*自由*字(大写)或该字的任何变体。例如， *free* 、 *FRee* 、 *FREe* 和 *FREE* 不会通过该命令返回。所以我们可以通过在`test`函数中添加不区分大小写的参数`"i"`来解决这个问题。

```
**$** curl -sA 'udemy subreddit scraper' 'https://www.reddit.com/r/udemy/top.json?t=month' **|** **jq** '.data.children[].data | select(.title|test("free"; "i")).title'"Top 30 Udemy Paid Courses For Free With Certificate 19 March 2022"
"Top 50 Premium Courses FREE for Wednesday, March 16, 2022"
"Best 50 FreeCourses for 18 March 2022"
"Best 44 Udemy Paid Courses For Free With Certificate 12 March 2022"
"Free Courses for 05 March 2022"
"Hurry up, limited time only ! 40 Free Courses for 04 March 2022"
"Udemy Paid Courses For Free With Certificate 06 March 2022"
"Browse Udemy courses that have gone free Today"
"3D modeling for Free"
```

现在，您有了更多包含其他变化的结果。即使有*免费的*关键词和任何其他变化，在我们的例子中，你可以看到它。

让我们对其进行定制，并将其放入 bash 脚本中:

```
SUBREDDIT="$1"
**curl** -sA 'subreddit reader' \
'https://www.reddit.com/r/'${SUBREDDIT}'/top.json?t=month' \
**|** **jq** '.data.children[].data | select(.title|test("free"; "i")).title'
```

您将 subreddit 名称作为一个参数。将 bash 脚本保存为例如 *top_free.sh* ，然后输入您想要从中获取免费内容的子编辑的名称:

```
**$** chmod u+x top_free.sh # give access to that shell script
**$** ./top_free.sh udemy   # replace udemy with whatever subreddit you want
```

# 更加定制的命令行

让我们甚至在 bash 脚本中创建更多的定制。添加查询参数作为您想要的变元变量，而不是硬编码“free”:

```
SUBREDDIT="$1"
QUERY="${2:-free}"
**curl** -sA 'subreddit reader' \
'https://www.reddit.com/r/'${SUBREDDIT}'/top.json?t=month' \
**|** **jq** '.data.children[].data | select(.title|test("'"${QUERY}"'"; "i")) | .title'
```

参数扩展`${2:-free}`意味着*查询*将是第二个参数。如果为空，查询将分配给单词 *free* 。

让我们将这个 bash 脚本保存为 *top_stuff.sh* 并查看带有关键字 *courses* 的标题:

```
**$** chmod u+x top_stuff.sh
**$** ./top_stuff.sh udemy courses"Winning at Udemy: What the most successful courses have in common"
"Top 30 Udemy Paid Courses For Free With Certificate 19 March 2022"
"Top 50 Premium Courses FREE for Wednesday, March 16, 2022"
"Best 50 FreeCourses for 18 March 2022"
"Best 44 Udemy Paid Courses For Free With Certificate 12 March 2022"
"Free Courses for 05 March 2022"
"https://www.reddit.com/r/Udemy/comments/t793wh/free_courses_for_05_march_2022/"
"Hurry up, limited time only ! 40 Free Courses for 04 March 2022"
"Best Courses for 10 March 2022"
"40 Courses for 09 March 2022 added Today Wednesday March 9, 2022 Don't miss out on these exciting offers"
"Best Courses for 07 March 2022"
"Udemy Paid Courses For Free With Certificate 06 March 2022"
"Browse Udemy courses that have gone free Today"
"Do all courses go on sale at some point, or can some Sellers request not to be included in sales?"
```

# 获取标题和 URL

现在，我们有了想要的标题。为什么我们不返回每个标题的 URL，这样我们就可以点击它并浏览 subreddit 帖子和评论了？

```
SUBREDDIT="$1"
QUERY="${2:-free}"
**curl** -sA 'subreddit reader' \
'https://www.reddit.com/r/'${SUBREDDIT}'/top.json?t=month' \
**|** **jq** '.data.children[].data | select(.title|test("'"${QUERY}"'"; "i")) | {title, url} | .[]'
```

这里，您已经使用`jq`文档中的对象构造添加了两个子节点: *title* 和 *url* 。

当您再次运行该命令时，您会得到:

```
"Winning at Udemy: What the most successful courses have in common"
"https://www.reddit.com/r/Udemy/comments/tgj25n/winning_at_udemy_what_the_most_successful_courses/"
"Top 30 Udemy Paid Courses For Free With Certificate 19 March 2022"
"https://www.reddit.com/r/Udemy/comments/thu0er/top_30_udemy_paid_courses_for_free_with/"
"Top 50 Premium Courses FREE for Wednesday, March 16, 2022"
"https://www.reddit.com/r/Udemy/comments/tfev3o/top_50_premium_courses_free_for_wednesday_march/"
"Best 50 FreeCourses for 18 March 2022"
"https://www.reddit.com/r/Udemy/comments/tgybkw/best_50_freecourses_for_18_march_2022/"
"Best 44 Udemy Paid Courses For Free With Certificate 12 March 2022"
"https://www.reddit.com/r/Udemy/comments/tcf5x6/best_44_udemy_paid_courses_for_free_with/"
"Free Courses for 05 March 2022"
"https://www.reddit.com/r/Udemy/comments/t793wh/free_courses_for_05_march_2022/"
"Hurry up, limited time only ! 40 Free Courses for 04 March 2022"
"https://www.reddit.com/r/Udemy/comments/t6fzna/hurry_up_limited_time_only_40_free_courses_for_04/"
"Best Courses for 10 March 2022"
"https://www.reddit.com/r/Udemy/comments/tavdbg/best_courses_for_10_march_2022/"
"40 Courses for 09 March 2022 added Today Wednesday March 9, 2022 Don't miss out on these exciting offers"
"https://www.reddit.com/r/Udemy/comments/ta4lct/40_courses_for_09_march_2022_added_today/"
"Best Courses for 07 March 2022"
"https://www.reddit.com/r/Udemy/comments/t8mesy/best_courses_for_07_march_2022/"
"Udemy Paid Courses For Free With Certificate 06 March 2022"
"https://www.reddit.com/r/Udemy/comments/t7xruf/udemy_paid_courses_for_free_with_certificate_06/"
"Browse Udemy courses that have gone free Today"
"https://www.reddit.com/r/Udemy/comments/te3f97/browse_udemy_courses_that_have_gone_free_today/"
"Do all courses go on sale at some point, or can some Sellers request not to be included in sales?"
"https://www.reddit.com/r/Udemy/comments/t4a0d6/do_all_courses_go_on_sale_at_some_point_or_can/"
```

# 最后的想法

在本教程中，您已经看到了如何从任何子编辑中获取每月免费的顶级内容。您已经获取了该帖子的标题和 URL，如果您愿意，您可以查看该帖子并加入社区。

我们还将其一般化，以包括您想要在子编辑中搜索的任何关键字。

如果您还有任何问题，请告诉我！或者想要更多的刮贴，就在下面评论吧！

尽情享受吧！

你可能会对我使用了`jq`的这个教程感兴趣