# 如何使用 OpenSea API 收集 NFT 销售数据

> 原文：<https://levelup.gitconnected.com/how-to-collect-nft-sales-data-using-opensea-api-5a6b9b163f7>

本文展示了如何从 NFT 最大的市场 [OpenSea](https://opensea.io/) 收集 NFT(不可替代代币)销售数据。您将看到一个使用 [OpenSea API](https://docs.opensea.io/reference/api-overview) 获取历史销售数据的脚本。

有了收集的数据，您将能够执行您的分析，建立仪表板，发现新的趋势，等等。

![](img/47b5db4309ae2263574928b165f719bd.png)

nft 事务(称为事件)是资产发生状态变化，例如创建或转移。根据 opensea，事件由以下字段组成:

*   事件类型:描述事件类型
*   asset / asset_bundle:包含发生此事件的资产或资产包的简化版本的子字段
*   创建日期:记录事件的时间
*   from_account / to_account:与此事件关联的帐户
*   is_private:一个布尔值，如果销售事件是私人销售，则为真
*   payment_token:该交易中使用的支付资产，如 ETH、WETH 或 DAI
*   **数量**:售出物品的数量。适用于半可替代资产
*   **total_price** :购买资产的总价。这包括任何可能已经收取的版税

出于我们的目的，只需要获得具有“*成功*状态的事件，即资产出售。

***接下来，我们将一步步看如何从 OpenSea API 获取 nft 销售数据。***

## 1.获取 OpenSea API 密钥

要开始发出请求，你需要 OpenSea API 密匙，可以从这里获得:[https://docs.opensea.io/reference/request-an-api-key](https://docs.opensea.io/reference/request-an-api-key)。

## 2.请求功能

此函数向端点发出请求，以获取两个日期之间发生的事件列表:

端点将返回一个事件对象列表和两个游标，用于检索下一页和上一页的结果。

## 3.解析事件函数

该函数解析事件对象:

## 4.检索两个日期之间的所有事件

现在，我们可以开始使用已经定义的函数检索两个日期之间发生的事件:

每次迭代请求时，解析响应，如果" *next"* 光标包含值(即有其他事件要获取)，则通过传递光标再次发出请求，依此类推，直到接收到所有结果。

你可以找到一个现成的脚本，它具有这里显示的所有功能:[https://github.com/Checco9811/opensea-api-nft-sales](https://github.com/Checco9811/opensea-api-nft-sales)。

通过将开始日期和结束日期作为参数来运行脚本，命令执行示例:

```
> python script.py -s "2022-03-25" -e "2022-03-26"
```

或者:

```
> python script.py -s "2022-03-25 01:20" -e "2022-03-26 02:30"
```

帮助:

```
> python script.py -h
options:
  -h, --help            show this help message and exit
  -s STARTDATE, --startdate STARTDATE
                     The Start Date (YYYY-MM-DD or YYYY-MM-DD HH:mm)
  -e ENDDATE, --enddate ENDDATE
                       The End Date (YYYY-MM-DD or YYYY-MM-DD HH:mm)
  -p PAUSE, --pause PAUSE
                   Seconds to wait between http requests. Default: 1
  -o OUTFILE, --outfile OUTFILE
          Output file path for saving nft sales record in csv format
```

## 结论

我们已经看到了如何使用 OpenSea API 收集两个给定日期之间的事件，但是也可以只从传递正确参数的单个集合中收集数据，或者考虑所有可能的事件类型，而不仅仅是销售事件。你可以在 [API 文档](https://docs.opensea.io/reference/retrieving-asset-events)中找到所有细节。
我希望这篇文章对你建立自己的 NFT 时间序列数据集和进行分析有用。

感谢您的阅读！

【YouTube 视频样本

[GitHub 代码](https://github.com/Checco9811/opensea-api-nft-sales)

[LinkedIn](https://www.linkedin.com/in/francesco-falleni/)