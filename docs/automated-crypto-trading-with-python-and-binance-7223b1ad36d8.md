# 用 Python 和币安实现自动加密交易

> 原文：<https://levelup.gitconnected.com/automated-crypto-trading-with-python-and-binance-7223b1ad36d8>

## python-币安简介

**免责声明**:我不鼓励人们交易加密货币，也不对由此造成的损失承担任何责任。事实上，我对高风险投资抱有相当程度的怀疑，尤其是加密货币。当然，盈利是可能的，但你的资产也会完全亏损。

![](img/4efbf6249b854a4c60f4c1593704aba5.png)

[坎查纳拉](https://unsplash.com/@kanchanara?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/binance?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

然而，如果你仍然对这个话题感兴趣，特别是对 Python 和币安的自动化交易，这篇文章应该分享一些见解。

# 币安

这篇文章涵盖了币安的 Python API 的用法。[币安](https://www.binance.com/en)可以说是全球最大的加密货币交易所之一。它的一些优点是使用简单(包括自动化)，以及相对较低的交易费用(1%)。当然也有其他的，也提供编程访问的 API，你应该选择最适合你的——但是这篇文章与币安和 Python 有关。

如果你刚到币安，请随时使用我的[推荐链接](https://www.binance.com/en-GB/activity/referral-entry/CPA?fromActivityPage=true&ref=CPA_008DHX4MEN)注册，这样对每个人都有好处。

# API 设置

对于 API 的选择，也有几个选项——这里我们讨论[python-币安](https://python-binance.readthedocs.io/en/latest/)，这可能是最常用的。

要安装，请运行:

```
pip install python-binance
```

接下来，我们必须创建一个 API 密钥。为此，请登录币安，点击您的个人资料图标，然后选择“API 管理”。然后，选择“创建 API”并完成安全问题。

请注意显示的 **API 密钥**和**密钥** —当导航离开该站点时，该密钥将不再可见。为了能够在即将到来的应用程序中使用该键，选择“编辑权限”，然后切换“启用现货&保证金交易”(以及您想到的任何其他可能的用例)。不过需要注意的是:这将密钥设置为“live ”,拥有它的每个人都可以用你的账户执行任意交易。

管理键而不把它们粘贴到代码中(并把它们提交给 git……)的一种方法是使用环境变量。为此(在 Linux 下)，执行

```
echo ‘export binance_api={API Key}’ >> ~/.bashrcecho ‘export binance_secret={Secret Key}’ >> ~/.bashrc
```

并重新加载您的终端。

# API 用法

首先，我们初始化币安客户端:

```
api_key = os.environ.get(‘binance_api’) api_secret = os.environ.get(‘binance_secret’) client = Client(api_key, api_secret)
```

让我们从查询我们拥有的 BTC(比特币)数量开始:

```
client.get_asset_balance(asset=’BTC’)[‘free’]
```

自然，用任何其他标记替换“BTC”都会返回相应的值。

要询问令牌的当前价格(在 USDT)，请运行:

```
client.get_symbol_ticker(symbol=’BTCUSDT’)[‘price’]
```

现在让我们来看看购买/销售密码:

在这里，我们只使用[市价订单](https://www.schwab.com/learn/story/3-order-types-market-limit-and-stop-orders#:~:text=A%20market%20order%20is%20an,to%20execute%20the%20trade%20immediately.)(即在给定市价买入/卖出的订单)，并将高级订单类型留给读者。

要购买 0.01 BNB，请执行以下操作:

```
buy_order = self.client.order_market_buy(symbol=‘BNBUSDT’, quantity=0.01)
```

`buy_order`然后包含一个订单摘要。

相反，要卖出之前买入的 BNB，运行:

```
sell_order = self.client.order_market_sell(symbol=‘BNBUSDT’, quantity=sell_quantity)
```

请注意，除非你非常幸运，价格上涨很快，否则这两行代码不会在一个空账户上相继执行，因为币安收取 1%的交易费。

进一步值得强调的是，硬币不能以任意精度进行交易——这里的关键术语是*精度*和*批量*，例如可以通过`[client.get_exchange_info()](https://python-binance.readthedocs.io/en/latest/binance.html#binance.client.Client.get_exchange_info)`进行查询。

最后，让我们看看如何使用`get_historical_klines()`查询历史市场数据。此函数可用于不同的参数和不同的方式—以下返回 ETH 最近 7 天的市场信息，每天汇总一次:

```
klines = client.get_historical_klines(“ETHUSDT”, Client.KLINE_INTERVAL_1DAY, “7 days ago UTC”)
```

`klines`现在是一个长度为 7 的列表，代表过去 7 天的信息——每个这样的“信息”由另一个长度为 12 的[列表组成，其中例如前 4 个值是:*开盘时间*、*开盘价*、*最高价*和*最低价*。](https://www.programcreek.com/python/?CodeExample=get+klines)

希望你喜欢这次短途旅行——如果是的话，请尽快回来——如果有任何问题，请联系我。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   💰免费编码面试课程[查看课程](https://skilled.dev/?utm_source=luc&utm_medium=article)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级达人集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)