# 自己写工具:python + slack webhook + crontab

> 原文：<https://levelup.gitconnected.com/write-your-own-tools-python-slack-webhook-crontab-b549dd7b85e>

![](img/344cc7be06d6ee90cf7e71f6aee5d7d0.png)

那些是机器人。机器人会替你做这项工作。

# 目标

在本文中，我将向您展示如何使用 slack webhook、python 脚本和 crontab 建立简单而强大的集成。我发现它非常有用，因为它允许我自动化不同的信息流。通常，您有一些数据源，访问这些数据源非常耗时，除此之外，通常还需要使用您的智能“动态”转换数据，最终您可能根本没有时间进行检查。

# 流动

![](img/9a743a4723c6314fd20f2052f8fcd336.png)

我们想在这篇文章中实现的流程。用 https://sequencediagram.org/完成[(如果你还不知道这个工具，你应该让自己熟悉)。](https://sequencediagram.org/)

这种集成的中心点是 python 脚本，您将在其中添加获取数据、转换数据然后使用 Slack webhook 发送数据的逻辑。这个流中的 crontab 是作业调度所需要的，手动运行脚本是次优的，因此您可以在脚本需要执行的给定时间设置一个 cron 作业，可以是每天一次，可以是每 10 分钟一次，也可以是每周日 12:00 一次，无论您怎么想。

# 非常真实的例子

为了让这个流程工作，你需要具备一些先决条件——配置一个 Slack webhook 是必要的。进入你的 slack 的应用程序管理，点击自定义集成，搜索传入的 Webhooks，然后按照步骤进行配置:

![](img/20a53bc365d2d849783de4a047d259cb.png)

在这里，您可以添加传入的 WebHooks 集成。

这个配置中最重要的工件是 webhook URL，它应该是这样的:
`[https://hooks.slack.com/services/T01ERXXXXXX/B020XXXXXX/rzrCkTvsXXXXXXXXXXXXXXXX](https://hooks.slack.com/services/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)`

把它保存在某个地方，我们将来会需要它。

现在，我们准备运行第一个示例，让我们创建一个简单的 python 脚本。

您可以使用 simple: `python3 slackwebhookcall.py`进行测试，结果应该是:

![](img/ae33d35c30944d072dd2979ca9adb0ee.png)

万岁！我们在 Slack 上的第一条机器人信息。

现在，要运行这个脚本，您只需要`requests`包，它可以通过 pip 安装:`pip install requests`其余的已经在您的标准 python 库中可用。请注意脚本的第一行——我们将在接下来的阶段需要它，因为我们将使该文件可执行，以便 crontab 可以运行它。

