# 使用 CodeIgniter 4 表单助手启用文件附件

> 原文：<https://levelup.gitconnected.com/enable-file-attachment-with-codeigniter-4-form-helper-9ad6e368e103>

CodeIgniter 4 PHP 框架有许多内置的助手库。其中一个我经常使用的是[表单助手](https://codeigniter.com/user_guide/helpers/form_helper.html)。在这篇文章中，我将介绍使用特定的表单助手库方法上传文件附件…

![](img/35835103edbe3417b00fc224ef056743.png)

自我推销:

如果你喜欢这里写的内容，尽一切办法，把这个博客和你最喜欢的帖子分享给其他可能从中受益或喜欢它的人。[因为咖啡是我最喜欢的饮料，如果你愿意，你甚至可以给我买一杯！](http://ko-fi.com/joshlovescoffee)

通常在 HTML 表单中，为了上传文件附件，您需要在 **<表单>** 标签中包含 **enctype** 属性:

```
enctype="multipart/form-data"
```

因此，在 *raw* HTML 中，PHP 项目中的代码如下所示:

```
<form action="some_file.php" method="post" enctype="multipart/form-data">
```

对于实际的文件附件字段，我们使用 **<输入>** 元素与文件类型:

```
<input type="file" name="my_file">
```

当然，这并不是太多的 HTML 代码。

但是，如果您在 CodeIgniter 4 中构建项目，为什么不利用“表单”帮助程序库的一些内置功能呢？

事实上，你只需要三种方法:

```
<?=form_open_multipart('some_file.php');?>
<?=form_upload('my_file');?>
<?=form_close();?>
```

我每周写一封关于我正在研究、学习和感兴趣的 SQL/PHP 的电子邮件。如果这听起来像是你想参与的事情，[点击这里](https://digitalowlsprose.ck.page/1b35a06295)了解更多信息。谢谢大家！

## CodeIgniter 4 表单助手:form_open_multipart()

**form_open_multipart()** 方法使用以下语法接受最多 3 个参数:

```
form_open_multipart(action, attributes, hidden)
```

*   操作-操作目标 URI 字符串。
*   属性 HTML 属性的数组或字符串。
*   隐藏—隐藏字段的数组。

## CodeIgniter 4 表单助手:form_upload()

**form_upload()** 方法使用以下语法接受最多 3 个参数:

```
form_upload(field, value, extra)
```

*   数据-字段属性数据
*   值-字段值
*   额外-额外属性

(**注意:**我通常使用一个[数组](https://www.php.net/manual/en/language.types.array.php)，将我需要的所有属性数据作为单个参数。)

## CodeIgniter 4 表单助手:文件上传结构示例

下面是一个*学习示例，在我的本地 Linux [XAMPP](https://www.apachefriends.org/index.html) 开发环境下，如何使用 CodeIgniter 4 form helper 在 HTML 表单中包含文件附件上传。*

在默认的 Home 控制器中，我创建了一个 **forms()** 方法，该方法返回视图文件 **form_view.php** :

**相关**:您可以通过使用[全局助手()函数](https://codeigniter.com/user_guide/general/common_functions.html#helper)来加载表单助手(或 CodeIgniter 4 中的任何其他助手)。

在 **form_view.php** 视图文件中，我们使用以下表单帮助器方法对表单进行了编码:

*   **form_open_multipart()**
*   **form_label()** —可选
*   **表单 _ 上传()**
*   **form_close()**

**注意**:在 **form_open_multipart()** 方法中用作参数的 **base_url()** 方法是“url”帮助器库的一部分，不在本文讨论范围内。关于“url”帮助器的更多信息，请访问在线文档。

**form_view.php** 文件中的表单帮助器方法在浏览器中呈现这个 HTML:

这产生了这个简单(且粗糙)的基本表单，包括文件附件字段:

![](img/8050a1a9b464de37a0f584aeb98f83f6.png)

你是[中](http://medium.com/)成员吗？如果是这样的话，[每次我发表博客文章的时候都会收到一封电子邮件通知](https://parabollus.medium.com/subscribe)如果你更喜欢中型平台的话。不是会员？别担心！使用[我的注册链接](https://parabollus.medium.com/membership)(我会向你收取佣金，无需额外费用)并加入。我真的很喜欢阅读所有伟大的内容，我知道你也会！！！

我已经在 CodeIgniter 4 项目中大量使用了 form helper 库，并且发现它非常有用和方便。如果你以前没用过，就试试吧。

一如既往，如果你有任何问题或看到代码中的任何错误，请通过评论让我知道。建设性的意见有助于我提供准确的博客帖子，我非常感激。感谢您的阅读！

## CodeIgniter 4 类似读数

我在下面列出了我在 CodeIgniter 4 上写的几篇帖子。你可以随意参观任何你感兴趣的地方。也请与他人分享！

*   [使用 MySQL 的 CodeIgniter 4 CRUD 系列:创建](/codeigniter-4-crud-series-with-mysql-create-f2533edbd5e8)
*   [使用 CodeIgniter 的查询构建器进行 MySQL 聚合查询](https://joshuaotwell.com/mysql-aggregate-query-using-codeigniters-query-builder/)
*   [带 MySQL 的 CodeIgniter 4 CRUD 系列:阅读](/codeigniter-4-crud-series-with-mysql-read-96c994e33e4e)
*   [带 MySQL 的 CodeIgniter 4 CRUD 系列:更新](/codeigniter-4-crud-series-with-mysql-update-876a6d20335a)
*   [带 MySQL 的 CodeIgniter 4 CRUD 系列:删除](/codeigniter-4-crud-series-with-mysql-delete-97d81274ea80)

喜欢你读过的？看到什么不正确的吗？请在下面评论，感谢阅读！！！

# 行动的号召！

感谢你花时间阅读这篇文章。我真心希望你发现了一些有趣和有启发性的东西。请在这里与你认识的其他人分享你的发现，他们也会从中获得同样的价值。

访问[投资组合-项目页面](https://wp.me/P28ctb-3KD)，查看我为客户完成的博客帖子/技术写作。

[**咖啡是我最喜欢的饮料！**](https://ko-fi.com/joshlovescoffee)

要在最新的博客文章发表时收到来自本博客(“数字猫头鹰散文”)的电子邮件通知(绝不是垃圾邮件)，请点击“点击订阅！”按钮在首页的侧边栏！(如有任何问题，请随时查看 [Digital Owl 的散文隐私政策页面](https://wp.me/P28ctb-3gI):电子邮件更新、选择加入、选择退出、联系表格等……)

请务必访问[“最佳”](https://joshuaotwell.com/where-blog_post-in-digital-owls-prose-best-of/)页面，收集我的最佳博文。

[Josh Otwell](https://joshuaotwell.com/about/) 作为一名 SQL 开发人员和博客作者，他热衷于学习和成长。其他最喜欢的活动是让他埋头于一本好书、一篇文章或 Linux 命令行。其中，他喜欢桌面 RPG 游戏，阅读奇幻小说，并与妻子和两个女儿共度时光。

免责声明:本文中的例子是关于如何实现类似结果的假设。它们不是最好的解决方案。所提供的大多数(如果不是全部)示例都是在个人发展/学习工作站环境中执行的，不应被视为生产质量或就绪。您的特定目标和需求可能会有所不同。使用那些最有利于你的需求和目标的实践。观点是我自己的。

*原载于 2021 年 10 月 27 日*[*https://joshuaotwell.com*](https://joshuaotwell.com/enable-file-attachment-with-codeigniter-4-form-helper/)T22。