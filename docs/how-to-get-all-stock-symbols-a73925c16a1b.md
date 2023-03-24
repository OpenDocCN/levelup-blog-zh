# 如何获取所有股票符号

> 原文：<https://levelup.gitconnected.com/how-to-get-all-stock-symbols-a73925c16a1b>

## 没有真正尝试…

![](img/0ea0d8334311ca8440fcfa8749dde6a7.png)

[M. B. M.](https://unsplash.com/@m_b_m?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/graphs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

现在我退休了，我有很多空闲时间。对股票市场的兴趣现在占据了我的一些时间。学习股票的第一步是知道有哪些股票。股票代码列表在很多地方都可以以. csv 格式下载，但我喜欢快速高效。我写这个脚本就是为了做到这一点。

首先，导入语句。

```
import pandas as pd
from yahoo_fin import stock_info as si
```

美国有三大股票交易所。它们是道琼斯、纳斯达克和标准普尔 500。还有第四类符号叫做“其他”。我们下载每一类列表来分离熊猫数据帧。

```
df1 = pd.DataFrame*(* si.tickers_sp500*() )* df2 = pd.DataFrame*(* si.tickers_nasdaq*() )* df3 = pd.DataFrame*(* si.tickers_dow*() )* df4 = pd.DataFrame*(* si.tickers_other*() )*
```

接下来，我们将每个数据帧转换成一个列表，然后转换成一个集合。

```
sym1 = set*(* symbol for symbol in df1*[*0*]*.values.tolist*() )* sym2 = set*(* symbol for symbol in df2*[*0*]*.values.tolist*() )* sym3 = set*(* symbol for symbol in df3*[*0*]*.values.tolist*() )* sym4 = set*(* symbol for symbol in df4*[*0*]*.values.tolist*() )*
```

股票代码可以在多个交易所上市。我们把这四组合二为一。因为是集合，所以不会有重复的符号。

```
symbols = set.union*(* sym1, sym2, sym3, sym4 *)*
```

大多数符号的长度不超过四个字母，例如:MSFT 代表微软。有些符号有第五个字母。一个[第五个字母](https://www.investopedia.com/ask/answers/06/nasdaqfifthletter.asp)大多被添加到在某些交易要求中拖欠的股票中。我们将确定删除其中的四个后缀。

```
my_list = *[*'W', 'R', 'P', 'Q'*]*
```

w 表示有未兑现的认股权证。我们不想要那些。

r 表示存在某种“权利”问题。再说一遍，不被通缉。

p 表示“第一优先发行”。优先股是一个独立的实体。

q 代表破产。我们也不想要这些。

我想消除这些符号的原因是它们需要很长时间，然后在下载后续数据时返回错误。

我们需要启动两套系统。存储集和删除集。

```
del_set = set*()* sav_set = set*()*
```

接下来，我们找到长度超过四个字符的符号，并将它们的最后一个字母放在 *my_list* 中。当找到时，它们被添加到 *del_set* 。所有其他符号被添加到 *sav_set* 中。

```
for symbol in symbols:
    if len*(* symbol *)* > 4 and symbol*[*-1*]* in my_list:
        del_set.add*(* symbol *)*
    else:
        sav_set.add*(* symbol *)*
```

最后，我们打印结果。

```
print*(* f'Removed *{*len*(* del_set *)}* unqualified stock symbols...' *)* print*(* f'There are *{*len*(* sav_set *)}* qualified stock symbols...' *)*
```

这是输出。

```
Removed 445 unqualified stock symbols…
There are 9193 qualified stock symbols…Process finished with exit code 0
```

现在我们有了一个完整的股票代码列表，可以开始我们的数据处理之旅了。您可能希望将 *sav_set* 保存为您喜欢的数据库或存储格式。

如果你喜欢阅读这样的故事，并想支持我成为一名作家，可以考虑每月 5 美元订阅 Medium。作为会员，你可以无限制地访问媒体上的故事。如果你用我的[链接](https://zenndogg-52643-medium.com/membership)注册，我会赚一小笔佣金。

下面提供了整个脚本。愉快的探索。

```
import pandas as pd
from yahoo_fin import stock_info as si

# gather stock symbols from major US exchanges
df1 = pd.DataFrame*(* si.tickers_sp500*() )* df2 = pd.DataFrame*(* si.tickers_nasdaq*() )* df3 = pd.DataFrame*(* si.tickers_dow*() )* df4 = pd.DataFrame*(* si.tickers_other*() )* # convert DataFrame to list, then to sets
sym1 = set*(* symbol for symbol in df1*[*0*]*.values.tolist*() )* sym2 = set*(* symbol for symbol in df2*[*0*]*.values.tolist*() )* sym3 = set*(* symbol for symbol in df3*[*0*]*.values.tolist*() )* sym4 = set*(* symbol for symbol in df4*[*0*]*.values.tolist*() )* # join the 4 sets into one. Because it's a set, there will be no duplicate symbols
symbols = set.union*(* sym1, sym2, sym3, sym4 *)* # Some stocks are 5 characters. Those stocks with the suffixes listed below are not of interest.
my_list = *[*'W', 'R', 'P', 'Q'*]* del_set = set*()* sav_set = set*()* for symbol in symbols:
    if len*(* symbol *)* > 4 and symbol*[*-1*]* in my_list:
        del_set.add*(* symbol )
    else:
        sav_set.add*(* symbol *)*

print*(* f'Removed *{*len*(* del_set *)}* unqualified stock symbols...' *)* print*(* f'There are *{*len*(* sav_set *)}* qualified stock symbols...' *)*
```