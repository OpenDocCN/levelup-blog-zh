# 在类似 Linux 或 Unix 的 Shells 上处理日期和时间

> 原文：<https://levelup.gitconnected.com/working-with-dates-and-times-on-linux-or-unix-like-shells-daefc5c4090c>

![](img/a45d68fb625e9143a9ae6357fb1201fe.png)

凯文·霍尔瓦特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

如果您经常使用 shell，您可能经常需要操作日期和时间。日期和时间可以用多种方式格式化，每种方式都有特殊的语法。因此，与他们合作并不容易。我使用一个 macOS xnu 内核，我将演示一些命令是如何工作的。关于日期和时间语法的更多细节，您也可以参考本文末尾的表格。

我们首先在 shell 中打印出当前的日期和时间。

```
$ dateTue Nov 17 15:47:20 EST 2020
```

使用本文末尾表格中 GNU 和 BSD 的格式控制字符，您也可以控制它的格式化方式。从表中可以推断出，每个控制字符代表日期和/或时间的不同方面。下面的“加号”表示用户将指定格式。

```
$ date +"Right now it is %A, %B %d, %Y."Right now it is Tuesday, November 17, 2020.
$ date +"%l:%M %p %Z"4:03 PM EST
```

您也可以将日期和时间保存在变量中。“echo”在下一行打印出任何变量。

```
$ TODAY=$(date +"%D")
$ echo $TODAY11/17/20
```

现在事情变得更加复杂和令人困惑。我们将保存用户指定的日期和时间。假设我们从 2020 年 6 月 29 日到 7 月 2 日每天从 Twitter 下载带有特定标签的推文。我们以 YYYY-MM-DD (%F)的格式指定我们测量的第一秒以及我们测量的间隔之后的第一秒，并希望将它们保存为自 1970–01–01 00:00:00 UTC(% s)以来的秒数。

```
$ first=$(date -j -f "%F" 2020-06-29 +"%s")
$ echo $first1593463202
$ last=$(date -j -f "%F" 2020-07-03 +"%s")
$ echo $last1593808656
```

我们以这种格式保存这些日期，因为在 Mac shell 中，增加或减少时间的最佳方式是增加或减少秒数。我们以后总是可以用其他格式显示日期。因此，我们创建了一个名为 SECONDS_IN_DAY 的变量，我们将使用它将日期增加一天。

```
SECONDS_IN_DAY=86400
```

最后，如前所述，我们遍历日期范围，以可读性更好的格式打印日期。

```
while [ "$first" != "$last" ]; do
  start=$(date -j -f "%s" $first +"%Y.%m.%d 00:00")
  first=$((first + $SECONDS_IN_DAY))
  end=$(date -j -f "%s" $first +"%Y.%m.%d 00:00")
  echo In the real script, this line of code would call Twitter to download tweets from $start to $end.
done
```

如果所有变量都已正确初始化，则此循环的输出为:

```
In the real script, this line of code would call Twitter to download tweets from 2020.06.29 00:00 to 2020.06.30 00:00.In the real script, this line of code would call Twitter to download tweets from 2020.06.30 00:00 to 2020.07.01 00:00.In the real script, this line of code would call Twitter to download tweets from 2020.07.01 00:00 to 2020.07.02 00:00.In the real script, this line of code would call Twitter to download tweets from 2020.07.02 00:00 to 2020.07.03 00:00.
```

这里是 GNU 中日期和时间的格式控制字符的完整列表。

下面是 BSD 中日期和时间的格式控制字符的完整列表。

快乐脚本！

![](img/b055f8e6fe06f996c7223bf6fde0816d.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Vinayak Sharma](https://unsplash.com/@vinayak_125?utm_source=medium&utm_medium=referral) 拍摄的照片