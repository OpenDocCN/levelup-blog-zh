# 为 Tronix (TRX)收集市场数据的简单方法

> 原文：<https://levelup.gitconnected.com/an-easy-way-to-collect-market-data-for-tronix-trx-41e6e7c52555>

## 就用 R 包 tronr

![](img/82fef75c8a2a6661068477693bab602b.png)

由[马克西姆·霍普曼](https://unsplash.com/@nampoh?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

ronix (TRX，又名特隆)是特隆区块链的本币。TRX 令牌基于 ERC-20 以太网标准并与之完全兼容。虽然 TRX 最初的目的是实现数字娱乐支付，但如今它已经获得了许多其他用例，推动了创区块链上的交易，并建立了它的经济(主要是在游戏和[去中心化金融](https://en.wikipedia.org/wiki/Decentralized_finance)部门)。

人们可以在众多交易所(如币安、火币、Bittrex 等)购买 TRX 或将其兑换成其他加密货币。).2021 年 2 月，TRX 在加密货币中的全球排名约为 25 位，总市值超过 40 亿美元。

鉴于创区块链的日益普及和经济价值，对于市场分析师、加密货币交易员和数据科学家来说，通过编程轻松收集 TRX 市场数据变得非常重要。一种方法是使用我的[最近发布的用于 r 的](/introducing-tronr-an-r-package-to-explore-the-tron-blockchain-f0413f38b753)包`[tronr](https://github.com/next-game-solutions/tronr)`，这篇文章是一个关于如何使用该包中各个函数的综合教程。

[](/introducing-tronr-an-r-package-to-explore-the-tron-blockchain-f0413f38b753) [## 介绍 tronr，一个探索 TRON 区块链的 R 包

### 查询账户余额、交易、代币转账等等。

levelup.gitconnected.com](/introducing-tronr-an-r-package-to-explore-the-tron-blockchain-f0413f38b753) 

# `tronr`提供的 TRX 市场数据

这个包提供了几个函数，人们可以用它们来查询由公众 [CoinGecko API](https://www.coingecko.com/api/documentations/v3) 提供的 TRX 市场数据。在高层次上，可以获得以下数据:

*   一组指标，描述了 TRX 在加密货币市场上相对于用户指定的基础货币(如美元、欧元、BTC 等)的当前状态。；可以用`[get_supported_coingecko_currencies()](https://next-game-solutions.github.io/tronr/reference/get_supported_coingecko_currencies.html)`函数获取 CoinGecko 服务目前支持的基础货币列表)；
*   过去任意日期或历史日期范围内的 TRX 市场指标；
*   一系列最近历史日期(例如，过去 7 天)的 TRX 市场指标。

所有这些函数都返回直接适用于任何使用 r 的下游分析的变量。

# 查询当前市场状况

使用`[get_current_trx_price()](https://next-game-solutions.github.io/tronr/reference/get_current_trx_price.html)`功能可以查询 TRX 对一组基础货币的当前价格。基础货币列表由`vs_currencies`参数指定，例如:

```
library(tronr)
#> R toolbox to explore the TRON blockchain
#> Developed by Next Game Solutions ([http://nextgamesolutions.com](http://nextgamesolutions.com))
library(dplyr)get_current_trx_price(vs_currencies = c("usd", "eur", "gbp", "btc"))#> # A tibble: 4 x 3
#>   trx_price vs_currency last_updated_at    
#>       <dbl> <chr>       <dttm>             
#> 1 0.0473     usd         2021-02-27 22:49:53
#> 2 0.0392     eur         2021-02-27 22:49:53
#> 3 0.0340     gbp         2021-02-27 22:49:53
#> 4 0.00000101 btc         2021-02-27 22:49:53
```

`last_updated_at`列显示价格值最后一次更新的时间(在 UTC 时区，在这里和所有其他由`tronr`函数返回的结果中)。

`[get_current_trx_price()](https://next-game-solutions.github.io/tronr/reference/get_current_trx_price.html)`函数还可以用于检索当前市值、过去 24 小时的交易量以及与 24 小时前相比的价格百分比变化的数据。这可以通过分别打开`include_market_cap`、`include_24h_vol`和`include_24h_change`选项来完成:

```
pr <- get_current_trx_price(vs_currencies = c("usd", "eur", "gbp"),
                            include_market_cap = TRUE,
                            include_24h_vol = TRUE,
                            include_24h_change = TRUE)glimpse(pr)#> Rows: 3
#> Columns: 6
#> $ trx_price                <dbl> 0.04720316, 0.03910102, 0....
#> $ vs_currency              <chr> "usd", "eur", "gbp"
#> $ market_cap               <dbl> 3389781118, 2807945528, 24...
#> $ vol_24h                  <dbl> 1542178017, 1277472414, 11...
#> $ price_percent_change_24h <dbl> 5.397492, 5.397238, 5.397492
#> $ last_updated_at          <dttm> 2021-02-27 22:57:50, 2021...
```

使用`[get_current_trx_market_data()](https://next-game-solutions.github.io/tronr/reference/get_current_trx_market_data.html)`函数可以获得一组更全面的指标，这些指标描述了 TRX 在加密货币市场上的现状。

```
cur_market <- get_current_trx_market_data(
    vs_currencies = c("usd", "eur")
)glimpse(cur_market)#> Rows: 2
#> Columns: 34
#> $ last_updated_at                  <dttm> 2021-02-27 23:01:...
#> $ total_supply                     <dbl> 100850743812, 1008...
#> $ circulating_supply               <dbl> 71660220128, 71660...
#> $ vs_currency                      <chr> "usd", "eur"
#> $ market_cap                       <dbl> 3360737036, 278388...
#> $ market_cap_rank                  <int> 25, 25
#> $ market_cap_change_24h            <dbl> 173537637, 143744568
#> $ market_cap_percentage_change_24h <dbl> 5.44483, 5.44458
#> $ total_trading_vol_24h            <dbl> 1531231415, 126840...
#> $ current_price                    <dbl> 0.04689822, 0.0388...
#> $ price_high_24h                   <dbl> 0.04843521, 0.0401...
#> $ price_low_24h                    <dbl> 0.04447655, 0.0368...
#> $ price_change_24h                 <dbl> 0.00242167, 0.0020...
#> $ price_percentage_change_24h      <dbl> 5.44483, 5.44458
#> $ price_percentage_change_7d       <dbl> -23.56806, -23.27816
#> $ price_percentage_change_14d      <dbl> -15.25585, -14.91854
#> $ price_percentage_change_30d      <dbl> 65.48261, 65.91304
#> $ price_percentage_change_60d      <dbl> 58.56575, 60.50827
#> $ price_percentage_change_200d     <dbl> 118.2539, 112.3130
#> $ price_percentage_change_1y       <dbl> 183.3925, 155.5123
#> $ ath                              <dbl> 0.231673, 0.192595
#> $ ath_change_percentage            <dbl> -79.62507, -79.69782
#> $ ath_date                         <dttm> 2018-01-05, 2018-...
#> $ atl                              <dbl> 0.00180434, 0.0015...
#> $ atl_change_percentage            <dbl> 2516.088, 2427.333
#> $ atl_date                         <dttm> 2017-11-12, 2017-...
#> $ coingecko_rank                   <int> 14, 14
#> $ coingecko_score                  <dbl> 62.811, 62.811
#> $ developer_score                  <dbl> 79.48, 79.48
#> $ community_score                  <dbl> 52.285, 52.285
#> $ liquidity_score                  <dbl> 77.151, 77.151
#> $ public_interest_score            <dbl> 0.065, 0.065
#> $ sentiment_votes_up_percentage    <dbl> 77.81, 77.81
#> $ sentiment_votes_down_percentage  <dbl> 22.19, 22.19
```

上述结果中的大多数变量应该是不言自明的。更多详情可在 CoinGecko 网站的“[方法学](https://www.coingecko.com/en/methodology)”页面上找到。

# 查询特定的历史日期

特定历史日期的 TRX 市场数据可以通过`[get_trx_market_data_for_date()](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_date.html)`功能获得。该函数的`date`参数接受`%Y-%m-%d`格式的字符、日期或 POSIXct 值，例如:

```
# `date` as a character value:
df1 <- get_trx_market_data_for_date(date = "2021-01-01")# `date` as a Date value:
df2 <- get_trx_market_data_for_date(date = as.Date("2021-01-01"))identical(df1, df2)
#> [1] TRUEdf1
#> # A tibble: 2 x 5
#>   date       vs_currency  market_cap total_trading_vol  price
#>   <date>     <chr>             <dbl>             <dbl>  <dbl>
#> 1 2021-01-01 usd         1918734259\.       1231160613\. 0.0268
#> 2 2021-01-01 eur         1570737264\.       1007867475\. 0.0220
```

CoinGecko 提供的 TRX 市场数据的历史始于`2017-11-09 00:00:00`。尝试检索更早日期的数据将会失败，并显示相应的控制台消息:

```
get_trx_market_data_for_date(date = "2017-11-08")
#> Error: No data are available for dates before 2017-11-09\. 
#> Check the `date` argument
```

试图要求一个尚不存在历史的未来`date`也将失败:

```
get_trx_market_data_for_date(date = "2090-01-01")
#> Error: Cannot retrieve data for a future `date`. 
#> Check the `date` argument
```

# 查询一系列历史日期

用户还可以检索一系列历史日期的 TRX 市场数据。这可以使用`[get_trx_market_data_for_time_range()](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_time_range.html)`功能来完成。与`[get_trx_market_data_for_date()](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_date.html)`相反，这个函数在每个查询中只接受一种基础货币(参数`vs_currency`)。此外，定义历史范围的开始(`min_timestamp`)和结束(`max_timestamp`)的时间戳是 Unix 时间格式的字符值。

`[get_trx_market_data_for_time_range()](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_time_range.html)`返回数据的粒度取决于请求的范围。将检索长达 90 天的每小时数据，以及 90 天以上的每日数据:

```
# ------ Range of <1 day ------ (min_timestamp <- [as.POSIXct](https://rdrr.io/r/base/as.POSIXlt.html)("2020-01-01 10:00:10") %>%
[to_unix_timestamp](https://next-game-solutions.github.io/tronr/reference/to_unix_timestamp.html)())#> [1] "1577872810000"(max_timestamp = [as.POSIXct](https://rdrr.io/r/base/as.POSIXlt.html)("2020-01-01 20:45:10") %>% [to_unix_timestamp](https://next-game-solutions.github.io/tronr/reference/to_unix_timestamp.html)())#> [1] "1577911510000"

[get_trx_market_data_for_time_range](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_time_range.html)(
  vs_currency = "usd",
  min_timestamp = min_timestamp,
  max_timestamp = max_timestamp
) %>% glimpse()#> Rows: 11
#> Columns: 5
#> $ timestamp         <dttm> 2020-01-01 10:09:54, 2020-01-01 ...
#> $ vs_currency       <chr> "usd", "usd", "usd", "usd", "usd"...
#> $ price             <dbl> 0.01321627, 0.01329594, 0.0131974...
#> $ total_trading_vol <dbl> 1102088570, 1099017083, 108718413...
#> $ market_cap        <dbl> 874524530, 879244117, 872880577, ... # ------ Range of >90 days ------(min_timestamp <- [as.POSIXct](https://rdrr.io/r/base/as.POSIXlt.html)("2020-01-01 00:00:00") %>% [to_unix_timestamp](https://next-game-solutions.github.io/tronr/reference/to_unix_timestamp.html)())#> [1] "1577836800000"(max_timestamp = [as.POSIXct](https://rdrr.io/r/base/as.POSIXlt.html)("2020-05-01 00:00:00") %>% [to_unix_timestamp](https://next-game-solutions.github.io/tronr/reference/to_unix_timestamp.html)())#> [1] "1588291200000"

[get_trx_market_data_for_time_range](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_time_range.html)(
  vs_currency = "usd",
  min_timestamp = min_timestamp,
  max_timestamp = max_timestamp
) %>% glimpse()#> Rows: 121
#> Columns: 5
#> $ timestamp         <dttm> 2020-01-01, 2020-01-02, 2020-01-...
#> $ vs_currency       <chr> "usd", "usd", "usd", "usd", "usd"...
#> $ price             <dbl> 0.01329452, 0.01319943, 0.0128447...
#> $ total_trading_vol <dbl> 1134528759, 1032624901, 105654945...
#> $ market_cap        <dbl> 877119319, 872350811, 848482045, ...
```

与`[get_trx_market_data_for_date()](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_date.html)`类似，如果`min_timestamp`和/或`max_timestamp`定义了一个不存在数据的时间范围，则`[get_trx_market_data_for_time_range()](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_time_range.html)`将失败:

```
# Requesting non-existing data from the past:min_timestamp <- as.POSIXct("2017-11-08 00:00:00") %>% to_unix_timestamp()
max_timestamp = as.POSIXct("2020-01-01 00:00:00") %>% to_unix_timestamp()get_trx_market_data_for_time_range(
  vs_currency = "usd",
  min_timestamp = min_timestamp,
  max_timestamp = max_timestamp
)#> Error: No data are available for dates before 2017-11-09\. 
#> Check the `min_timestamp` argument # Requesting non-existing data from the future:min_timestamp <- as.POSIXct("2021-01-01 00:00:00") %>% to_unix_timestamp()
max_timestamp = as.POSIXct("2022-01-01 00:00:00") %>% to_unix_timestamp()get_trx_market_data_for_time_range(
  vs_currency = "usd",
  min_timestamp = min_timestamp,
  max_timestamp = max_timestamp
)#> Error: Cannot retrieve data for future dates. 
#> Check the `max_timestamp` argument
```

# 查询最近 *n* 天

作为查询历史日期范围的一种变体，可以使用`[get_trx_market_data_for_last_n_days()](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_last_n_days.html)`函数检索最近几个日期(从当前日期开始，包括当前日期)的 TRX 市场数据:

```
get_trx_market_data_for_last_n_days(
     vs_currency = "usd", days = 7
) %>% glimpse()#> Rows: 169
#> Columns: 5
#> $ timestamp         <dttm> 2021-02-21 00:11:55, 2021-02-21 ...
#> $ vs_currency       <chr> "usd", "usd", "usd", "usd", "usd"...
#> $ price             <dbl> 0.05765095, 0.05823241, 0.0583598...
#> $ total_trading_vol <dbl> 3059125266, 3057387492, 304195819...
#> $ market_cap        <dbl> 4175200820, 4176192440, 416885311...
```

除了接受数值之外，`days`参数还可以接受一个字符值`"max"`，这将导致检索 TRX 市场数据的全部现有历史:

```
get_trx_market_data_for_last_n_days(
    vs_currency = "usd", days = "max"
) %>% glimpse()#> Rows: 1,207
#> Columns: 5
#> $ timestamp         <dttm> 2017-11-09, 2017-11-10, 2017-11-...
#> $ vs_currency       <chr> "usd", "usd", "usd", "usd", "usd"...
#> $ price             <dbl> 0.002386822, 0.002044441, 0.00191...
#> $ total_trading_vol <dbl> 1224287.2, 990422.8, 707643.0, 81...
#> $ market_cap        <dbl> 156404162, 133968506, 125470649, ...
```

注意在最后两个例子中数据粒度是如何变化的。通常，如果选择`days = 1`，数据将大约每 3-8 分钟显示一次。如果`days`在 2 和 90(含)之间，将使用每小时的时间步长。日数据用于 90 以上的`days`。可以使用`interval`参数来控制这个粒度(默认情况下是`interval = NULL`)。目前，它唯一接受的值是`"daily"`:

```
# ------ Within-day data, with `interval = "daily"` ------[get_trx_market_data_for_last_n_days](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_last_n_days.html)(
  vs_currency = "usd",
  days = 1,
  interval = "daily"
) %>% glimpse()#> Rows: 2
#> Columns: 5
#> $ timestamp         <dttm> 2021-02-27 00:00:00, 2021-02-27 ...
#> $ vs_currency       <chr> "usd", "usd"
#> $ price             <dbl> 0.04534392, 0.04601954
#> $ total_trading_vol <dbl> 2032986783, 1516129456
#> $ market_cap        <dbl> 3253119066, 3297770342 # ------ Less than 90 days, with `interval = "daily"` ------[get_trx_market_data_for_last_n_days](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_last_n_days.html)(
  vs_currency = "usd",
  days = 10,
  interval = "daily"
) %>% glimpse()#> Rows: 11
#> Columns: 5
#> $ timestamp         <dttm> 2021-02-18 00:00:00, 2021-02-19 ...
#> $ vs_currency       <chr> "usd", "usd", "usd", "usd", "usd"...
#> $ price             <dbl> 0.05254076, 0.05504973, 0.0613594...
#> $ total_trading_vol <dbl> 2221799069, 1965989783, 354407505...
#> $ market_cap        <dbl> 3770282093, 3942038158, 438662175... # ------ More than 90 days, with `interval = "daily"` ------[get_trx_market_data_for_last_n_days](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_last_n_days.html)(
  vs_currency = "usd",
  days = 100,
  interval = "daily"
) %>% glimpse()#> Rows: 101
#> Columns: 5
#> $ timestamp         <dttm> 2020-11-20, 2020-11-21, 2020-11-...
#> $ vs_currency       <chr> "usd", "usd", "usd", "usd", "usd"...
#> $ price             <dbl> 0.02579945, 0.02621825, 0.0303699...
#> $ total_trading_vol <dbl> 762903574, 791787366, 1650443139,...
#> $ market_cap        <dbl> 1846615827, 1879975405, 217323112...
```

如果请求的天数涵盖了`2017–11–09`之前的日期，检索到的数据将在`2017–11–09`(TRX 历史的开始)被*截取*。将以下结果与之前创建的表`hist_max`进行比较:

```
hist_clipped <- get_trx_market_data_for_last_n_days(
  vs_currency = "usd",
  days = 100000,
  interval = "daily"
)min(hist_max$timestamp)
#> [1] "2017-11-09 UTC"min(hist_clipped$timestamp)
#> [1] "2017-11-09 UTC"
```

# 查询最近 n 天的 OHLC 数据

开盘-盘高-盘低-收盘( [OHLC](https://en.wikipedia.org/wiki/Open-high-low-close_chart) )数据描述了一项金融资产当天和当天之间的价格变动。可以使用`[get_trx_ohlc_data_for_last_n_days()](https://next-game-solutions.github.io/tronr/reference/get_trx_ohlc_data_for_last_n_days.html)`函数检索 TRX 的这类数据，该函数具有与`[get_trx_market_data_for_last_n_days()](https://next-game-solutions.github.io/tronr/reference/get_trx_market_data_for_last_n_days.html)`相同的参数，只是没有`interval`参数。检索数据(即蜡烛体)的粒度取决于`days`的值，如下所示:

*   1 天 30 分钟
*   7–30 天:4 小时
*   31 岁及以上:4 天

`days`参数当前接受的值只有 1、7、14、30、90、180、365 和`"max"`。以下是一些例子:

```
# ------ 30-min granularity ------[get_trx_ohlc_data_for_last_n_days](https://next-game-solutions.github.io/tronr/reference/get_trx_ohlc_data_for_last_n_days.html)(
   vs_currency = "usd", days = 1
) %>% glimpse()#> Rows: 49
#> Columns: 6
#> $ timestamp   <dttm> 2021-02-27 00:00:00, 2021-02-27 00:30:...
#> $ vs_currency <chr> "usd", "usd", "usd", "usd", "usd", "usd...
#> $ price_open  <dbl> 0.045396, 0.045326, 0.045813, 0.046517,...
#> $ price_high  <dbl> 0.045396, 0.045801, 0.046534, 0.046576,...
#> $ price_low   <dbl> 0.045188, 0.045326, 0.045813, 0.046204,...
#> $ price_close <dbl> 0.045344, 0.045660, 0.046534, 0.046204,... # ------ 4-hours granularity ------get_trx_ohlc_data_for_last_n_days(
   vs_currency = "usd", days = 7
) %>% glimpse()#> Rows: 42
#> Columns: 6
#> $ timestamp   <dttm> 2021-02-21 04:00:00, 2021-02-21 08:00:...
#> $ vs_currency <chr> "usd", "usd", "usd", "usd", "usd", "usd...
#> $ price_open  <dbl> 0.057651, 0.058873, 0.059038, 0.060590,...
#> $ price_high  <dbl> 0.059317, 0.059300, 0.060171, 0.060590,...
#> $ price_low   <dbl> 0.057651, 0.058873, 0.059038, 0.059617,...
#> $ price_close <dbl> 0.059317, 0.059134, 0.060171, 0.059912,... # ------ 4-days granularity ------[get_trx_ohlc_data_for_last_n_days](https://next-game-solutions.github.io/tronr/reference/get_trx_ohlc_data_for_last_n_days.html)(
   vs_currency = "usd", days = 90
) %>% glimpse()#> Rows: 24
#> Columns: 6
#> $ timestamp   <dttm> 2020-11-30, 2020-12-03, 2020-12-07, 20...
#> $ vs_currency <chr> "usd", "usd", "usd", "usd", "usd", "usd...
#> $ price_open  <dbl> 0.030608, 0.032244, 0.031609, 0.030334,...
#> $ price_high  <dbl> 0.030608, 0.032244, 0.031609, 0.030334,...
#> $ price_low   <dbl> 0.030608, 0.030360, 0.029581, 0.027870,...
#> $ price_close <dbl> 0.030608, 0.031228, 0.030940, 0.028154,... get_trx_ohlc_data_for_last_n_days(
   vs_currency = "usd", days = "max"
) %>% glimpse()#> Rows: 315
#> Columns: 6
#> $ timestamp   <dttm> 2017-11-11, 2017-11-15, 2017-11-19, 20...
#> $ vs_currency <chr> "usd", "usd", "usd", "usd", "usd", "usd...
#> $ price_open  <dbl> 0.002387, 0.001804, 0.002248, 0.002133,...
#> $ price_high  <dbl> 0.002387, 0.002415, 0.002248, 0.002333,...
#> $ price_low   <dbl> 0.001915, 0.001804, 0.001985, 0.002130,...
#> $ price_close <dbl> 0.001915, 0.002320, 0.001985, 0.002138,...
```

# 结论

这篇文章展示了使用 R package tronr 为 TRX 收集市场数据是多么容易。然后，这些数据可以输入到各种分析项目中，甚至可能输入到自动化应用程序中。但是，请注意，CoinGecko API 限制从一个给定的 IP 地址每分钟调用 100 次。在开发基于 tronr 的应用程序时，请记住这一点。

我将非常感谢这个包的用户的任何反馈、错误报告和贡献——详见该项目的 [Github 库](https://github.com/next-game-solutions/tronr)。

# 在你走之前

我提供数据科学咨询服务。[取得联系](mailto:sergey@nextgamesolutions.com)！