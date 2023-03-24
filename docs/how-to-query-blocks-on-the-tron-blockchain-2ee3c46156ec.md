# 如何在 TRON 区块链上查询区块

> 原文：<https://levelup.gitconnected.com/how-to-query-blocks-on-the-tron-blockchain-2ee3c46156ec>

## 就用 R 包 tronr

![](img/61d31980a5a5abc338a929f5f3ffa03a.png)

谢尔盖·马斯蒂斯基摄影

与任何其他区块链一样，TRON 网络上的事务被组织成块。块是逻辑单元，可以看作是分类帐中的页面。每个块都有一个 ID 和一个时间戳，并且包含在某个时间段内发生的事务的信息。在创区块链上，区块覆盖 3 秒的间隔。

获取一个区块或一组区块的信息通常是分析区块链数据的起点。本文演示了如何使用 R 包`[tronr](https://github.com/next-game-solutions/tronr)`来收集这些信息，这是一个探索创区块链的工具箱。

[](/introducing-tronr-an-r-package-to-explore-the-tron-blockchain-f0413f38b753) [## 介绍 tronr，一个探索 TRON 区块链的 R 包

### 查询账户余额、交易、代币转账等等。

levelup.gitconnected.com](/introducing-tronr-an-r-package-to-explore-the-tron-blockchain-f0413f38b753) 

# 查询单个块

