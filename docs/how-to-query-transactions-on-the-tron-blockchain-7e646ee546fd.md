# 如何在 TRON 区块链上查询交易

> 原文：<https://levelup.gitconnected.com/how-to-query-transactions-on-the-tron-blockchain-7e646ee546fd>

## 就用 R 包`tronr`

![](img/ce5d4907686e1207e7b7cec443949e8d.png)

由 [Malcolm Lightbody](https://unsplash.com/@mlightbody?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

B 锁链交易是两个或多个地址之间交互的记录。在 [TRON 区块链](https://tron.network/)上，通常有两个交互地址，它们之间的交互可以采取[许多不同的形式](https://tronprotocol.github.io/documentation-en/mechanism-algorithm/system-contracts/)(例如，创建新账户或资产、触发智能合同、转移资产等。).每笔交易都可以通过包含 64 个字母数字字符的散列 ID 来唯一识别。

获取一项或一组交易的信息是区块链数据分析的核心。本文演示了如何使用 R package `[tronr](https://github.com/next-game-solutions/tronr)`收集这些信息，这是一个探索 TRON 网络的工具箱。

[](/introducing-tronr-an-r-package-to-explore-the-tron-blockchain-f0413f38b753) [## 介绍 tronr，一个探索 TRON 区块链的 R 包

### 查询账户余额、交易、代币转账等等。

levelup.gitconnected.com](/introducing-tronr-an-r-package-to-explore-the-tron-blockchain-f0413f38b753) 

# 查询单个交易

`tronr`包中有几个函数允许用户查询交易数据。其中一个关键函数是`[get_tx_info_by_id()](https://next-game-solutions.github.io/tronr/reference/get_tx_info_by_id.html)`，它根据 ID 返回事务的属性(以[嵌套 tible](https://tidyr.tidyverse.org/articles/nest.html)的形式)。可以使用其他几个`tronr`功能获得这些 id，例如`[get_block_info()](https://next-game-solutions.github.io/tronr/reference/get_block_info.html)`、`[get_blocks_for_time_range()](https://next-game-solutions.github.io/tronr/reference/get_blocks_for_time_range.html)`、`[get_tx_for_time_range()](https://next-game-solutions.github.io/tronr/reference/get_tx_for_time_range.html)`等。这里有一个例子:

```
require(tronr)
require(dplyr)
require(tidyr)#> R toolbox to explore the TRON blockchain
#> Developed by Next Game Solutions ([http://nextgamesolutions.com](http://nextgamesolutions.com/))# Get transactions of the latest block:
latest_block <- [get_block_info](https://next-game-solutions.github.io/tronr/reference/get_block_info.html)(latest = TRUE)# Pick an example transaction:
tx_id <- latest_block$tx[[1]]$tx_id[1]tx_id
# "074ce32ed2ca89c69e54e4ac4ff5ee825df33f6cf087d869c44dc3456f349855"# Retrieve transaction attributes (see [documentation](https://next-game-solutions.github.io/tronr/reference/get_tx_info_by_id.html) for their 
# definitions):
r1 <- [get_tx_info_by_id](https://next-game-solutions.github.io/tronr/reference/get_tx_info_by_id.html)(tx_id = tx_id, add_contract_data = FALSE)glimpse(r1)#> Rows: 1
#> Columns: 19
#> $ request_time             <dttm> 2021-03-31 19:22:57
#> $ tx_id                    <chr> "074ce32ed2ca89c69e54e4ac4f...
#> $ block_number             <chr> "28941541"
#> $ timestamp                <dttm> 2021-03-31 19:19:06
#> $ contract_result          <chr> "SUCCESS"
#> $ confirmed                <lgl> TRUE
#> $ confirmations_count      <int> 71
#> $ sr_confirm_list          <list> [<tbl_df[19 x 3]>]
#> $ contract_type            <chr> "TriggerSmartContract"
#> $ from_address             <chr> "TSrS5zMUgzHe688XcZ4PnN5Y3c...
#> $ to_address               <chr> "TDxYAUHTw7Tk9NQfDJk9wmcsb2...
#> $ is_contract_from_address <lgl> FALSE
#> $ is_contract_to_address   <lgl> TRUE
#> $ costs                    <list> [<tbl_df[1 x 8]>]
#> $ trx_transfer             <dbl> 9.906872
#> $ trc10_transfer           <lgl> NA
#> $ trc20_transfer           <list> [<tbl_df[1 x 9]>]
#> $ internal_tx              <list> [<tbl_df[3 x 12]>]
#> $ info                     <lgl> NA
```

[](/how-to-query-blocks-on-the-tron-blockchain-2ee3c46156ec) [## 如何在 TRON 区块链上查询区块

### 就用 R 包 tronr

levelup.gitconnected.com](/how-to-query-blocks-on-the-tron-blockchain-2ee3c46156ec) 

如果`add_contact_data`参数被设置为`TRUE`，那么结果 tibble 也将有一个名为`contract_data`的列。该列将包含一个列表，其中包含由执行感兴趣的交易的智能合约生成的原始数据。该清单实际内容将取决于每项交易和相应合同的性质:

```
r2 <- [get_tx_info_by_id](https://next-game-solutions.github.io/tronr/reference/get_tx_info_by_id.html)(tx_id = tx_id, add_contract_data = TRUE)glimpse(r1)#> Rows: 1
#> Columns: 20
#> $ request_time             <dttm> 2021-03-31 19:28:23
#> $ tx_id                    <chr> "074ce32ed2ca89c69e54e4ac4f...
#> $ block_number             <chr> "28941541"
#> $ timestamp                <dttm> 2021-03-31 19:19:06
#> $ contract_result          <chr> "SUCCESS"
#> $ confirmed                <lgl> TRUE
#> $ confirmations_count      <int> 180
#> $ sr_confirm_list          <list> [<tbl_df[19 x 3]>]
#> $ contract_type            <chr> "TriggerSmartContract"
#> $ from_address             <chr> "TSrS5zMUgzHe688XcZ4PnN5Y3c...
#> $ to_address               <chr> "TDxYAUHTw7Tk9NQfDJk9wmcsb2...
#> $ is_contract_from_address <lgl> FALSE
#> $ is_contract_to_address   <lgl> TRUE
#> $ costs                    <list> [<tbl_df[1 x 8]>]
#> $ trx_transfer             <dbl> 9.906872
#> $ trc10_transfer           <lgl> NA
#> $ trc20_transfer           <list> [<tbl_df[1 x 9]>]
#> $ internal_tx              <list> [<tbl_df[3 x 12]>]
#> $ info                     <lgl> NA
#> $ contract_data            <list> [["422f1043000000000000000...r2$contract_data[[1]]#> $data
#> [1]"422f1043000000000000000000000000000000000000000000000000...
#>
#> $owner_address
#> [1] "TSrS5zMUgzHe688XcZ4PnN5Y3cHQA3euWt"
#> 
#> $contract_address
#> [1] "TDxYAUHTw7Tk9NQfDJk9wmcsb26S8kHbdF"
#> $call_value
#> [1] 9906872
```

请注意，`[get_tx_info_by_id()](https://next-game-solutions.github.io/tronr/reference/get_tx_info_by_id.html)`返回的 tibbles 中的所有代币金额(见`trx_transfer`、`trc10_transfer`、`trc20_transfer`和`internal_tx`列)均使用整数和小数部分表示。然而，如果`add_contract_data = TRUE`，则返回的原始合同数据“按原样”呈现(即，没有任何解析或其他处理)，因此该数据中存在的任何令牌数量都使用机器级精度来表示。详见`[apply_decimal](https://next-game-solutions.github.io/tronr/reference/apply_decimal.html)()`。

# 查询时间范围

要检索一段时间内的事务及其属性的列表，可以使用`[get_tx_for_time_range()](https://next-game-solutions.github.io/tronr/reference/get_tx_for_time_range.html)`函数。与`[get_tx_info_by_id()](https://next-game-solutions.github.io/tronr/reference/get_tx_info_by_id.html)`相比，该函数有两个额外的参数来定义感兴趣的时间范围— `min_timestamp`和`max_timestamp`。这两个附加参数都需要 Unix 时间戳(包括毫秒):

```
tx_df <- [get_tx_for_time_range](https://next-game-solutions.github.io/tronr/reference/get_tx_for_time_range.html)(min_timestamp = "1577836800000",
                               max_timestamp = "1577836803000")glimpse(tx_df)#> Rows: 41
#> Columns: 20
#> $ request_time             <dttm> 2021-03-31 19:45:21, 2...
#> $ tx_id                    <chr> "5f131118e7e24725906a72...
#> $ block_number             <chr> "15860581", "15860581",...
#> $ timestamp                <dttm> 2020-01-01, 2020-01-01...
#> $ contract_result          <chr> "SUCCESS", "SUCCESS", "...
#> $ confirmed                <lgl> TRUE, TRUE, TRUE, TRUE,...
#> $ confirmations_count      <int> 13081480, 13081480, 130...
#> $ sr_confirm_list          <list> [<tbl_df[19 x 3]>, <tb...
#> $ contract_type            <chr> "TransferAssetContract"...
#> $ from_address             <chr> "TXmUfpBfxRTdbZXhzuqEJK...
#> $ to_address               <chr> "TCQBxaNNQ2h1HbrWxWSg7A...
#> $ is_contract_from_address <lgl> FALSE, FALSE, FALSE, FA...
#> $ is_contract_to_address   <lgl> FALSE, TRUE, TRUE, TRUE...
#> $ costs                    <list> [<tbl_df[1 x 8]>, <tbl...
#> $ trx_transfer             <dbl> 0.000, 200.000, 0.000, ...
#> $ trc10_transfer           <list> [<tbl_df[1 x 5]>, NULL...
#> $ trc20_transfer           <lgl> NA, NA, NA, NA, NA, NA,...
#> $ internal_tx              <list> [NULL, NULL, <tbl_df[1...
#> $ info                     <lgl> NA, NA, NA, NA, NA, NA,...
#> $ contract_data            <list> [[10000000, "1002830",...
```

请注意，`[get_tx_for_time_range()](https://next-game-solutions.github.io/tronr/reference/get_tx_for_time_range.html)`在幕后进行了多次 [Tronscan API](https://github.com/tronscan/tronscan-frontend/blob/master/document/api.md) 调用。由于 TRON 区块链上发生的交易数量非常大，因此建议用户明智地选择`min_timestamp`和`max_timestamp`。如果请求的时间范围太大，由底层 Tronscan API 返回的最大事务数将被限制在 10000 个。在这种情况下，将感兴趣的时间范围分成更小的时间段有助于避免数据出现缺口。然而，用户必须实现他们自己的逻辑。

# 查询特定于帐户的交易

还可以使用`[get_tx_info_by_account_address()](https://next-game-solutions.github.io/tronr/reference/get_tx_info_by_account_address.html)`函数来检索特定账户的交易数据。此外，这可以在特定的时间范围内完成:

```
tx_df_acc <- [get_tx_info_by_account_address](https://next-game-solutions.github.io/tronr/reference/get_tx_info_by_account_address.html)(
  address = "TAUN6FwrnwwmaEqYcckffC7wYmbaS6cBiX",
  min_timestamp = "1577836800000",
  max_timestamp = "1577838600000"
)

glimpse(tx_df_acc)#> Rows: 18
#> Columns: 21
#> $ request_time             <dttm> 2021-03-31 19:55:31, 2...
#> $ address                  <chr> "TAUN6FwrnwwmaEqYcckffC...
#> $ tx_id                    <chr> "36ec18062510f22a469bfb...
#> $ block_number             <chr> "15860591", "15860591",...
#> $ timestamp                <dttm> 2020-01-01 00:00:36, 2...
#> $ contract_result          <chr> "SUCCESS", "SUCCESS", "...
#> $ confirmed                <lgl> TRUE, TRUE, TRUE, TRUE,...
#> $ confirmations_count      <int> 13081672, 13081672, 130...
#> $ sr_confirm_list          <list> [<tbl_df[19 x 3]>, <tb...
#> $ contract_type            <chr> "TransferContract", "Tr...
#> $ from_address             <chr> "TAUN6FwrnwwmaEqYcckffC...
#> $ to_address               <chr> "TDn2MK7n5SqVksSZtQDAhD...
#> $ is_contract_from_address <lgl> FALSE, FALSE, FALSE, FA...
#> $ is_contract_to_address   <lgl> FALSE, FALSE, FALSE, FA...
#> $ costs                    <list> [<tbl_df[1 x 8]>, <tbl...
#> $ trx_transfer             <dbl> 664296.00000, 925.55360...
#> $ trc10_transfer           <list> [NULL, NULL, NULL, NUL...
#> $ trc20_transfer           <lgl> NA, NA, NA, NA, NA, NA,...
#> $ internal_tx              <lgl> NA, NA, NA, NA, NA, NA,...
#> $ info                     <lgl> NA, NA, NA, NA, NA, NA,...
#> $ contract_data            <list> [[6.64296e+11, "TAUN6F...
```

# 结论

本文展示了使用 R 包`tronr`从 TRON 区块链收集事务数据是多么容易。我将非常感谢来自这个包的用户的任何反馈、错误报告和贡献——请在该项目的 [Github 库](https://github.com/next-game-solutions/tronr)找到进一步的细节。

另请参阅本系列教程的其他文章:

[](/an-easy-way-to-collect-market-data-for-tronix-trx-41e6e7c52555) [## 为 Tronix (TRX)收集市场数据的简单方法

### 就用 R 包 tronr

levelup.gitconnected.com](/an-easy-way-to-collect-market-data-for-tronix-trx-41e6e7c52555) [](/show-me-the-money-how-to-query-tron-account-balances-4d62ff54c67b) [## 给我钱看:如何查询 TRON 账户余额

### 就用 R 包 tronr

levelup.gitconnected.com](/show-me-the-money-how-to-query-tron-account-balances-4d62ff54c67b) 

# 在你走之前

我提供数据科学咨询服务。[取得联系](mailto:sergey@nextgamesolutions.com)！