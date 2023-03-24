# 给我钱看:如何查询 TRON 账户余额

> 原文：<https://levelup.gitconnected.com/show-me-the-money-how-to-query-tron-account-balances-4d62ff54c67b>

## 就用 R 包 tronr

![](img/3a4c61eef3e667059d52bb862d03cec6.png)

迈克尔·朗米尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

账户余额是可以从区块链中收集到的最有趣和最重要的数据类型之一。这种类型的数据支持各种分析应用，例如了解资金如何在网络上分配、感兴趣的账户持有哪些资产、余额的动态性如何等。

这篇文章展示了我最近发布的 R 包`[tronr](https://github.com/next-game-solutions/tronr)`如何用于查询 [TRON 区块链](https://tron.network/)上的账户余额(术语*账户*在这里指的是类似钱包的账户和智能合约)。查看这篇关于什么是`tronr`以及如何安装它的早期介绍文章:

[](/introducing-tronr-an-r-package-to-explore-the-tron-blockchain-f0413f38b753) [## 介绍 tronr，一个探索 TRON 区块链的 R 包

### 查询账户余额、交易、代币转账等等。

levelup.gitconnected.com](/introducing-tronr-an-r-package-to-explore-the-tron-blockchain-f0413f38b753) 

# 查询 Tronix (TRX)余额

特隆尼克(TRX，又名特隆)是特隆区块链的本地货币。账户当前持有的 TRX 金额可以通过`[get_account_trx_balance()](https://next-game-solutions.github.io/tronr/reference/get_account_trx_balance.html)`功能找回。该函数的主要参数是`address`，它(类似于所有将`address`作为其输入之一的`tronr`函数)接受[地址](https://developers.tron.network/docs/account#address-format)，格式为 [base58check](https://bitcoin.stackexchange.com/questions/75527/eli5-what-is-base58check-encoding) (以`T`开始)或十六进制格式(以`41`开始):

```
library(dplyr)
library(tidyr)
library(tronr)# Using a human-readable base58check-formatted address:
bal <- [get_account_trx_balance](https://next-game-solutions.github.io/tronr/reference/get_account_trx_balance.html)(
    address = "TQjaZ9FD473QBTdUzMLmSyoGB6Yz1CGpux"
)bal
#> # A tibble: 1 x 3
#>  request_time        address                        trx_balance
#>  <dttm>              <chr>                                <dbl>
#> 1 2021-03-06 21:58:02 TQjaZ9FD473QBTdUzMLmSyoGB6Yz1~    4888953. # Using the same address as above, but first converting it
# to hex format:
hex_address <- [convert_address](https://next-game-solutions.github.io/tronr/reference/convert_address.html)("TQjaZ9FD473QBTdUzMLmSyoGB6Yz1CGpux")hex_address
#> [1] "41a1f60e12be07004934ead89620f1ba0bebbe128e"bal2 <- [get_account_trx_balance](https://next-game-solutions.github.io/tronr/reference/get_account_trx_balance.html)(address = hex_address)bal2
#> # A tibble: 1 x 3
#>  request_time        address                        trx_balance
#>  <dttm>              <chr>                                <dbl>
#> 1 2021-03-06 21:59:23 TQjaZ9FD473QBTdUzMLmSyoGB6Yz1~    4888953.
```

使用`[get_current_trx_price()](https://next-game-solutions.github.io/tronr/reference/get_current_trx_price.html)`函数可以将获得的 TRX 余额转换成法定货币，比如美元(在底层，该函数调用[coingeko API](https://www.coingecko.com/api/documentations/v3)来查询相应货币的当前 TRX 价格):

```
price <- [get_current_trx_price](https://next-game-solutions.github.io/tronr/reference/get_current_trx_price.html)(vs_currencies = [c](https://rdrr.io/r/base/c.html)("usd"))
bal$trx_balance * price$trx_price
#> [1] 244579.7
```

[](/an-easy-way-to-collect-market-data-for-tronix-trx-41e6e7c52555) [## 为 Tronix (TRX)收集市场数据的简单方法

### 就用 R 包 tronr

levelup.gitconnected.com](/an-easy-way-to-collect-market-data-for-tronix-trx-41e6e7c52555) 

除了特定账户的 TRX 余额，还可以获得主要 TRX 持有者的列表(截至查询时):

```
# Getting a list of top-20 TRX holders:
[list_top_trx_holders](https://next-game-solutions.github.io/tronr/reference/list_top_trx_holders.html)(n = 20)#> # A tibble: 20 x 5                                            
#>    request_time        address   trx_balance total_tx tron_power
#>    <dttm>              <chr>           <dbl>    <int>      <dbl>
#>  1 2021-03-06 21:39:38 TNUC9Qb1~     1.47e10  1326915          0
#>  2 2021-03-06 21:39:38 TQcia2H2~     2.91e 9   269599          0
#>  3 2021-03-06 21:39:38 TA9FnQrL~     2.38e 9    57671   23000000
#>  4 2021-03-06 21:39:38 TNaRAoLU~     1.92e 9 12920348          0
#>  5 2021-03-06 21:39:38 TE2RzoSV~     1.78e 9   126235          0
#>  6 2021-03-06 21:39:38 TQn9Y2kh~     1.31e 9  3165150          0
#>  7 2021-03-06 21:39:38 TTd9qHyj~     1.26e 9    83813          0
#>  8 2021-03-06 21:39:38 TWd4WrZ9~     1.20e 9      598 3239581502
#>  9 2021-03-06 21:39:38 TKAtLoCB~     9.61e 8   204997          0
#> 10 2021-03-06 21:39:38 TA5vCXk4~     7.45e 8   343487          0
#> 11 2021-03-06 21:39:38 TLgzJvHD~     7.14e 8  1379806          0
#> 12 2021-03-06 21:39:38 TAUN6Fwr~     6.61e 8 18912015          0
#> 13 2021-03-06 21:39:38 TYukBQZ2~     6.45e 8   650231          0
#> 14 2021-03-06 21:39:38 TDDfvZoT~     6.16e 8     1245          0
#> 15 2021-03-06 21:39:38 TH2mEwTK~     6.03e 8   233316          0
#> 16 2021-03-06 21:39:38 TYN6Wh11~     5.79e 8   239220          0
#> 17 2021-03-06 21:39:38 TTCixdqA~     3.20e 8       47          0
#> 18 2021-03-06 21:39:38 TJ3yu4JJ~     2.56e 8      317  297738352
#> 19 2021-03-06 21:39:38 TPoWniNM~     2.38e 8       68          0
#> 20 2021-03-06 21:39:38 TVrZ3Pjj~     2.25e 8   119458          0
```

# 查询完整的帐户详细信息

使用`[get_account_balance()](https://next-game-solutions.github.io/tronr/reference/get_account_balance.html)`函数可以获得帐户余额的更全面的视图，该函数返回一个[嵌套 tible](https://tidyr.tidyverse.org/articles/nest.html):

```
r <- [get_account_balance](https://next-game-solutions.github.io/tronr/reference/get_account_balance.html)(
    address = "TQjaZ9FD473QBTdUzMLmSyoGB6Yz1CGpux"
)glimpse(r)#> Rows: 1
#> Columns: 10
#> $ request_time <dttm> 2021-03-06 21:22:46
#> $ address      <chr> "TQjaZ9FD473QBTdUzMLmSyoGB6Yz1CGpux"
#> $ name         <chr> "SunTRXV3Pool"
#> $ total_tx     <int> 69046
#> $ bandwidth    <list> [<tbl_df[1 x 20]>]
#> $ trx_balance  <dbl> 4888953
#> $ n_trc20      <int> 16
#> $ trc20        <list> [<tbl_df[16 x 7]>]
#> $ n_trc10      <int> 12
#> $ trc10        <list> [<tbl_df[12 x 8]>]
```

在`bandwidth`(账户当前[能量和带宽资源](https://tronprotocol.github.io/documentation-en/introduction/overview/#8-resource-model))、`trc20`(账户持有的 [TRC-20 令牌](https://developers.tron.network/docs/trc20))、`trc10`(账户持有的 [TRC-10 令牌](https://developers.tron.network/docs/trc10))列中可以看到该 tibble 的嵌套结构。这些列中的每一列都包含一个具有单个元素的列表——另一个列表包含相应的数据。这些嵌套数据可以通过使用标准 R 索引机制或应用`[tidyr::unnest()](https://tidyr.tidyverse.org/reference/nest.html)`函数来检索:

```
# Extract data on the TRC-20 tokens held by the account,
# using the standard R indexing:
r$trc20[[1]]#> # A tibble: 16 x 7
#>    token_contract_~ token_name token_abbr token_type balance
#>    <chr>            <chr>      <chr>      <chr>        <dbl>
#>  1 TKkeiboTkxXKJpb~ SUN        SUN        trc20      3.28e+1
#>  2 TVogNDwsyGYuEcA~ 42 Defense 42D        trc20      1.00e-1
#>  3 TV2DYBjPY2SJuHQ~ AAA DeFi ~ AAA        trc20      1.00e+0
#>  4 TRZPqchWTRUzXNG~ AISwap     AIS        trc20      1.00e+0
#>  5 TDndaG9V79f3dVu~ ANB Finan~ ANB        trc20      1.12e-7
#>  6 TU7uTrWbnt9RVQJ~ CLAM HOLD~ CLAM       trc20      1.00e-6
#>  7 TMqgAJSuMEq5EQW~ EAB Finan~ EAB        trc20      1.00e-4
#>  8 THSjCwmYVeK1oLR~ EAB Finan~ EAB        trc20      1.11e-3
#>  9 TY2p1N32AEBtpqT~ GOLF Token GOLF       trc20      1.00e-2
#> 10 TQXhxDPNKVsgxYb~ GoldenEggs GLDE       trc20      2.00e-6
#> 11 TASrsr1Qsz8eUSa~ HYPE Token HYPE       trc20      1.25e-1
#> 12 TCwsQqyVfTNwNm5~ MAVRONA C~ MAVRONA    trc20      2.00e+0
#> 13 TM6oGQHC9Fq6d9t~ NEXA Token NEXA       trc20      4.12e-1
#> 14 THJPvg68wH4wbAk~ O K E X _~ OKU        trc20      1.00e-6
#> 15 TUAbNGTPe7uCMcd~ SNDCOIN    SNDC       trc20      1.00e-2
#> 16 TLvdAAh7hxDWvvh~ StakeAnbF~ SKE        trc20      1.00e-6
#> # ... with 2 more variables: token_price_in_trx <dbl>,
#> #   vip <lgl># The same result could be obtained with the following command:
# r %>% [select](https://dplyr.tidyverse.org/reference/select.html)(trc20) %>% [unnest](https://tidyr.tidyverse.org/reference/nest.html)(cols = trc20)
```

# 结论

本文展示了使用 R 包`tronr`查询 TRON 账户余额是多么容易。我将非常感谢来自这个包的用户的任何反馈、错误报告和贡献——详见该项目的 [Github 库](https://github.com/next-game-solutions/tronr)。

# 在你走之前

我提供数据科学咨询服务。[取得联系](mailto:sergey@nextgamesolutions.com)！