可用`get_block_info()`功能查询单个程序块(详见[在线文档](https://next-game-solutions.github.io/tronr/reference/get_block_info.html))。默认情况下，此函数返回最新生成的块的数据:

```
library(dplyr)
library(tidyr)
library(tronr)#> R toolbox to explore the TRON blockchain
#> Developed by Next Game Solutions ([http://nextgamesolutions.com](http://nextgamesolutions.com))latest <- get_block_info()glimpse(latest)#> Rows: 1
#> Columns: 11
#> $ request_time    <dttm> 2021-03-14 16:29:36
#> $ block_number    <chr> "28449202"
#> $ timestamp       <dttm> 2021-03-14 16:28:36
#> $ hash            <chr> "0000000001b219b2d39d0fef84...
#> $ parent_hash     <chr> "0000000001b219b1b45025d87b...
#> $ tx_trie_root    <chr> "TAiDWyo2xFwWPAJGk6nHeDRoLy...
#> $ confirmed       <lgl> TRUE
#> $ size            <int> 28535
#> $ witness_address <chr> "TMG95kirH4cKW5GnKoCcCye1dB...
#> $ tx_count        <int> 117
#> $ tx              <list> [<tbl_df[117 x 4]>]
```

数据以[嵌套表](https://tidyr.tidyverse.org/articles/nest.html)的形式返回。从`tx`列可以看出这个表的嵌套性质，这是一个包含单个元素的列表——另一个表包含属于这个块的事务的基本数据:

```
latest %>% select(tx) %>% unnest(cols = tx)#> # A tibble: 117 x 4
#>    tx_id      contract_type  from_address   to_address  
#>    <chr>      <chr>          <chr>          <chr>       
#>  1 68ec65ea8~ TransferAsset~ TNA16cAySySgH~ TTbZWd1oWsi~
#>  2 f57a3223c~ TransferAsset~ TCqBZyPC9cXHt~ TUy87XWuwLm~
#>  3 e4a1030cd~ TriggerSmartC~ THtbMw6byXuiF~ TK5qKN9xJoL~
#>  4 6d1308bb5~ TransferAsset~ TQ1DnUTJ2Rp1i~ THWtE67AFDo~
#>  5 aa8974c97~ TransferAsset~ TDPPyq7NYE1pz~ TV6z2FS4dtu~
#>  6 2268325a6~ TransferContr~ TAK9Ms2HMfeuq~ TGtLNH2UFnC~
#>  7 564099e2c~ TransferContr~ TZCVhWWYweyC1~ TPmWzzLMHzY~
#>  8 e00fb3e87~ TransferAsset~ TJnrpbNpJwVhz~ TCDwjF4ich1~
#>  9 11e84be40~ TransferAsset~ TWJMJbiweMzQi~ TSg1NCBD44D~
#> 10 6d7a37fe4~ TransferAsset~ TDSVqaDqCxTba~ TVamy8pNphv~
#> # ... with 107 more rows
```

要获得特定块的数据，必须提供它的编号(作为一个字符值)并将参数`latest`切换到`FALSE`:

```
block <- get_block_info(latest = FALSE, block_number = "28449202")glimpse(block)#> Rows: 1
#> Columns: 11
#> $ request_time    <dttm> 2021-03-14 16:52:03
#> $ block_number    <chr> "28449202"
#> $ timestamp       <dttm> 2021-03-14 16:28:36
#> $ hash            <chr> "0000000001b219b2d39d0fef84...
#> $ parent_hash     <chr> "0000000001b219b1b45025d87b...
#> $ tx_trie_root    <chr> "TAiDWyo2xFwWPAJGk6nHeDRoLy...
#> $ confirmed       <lgl> TRUE
#> $ size            <int> 28535
#> $ witness_address <chr> "TMG95kirH4cKW5GnKoCcCye1dB...
#> $ tx_count        <int> 117
#> $ tx              <list> [<tbl_df[117 x 4]>]
```

# 查询时间范围

另一个功能`[get_blocks_for_time_range()](https://next-game-solutions.github.io/tronr/reference/get_blocks_for_time_range.html)`，允许用户收集一段时间内生成的几个块的数据。与其他`tronr`函数类似，该时间段的开始和结束必须作为 Unix 时间戳提供(要将 R 的 POSIXct datetime 值转换成这样的时间戳，请使用`[to_unix_timestamp()](https://next-game-solutions.github.io/tronr/reference/to_unix_timestamp.html)`):

```
range <- get_blocks_for_time_range(
    min_timestamp = "1551715200000",
    max_timestamp = "1551715210000"
)glimpse(range)#> Rows: 4
#> Columns: 13
#> $ block_number    <chr> "7203852", "7203851", "7203...
#> $ timestamp       <dttm> 2019-03-04 16:00:09, 2019-...
#> $ hash            <chr> "00000000006dec0cd76d5eee4b...
#> $ parent_hash     <chr> "00000000006dec0b36bce19a10...
#> $ tx_trie_root    <chr> "2Y3SaM5CxAEM1LAKw9F2sCHKU4...
#> $ confirmed       <lgl> TRUE, TRUE, TRUE, TRUE
#> $ revert          <lgl> FALSE, FALSE, FALSE, FALSE
#> $ size            <int> 22632, 20701, 21046, 19363
#> $ witness_address <chr> "TJ2aDMgeipmoZRuUEru2ri8t7T...
#> $ witness_name    <chr> "Huobi_pool", "BlockchainOr...
#> $ tx_count        <int> 83, 72, 75, 61
#> $ net_usage       <int> 36919, 24628, 34877, 22663
#> $ energy_usage    <int> 1996195, 1488224, 1307323, ...
```

结果是一个简单的(即非嵌套的)tibble，其内容与`get_block_info()`返回的内容有些不同。该表中特别令人感兴趣的是诸如`tx_count`(与一个块相关联的事务数量)、`net_usage`和`energy_usage`(事务成本的指示符)、`size`(块大小，以字节为单位)等列。(详见[在线文档](https://next-game-solutions.github.io/tronr/reference/get_blocks_for_time_range.html))。

# 结论

本文展示了使用 R 包`tronr`在 TRON 区块链上查询块是多么容易。我将非常感谢来自这个包的用户的任何反馈、错误报告和贡献——在项目的 [Github 库](https://github.com/next-game-solutions/tronr)找到进一步的细节。

另请参阅本系列教程的其他文章:

[](/show-me-the-money-how-to-query-tron-account-balances-4d62ff54c67b) [## 给我钱看:如何查询 TRON 账户余额

### 就用 R 包 tronr

levelup.gitconnected.com](/show-me-the-money-how-to-query-tron-account-balances-4d62ff54c67b) [](/an-easy-way-to-collect-market-data-for-tronix-trx-41e6e7c52555) [## 为 Tronix (TRX)收集市场数据的简单方法

### 就用 R 包 tronr

levelup.gitconnected.com](/an-easy-way-to-collect-market-data-for-tronix-trx-41e6e7c52555) 

# 在你走之前

我提供数据科学咨询服务。[取得联系](mailto:sergey@nextgamesolutions.com)！