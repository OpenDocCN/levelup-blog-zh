# Flutter 中的服务器端分页数据表

> 原文：<https://levelup.gitconnected.com/server-side-paginated-data-table-in-flutter-d01b16f30f3>

![](img/188e54a69d2943052f459671005b40e0.png)

米卡·鲍梅斯特在 [Unsplash](https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Flutter 非常棒，它为移动开发者和任何对用单一代码库完成项目感兴趣的人提供了广泛的可能性。但是默认控件集合中缺少一些控件和公共工作流。这篇文章是关于大型数据集的数据表以及如何在所有的 Flutter 平台上正确处理它。

# 前言—高级数据表

我这里说的控件是我自己做的，可以在我的 GitHub 账号上找到。我意识到我在做一个需要显示大量数据的项目时需要它们。
我确实重用了直接来自颤振控件的代码，你会在这种情况下的代码中找到相关注释。该软件包也可以通过 pub.dev 获得:

[](https://pub.dev/packages/advanced_datatable) [## 高级 _ 数据表| Flutter 包

### 高级数据表使用 Flutter PaginatedDataTable 小部件，并向其添加了一些功能。不要添加…

公共开发](https://pub.dev/packages/advanced_datatable) 

顺便说一下，除了 pub.dev 页面之外，还有其他使用包的方法，如这里描述的。

如果您想观看现场演示，请使用下面的链接。请记住，魔术是在后台发生的。使用浏览器和网络监视器的开发工具，您将看到您的客户端只获得当前页面的数据。

 [## 示例 _ 高级 _ 数据表

### 一个新的颤振项目。

dev-猫头鹰. github.io](https://dev-owl.github.io/advanced_datatable/#/) 

# 服务器端分页数据表

这是一个标题…

![](img/0406f2d551aac87ca42fc07c3c6f113a.png)

由 [Alexander Sinn](https://unsplash.com/@swimstaralex?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

简单一点说:Flutter 已经提供了一个数据表和分页表。这些小部件都不支持这样的用例，即您的服务器/远程端进行分页，并且只向您的客户机发送当前页面。这样做的原因很简单:减少内存消耗，处理慢速连接。
得益于 **advanced_datatable** ，您现在可以在服务器上处理庞大的数据集，只需将一个页面所需的数据渲染到您的应用程序或网站。基本概念和功能与[分页数据表](https://api.flutter.dev/flutter/material/PaginatedDataTable-class.html)相同。

# 如何使用它

让我们从小部件本身开始，我建议将它添加到一个可滚动的父控件中。一个表可以增长并需要空间，最简单的设置是:

您需要定义想要显示的列和数据源。这些列使用了现有的来自 flutter 的 *DataColumn* 类，请在这里找到文档[。](https://api.flutter.dev/flutter/material/DataColumn-class.html)

## 您的数据源

数据源是连接您的表和远程后端的关键元素(顺便说一下，数据也可以从文件或数据库中读取，它不一定是服务器)。
你需要扩展*AdvancedDataTableSource<T>*类，并为你的数据指定一个类型。在[实时示例](https://dev-owl.github.io/advanced_datatable/)中，该类被称为 *CompanyContact* 并存储从我的数据源返回的数据(事实上，示例中用作后端的示例 web 服务器是用纯 dart 编写的，[在这里查看代码](https://github.com/Dev-Owl/advanced_datatable/blob/main/example/webserver/bin/serveTestData.dart))。完整的数据源如下所示。

必须实现以下功能:

*   getRow
*   获取下一页
*   *selectRowCount* (不是函数，还需要到位)

所有的函数都会在需要的时候被 advanced_datatable 调用，你只需要做到以下几点:

1.  在 *getNextPage* 中，您必须将您的数据作为*RemoteDataSourceDetails*对象提供。您需要设置当前页面的总行数和行数据。Advanced_datatable 将为您提供加载数据的所有信息(页面大小、偏移量、排序细节)。
2.  *getRow* 将请求一个[*DataRow*](https://api.flutter.dev/flutter/material/DataRow-class.html)*通过传递一个索引给你。您可以确保索引存在于当前加载的页面中。使用 *lastDetails 可以访问最后加载的细节。*在上面的例子中，模型类( *CompanyContact* )实现了一个[函数来返回 *DataRow*](https://github.com/Dev-Owl/advanced_datatable/blob/main/example/lib/company_contact.dart#L18)*

*这就是你在 flutter 中拥有一个远程端分页的*异步*数据表所需要做的一切，简而言之:*

1.  *创建行模型*
2.  *定义列列表*
3.  *用 getRow 和 *getNextPage* 实现 *DataSource* 类*

# *一些简洁的额外内容*

*![](img/6df1f5b77b20040a1a0d25be55c23fe0.png)*

*迈克尔·泽兹奇在 [Unsplash](https://unsplash.com/s/photos/excel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片*

## *空行*

*原始数据表不断用空行填充页面，以确保您的页面上始终有固定数量的行(即使没有足够的数据)。要禁用此默认行为，只需设置:*

```
*addEmptyRows: false*
```

## *装货*

*在加载数据时，您可能希望以自己的方式通知用户。将回调传递给以下参数:*

```
*loadingWidget: () => Text('Loading...')*
```

*如果不设置回调，advanced_datatable 会显示进度指示器。*

## *错误状态*

*您的数据可能是通过互联网下载的，这可能会因为各种各样的原因而失败。像加载小部件一样，您可以通过设置以下参数向用户传达此状态:*

```
*errorWidget: () => Text('Error unable to load data...')*
```

*如果没有设置上述内容，并且出现错误，advanced_datatable 将呈现一个包含(如果可能的话)错误信息的文本。*

# *结论*

*随着 Flutter 在越来越多的平台上可用，允许在更大的数据集上工作的灵活小部件的需求将变得越来越重要。我在这里创建的东西相当简单，我只是刚刚开始，任何反馈或你错过的功能都是非常欢迎的[只要在 GitHub](https://github.com/Dev-Owl/advanced_datatable/issues/new) 上开一张票。*