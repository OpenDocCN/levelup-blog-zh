# 5 个 PHP 日期函数及其 MySQL 等价物

> 原文：<https://levelup.gitconnected.com/5-php-date-functions-and-their-mysql-equivalents-a70c208e4d13>

任何处理数据的人都会在某个时候遇到日期值。出于许多原因，日期是必要的。如果您是 PHP/MySQL 开发人员，您可以选择数据库和编程语言的日期函数选项。在这篇文章中，我将介绍 5 个 PHP 日期函数和它们的(某种程度上)MySQL 等价物，并分别给出例子。继续阅读…

![](img/0346b213dc13581723615fab387c8912.png)

自我推销:

如果你喜欢这里写的内容，尽一切办法，把这个博客和你最喜欢的帖子分享给其他可能从中受益或喜欢它的人。[因为咖啡是我最喜欢的饮料，如果你愿意，你甚至可以给我买一杯！](http://ko-fi.com/joshlovescoffee)

***OpenLamp.tech*** ，面向 PHP 和 MySQL 开发者的时事通讯。[立即加入成长社区](http://openlamptech.substack.com)。

# PHP:DATE _ FORMAT()| MySQL:DATE _ FORMAT()

我们总是希望数据以我们需要的格式来到我们面前。实际上，我们更清楚，通常情况下，数据是以一种我们不能使用或不想使用的方式格式化的(如果有的话)。也就是说，您可以使用这两个函数中的任何一个，并且*将*日期数据转换成几乎任何您需要的格式。

这两个函数都有无数不同的格式选项，在一篇博客文章中涵盖所有这些选项会非常复杂。相反，我将介绍几个我熟悉的常见问题，并将探索留给您自己。

## 日期格式()

语法 PHP `date_format()`:

```
date_format($object, $format)
```

*   返回由格式指定的日期。
*   这两个参数都是必需的。

使用`date_create()`函数，我创建了一个练习日期，我们可以用它来举例:

*在浏览器中回显*返回:

```
2021-04-12
```

为了将日期值格式化为完整的月份名称、2 位数的日(带后缀)和 4 位数的年，我们可以使用以下格式参数:

它返回:

```
December 4th, 2021
```

通常，您会看到 2 位数的月、2 位数的日和 4 位数的年格式(MM/DD/YYYY)。以下示例中显示的这些格式字符串*会为您生成该格式的*:

(PHP 的`date_format()`有几个格式选项。请访问[date _ format()文档](https://www.php.net/manual/en/datetime.format.php)查看全部内容。)

## 日期格式()

语法 MySQL `DATE_FORMAT()`:

```
DATE_FORMAT(date, format)
```

*   返回由格式指定的日期。
*   这两个参数都是必需的。

为简单起见，我使用了来自 [sakila 实践数据库](https://dev.mysql.com/doc/sakila/en/)的*“商店”*表，其中包含以下数据:

对于 3 个字母的月份缩写、2 位数的日期(带后缀)和完整的 4 位数年份，我们可以使用以下格式说明符:

正如您在对“store”表的第一个*探索性*查询中看到的,“last_update”列日期值以“YYYY-MM-DD”格式存储。如果这些值还没有这样存储，您可以很容易地使用下一个查询中显示的格式说明符对它们进行格式化:

您看到的一种常见格式是“DD-MON-YY”格式(许多 Oracle SQL 日期都是以这种格式存储的)。该*模式*也有格式说明符:

(有关更多信息，请访问[这个 MySQL DATE_FORMAT()资源](https://www.w3schools.com/sql/func_mysql_date_format.asp)。)

# PHP: date_diff() | MySQL: DATEDIFF()

PHP 的`date_diff()`函数返回两个日期时间对象之间的差异。*差异*可以是许多不同的值，如天数、月数、甚至小时数(以及更多)。

## 日期差异()

语法 PHP `date_diff()`

```
date_diff($origin_date, $target_date)
```

我将创建 2 个日期，然后在从`date_diff()`函数调用得到的对象上调用`format()`方法:

浏览器中出现的`a`格式返回:

```
+21 days
```

使用`m`格式，我们可以知道日期对象之间的月数差异:

(注:`date_diff()`在*程序样式*中。在[官方 date_diff()文档](https://www.php.net/manual/en/datetime.diff.php)中阅读更多信息。)

## DATEDIFF()

MySQL 的`DATEDIFF()`函数返回两个日期值之间的天数。这个函数对于计算一个事件在过去发生了多少天或者在将来会发生多少天是很方便的(例如，生日、假日、周年纪念日等等)。

语法 MySQL `DATEDIFF()`:

```
DATEDIFF(date_1, date_2)
```

在写这篇文章的时候，MySQL 的`CURRENT_DATE()`函数在我的本地学习/开发环境中返回这个值:

圣诞节是在 12 月 25 日，我们可以使用`DATEDIFF()`计算离假期还有多少天:

希望你们今年过得好，圣诞老人会来看你们！

(关于 DATEDIFF() 的更多[信息)。)](https://www.w3schools.com/sql/func_mysql_datediff.asp)

你可以在我的 [***小费罐***](https://digitalowlsprose.ck.page/products/appreciation-support) 里扔出你所有的零钱来支持我的内容和我的博客。我真的很感谢你的支持！

# PHP: date() | MySQL: NOW()

如果函数调用中没有提供`$timestamp`参数值，PHP 的`date()`函数返回当前时间，根据`$format_string`参数格式化。

## 日期()

语法 PHP `date()`:

```
date(string $format_string, $timestamp = null)
```

在写这篇文章的时候，`date()`函数在我的本地开发环境中返回了这个日期:

正如其他几个 PHP 日期函数示例一样，`date()`有许多格式化选项。

(访问 [PHP date()函数文档](https://www.php.net/manual/en/function.date.php)以获取关于该函数的更多信息。)

## 现在()

MySQL `NOW()`函数返回当前日期和时间。`NOW()` 不接受任何论据。

语法 MySQL `NOW()`:

```
NOW()
```

让我们在我的本地开发环境中看一个`NOW()`的例子:

`NOW()`功能易于使用，并提供了一个方便的日期/时间值，您可以使用它来计算基于油井的信息，现在*为*。

(了解更多关于 [NOW()函数在这里](https://www.w3schools.com/sql/func_mysql_now.asp)。)

# PHP: date_add() | MySQL: DATE_ADD()

如果您有一个日期值，您想添加另一个*单位*(日、月、年等)，PHP 的`date_add()`和 MySQL 的`DATE_ADD()`函数都返回这种类型的计算。

## 日期添加()

语法 PHP `date_add()`:

```
date_add($date_object, $interval_amount)
```

(注:`date_add()`是在程序式。)

有几个[日期间隔](https://www.php.net/manual/en/class.dateinterval.php)可以作为第二个参数传递给`date_add()`。在本例中，我在[date _ interval _ create _ from _ _ date _ string()](https://www.php.net/manual/en/function.date-interval-create-from-date-string.php)函数中指定“21 天”(这是`date_add()`的第二个参数):

退货:

```
2021-12-25
```

月份是完全有效的日期间隔，在下面的示例中，我们计算未来 3 个月的日期:

在浏览器中，我们看到:

```
March 4th, 2022
```

(更多信息参见在线 [date_add()文档](https://www.php.net/manual/en/datetime.add.php)。)

## 日期添加()

语法 MySQL `DATE_ADD()`:

```
DATE_ADD(date_value, INTERVAL value add_unit_value)
```

`DATE_ADD()`接受日期或时间`INTERVAL`值，将该`INTERVAL`值与指定日期相加，并根据计算结果返回一个日期。

使用`DATE_ADD()`将`6 DAY INTERVAL`添加到日期‘2021–12–25’会返回日期‘2021–12–31’(除夕):

`INTERVAL`值必须是*单数*。复数形式的*返回一个错误:*

您也可以添加整月作为`INTERVAL`值:

(通过访问本网站了解更多关于 [DATE_ADD()的信息。)](https://www.w3schools.com/sql/func_mysql_date_add.asp)

**促销**:我正在[我的 Etsy 商店](https://www.etsy.com/shop/digitalowlsprose/)制作 Gmail HTML 电子邮件签名模板。让你的电子邮件*脱颖而出*和*流行*你自己的。

# PHP: date_sub() | MySQL: DATE_SUB()

就计算而言，使用 PHP 的`date_sub()`函数，您可以获得与`date_add()`相同的功能。除了使用`date_sub()`，您正在减去日期间隔。

## date_sub()

语法 PHP `date_sub()`:

`date_format()`在`$some_date`对象上的回声现在是:

```
September 4th, 2021
```

如果我们用`date_sub()`减去 3 天，我们得到:

## DATE_SUB()

语法 MySQL `DATE_SUB()`:

```
DATE_SUB(date_value, INTERVAL value subtract_unit_value)
```

我们可以使用`DATE_ADD()`将`INTERVAL`加到日期/时间值上，并返回一个计算出的日期。同理，我们可以用`DATE_SUB()`减去一个`INTERVAL`。正如`DATE_ADD()`一样，指定的`INTERVAL`单位必须是单数。

同样，我不会涵盖每一个可用的`INTERVAL`单位。更多信息请访问文章中的任何链接。

例如，`INTERVAL`值“2 个月”返回比目标日期参数早 2 个月的日期:

([了解更多关于 DATE_SUB()](https://www.w3schools.com/sql/func_mysql_date_sub.asp) 和许多可以在函数调用中使用的`INTERVAL`值的信息。)

你是[媒体](http://medium.com/)成员吗？如果是这样的话，[每次我发表博客文章的时候都会收到一封电子邮件通知](https://parabollus.medium.com/subscribe)如果你更喜欢中型平台的话。不是会员？别担心！使用[我的注册链接](https://parabollus.medium.com/membership)(我将获得佣金，无需额外费用)并加入。我真的很喜欢阅读所有伟大的内容，我知道你也会！！！

我希望你已经学到了一些关于在 PHP 和 MYSQL 中使用日期值的新知识，因为我知道我已经学到了。如果您有任何问题或看到代码中的任何错误，请在下面留下评论。建设性的意见有助于我更好地提供准确的信息，非常感谢。🙏

喜欢你读过的？看到什么不正确的吗？请在下面评论，感谢阅读！！！

# 行动的号召！

感谢你花时间阅读这篇文章。我真心希望你发现了一些有趣和有启发性的东西。请在这里与你认识的其他人分享你的发现，他们也会从中获得同样的价值。

访问 [Portfolio-Projects 页面](https://wp.me/P28ctb-3KD)查看我为客户完成的博客帖子/技术写作。

[**我是大咖！帮我准备一个新杯子！**](https://ko-fi.com/joshlovescoffee)

要在最新的博客文章发表时收到来自本博客(“数字猫头鹰散文”)的电子邮件通知(绝不是垃圾邮件)，请点击“点击订阅！”按钮在首页的侧边栏！(如有任何问题，请随时查看 [Digital Owl 的散文隐私政策页面](https://wp.me/P28ctb-3gI):电子邮件更新、选择加入、选择退出、联系方式等……)

请务必访问[“最佳”](https://joshuaotwell.com/where-blog_post-in-digital-owls-prose-best-of/)页面，收集我的最佳博文。

作为一名 SQL 开发人员和博客作者，Josh Otwell 热衷于学习和成长。其他最喜欢的活动是让他埋头于一本好书、一篇文章或 Linux 命令行。其中，他喜欢桌面 RPG 游戏，阅读奇幻小说，并与妻子和两个女儿共度时光。

免责声明:本文中的例子是关于如何实现类似结果的假设。它们不是最好的解决方案。所提供的大多数(如果不是全部)示例都是在个人发展/学习工作站环境中执行的，不应被视为生产质量或就绪。您的特定目标和需求可能会有所不同。使用那些最有利于你的需求和目标的实践。观点是我自己的。

***OpenLamp.tech*** ，面向 PHP 和 MySQL 开发者的简讯。[加入社区](http://openlamptech.substack.com)。学习和成长…

*有何贵干？*

*   *你想开一个博客吗？我用 WordPress 写博客。让我们都在提供的计划上省钱。💸*
*   *从[我的 Etsy 商店](https://www.etsy.com/shop/digitalowlsprose/)获取一个 Gmail HTML 电子邮件签名模板，让您的电子邮件显得与众不同。✉️*
*   *需要托管你的下一个网络应用程序或 WordPress 网站吗？我使用并强烈推荐 [Hostinger](https://www.hostg.xyz/aff_c?offer_id=6&aff_id=94641) 。他们有很好的价格和服务。*
*   *我喜欢每天早上在你的收件箱里阅读 Refind: *网络的精髓。免费订阅*。通过我的推荐链接为你自己[注册，帮助我获得高级订阅。](https://refind.com/joshua-otwell?invite=5440c95e39)*
*   *获取您的[免费移动“创造者”壁纸包](https://click.convertkit-mail4.com/d0uvkov9k4s0h22640am/p8hehqu9xxnm2zbr/aHR0cHM6Ly9zcGFya2xwLmNvL2pvc2h1YWMwM2U2Mw==)。*

****披露*** :本帖部分服务和产品链接为附属链接。在没有额外费用给你，你应该通过点击其中一个购买，我会收到佣金。*

**原载于 2021 年 12 月 29 日*[*【https://joshuaotwell.com】*](https://joshuaotwell.com/5-php-date-functions-and-their-mysql-equivalents/)*。**