格式化 slack 消息有很多种可能性，下面的例子是最简单的一个，看看`payload`，`channel`显然告诉我们要把消息发送到哪里，`username`是将显示在消息上的用户名——你不需要在你的 Slack 工作区中定义这个用户，你可以在这里放置任何东西，`text`是消息的文本，`icon_emoji`是在消息附近显示为用户图片的表情符号，你可以添加一些有趣的东西，因为这一切都是为了开心。关于 Slack 消息格式的细节可以在 Slack 的官方文档中找到:[https://api.slack.com/messaging/composing](https://api.slack.com/messaging/composing)

让我们的例子变得有用，假设你投资了一些股票或加密货币，你可以在你的 slack 通道中查看，而不是在 web 界面中查看当前价格。

去雅虎财经:[https://finance.yahoo.com/quote/BTC-USD?p=BTC-USD](https://finance.yahoo.com/quote/BTC-USD?p=BTC-USD)在那里使用开发工具深入了解一下通话内容:

![](img/22006ce731459db226c794068275a7c2.png)

突出显示的请求是我们感兴趣的请求。

> 希望文章发表后雅虎不要屏蔽访问；)

复制上面的网址:[https://query1.finance.yahoo.com/v7/finance/quote?&symbols = BTC-美元&fields = extendedMarketChange，extendedMarketChangePercent，extendedMarketPrice，extendedMarketTime，regularMarketChange，regularMarketChangePercent，regularMarketPrice，regularMarketTime，circulatingSupply，ask，askSize，bidSize，dayHigh，dayLow，regularMarketDayHigh，regularMarketDayLow，regularMarketVolume，volume](https://query1.finance.yahoo.com/v7/finance/quote?&symbols=BTC-USD&fields=extendedMarketChange,extendedMarketChangePercent,extendedMarketPrice,extendedMarketTime,regularMarketChange,regularMarketChangePercent,regularMarketPrice,regularMarketTime,circulatingSupply,ask,askSize,bid,bidSize,dayHigh,dayLow,regularMarketDayHigh,regularMarketDayLow,regularMarketVolume,volume)

获取我们的 python 脚本中的数据(调用:`python3 btcall.py`):

现在，我们需要解析数据:

```
{
    "quoteResponse": {
        "result": [
            {
                "language": "en-US",
                "region": "US",
                "quoteType": "CRYPTOCURRENCY",
                "quoteSourceName": "CoinMarketCap",
                "triggerable": true,
                "exchange": "CCC",
                "exchangeTimezoneName": "Europe/London",
                "exchangeTimezoneShortName": "BST",
                "gmtOffSetMilliseconds": 3600000,
                "market": "ccc_market",
                "esgPopulated": false,
                "marketState": "REGULAR",
                "firstTradeDateMilliseconds": 1410908400000,
                "circulatingSupply": 18696150,
                "regularMarketChange": 352.47656,
                "regularMarketChangePercent": 0.6182226,
                "regularMarketTime": 1619887322,
                "regularMarketPrice": 57367.05,
                "regularMarketDayHigh": 58413.133,
                "regularMarketDayRange": "57052.273 - 58413.133",
                "regularMarketDayLow": 57052.273,
                "regularMarketVolume": 44779245568,
                "regularMarketPreviousClose": 57728.312,
                "fullExchangeName": "CCC",
                "fiftyTwoWeekLowChange": 314.77734,
                "fiftyTwoWeekLowChangePercent": 0.0055173496,
                "fiftyTwoWeekRange": "57052.273 - 58413.133",
                "fiftyTwoWeekHighChange": -1046.082,
                "fiftyTwoWeekHighChangePercent": -0.017908337,
                "fiftyTwoWeekLow": 57052.273,
                "fiftyTwoWeekHigh": 58413.133,
                "sourceInterval": 15,
                "exchangeDataDelayedBy": 0,
                "tradeable": false,
                "symbol": "BTC-USD"
            }
        ],
        "error": null
    }
}
```

正如你所看到的价格将在`data["quoteResponse"]["result"][0]["regularMarketPrice"]`下，你也可以在那里找到日期，以及许多其他有用的信息。

让我们一起包装它。

结果将会是:

![](img/681eb20405e11a74f98899350ddee21f.png)

耶！有用的 webhook 集成。

这里我们几乎完成了，我们需要的是与 crontab 的最终集成。

去你的终端，我们需要运行一些命令。首先，我们需要使脚本可执行:

`chmod +x webhookcall.py`

其次，我们需要更新 crontab:

`crontab -e`

您应该将下面一行放入 crontab 配置中:

```
*/10 * * * * /path/to/your/webhook/file.py
```

保存更改并观察奇迹是如何发生的。

如果你不熟悉 crontab 的设置，可以看看这个:【https://crontab.guru/every-10-minutes 

# 可能的扩展

你可以想象这里有很多用例及扩展，比如:

*   更改输出—例如，脚本可以向您发送电子邮件(或发送给其他人)。
*   改变数据来源，它不需要是股票价格，例如，我从时间跟踪工具收集数据，并使用相同的方法在松弛通道上呈现摘要。
*   越来越多你已经付费购买的工具拥有 API 访问权限，所以如果有些东西很难找到或者以一种你不满意的方式呈现，你可以写你自己的解决方案。
*   您可以尝试宽松的消息格式，例如，对于 BTC 价格，当价格上涨时，您可以将其显示为绿色，否则显示为红色。

感谢阅读。