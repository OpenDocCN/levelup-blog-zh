# 漂亮的奥多姆方法

> 原文：<https://levelup.gitconnected.com/nifty-odoo-orm-methods-324152490697>

## 他们肯定会节省你一些击键和时间

![](img/c8cd8da3046b9da6924e1c7ff351a72d.png)

来自 [Pexels](https://www.pexels.com/photo/man-woman-laptop-office-6804595/) 的 cottonbro 拍摄的照片

Odoo [表单](https://www.odoo.com/documentation/16.0/developer/reference/backend/orm.html)是[活动记录模式](https://www.martinfowler.com/eaaCatalog/activeRecord.html)的实现。该模式简化了数据访问和持久性，使开发人员能够用有限的 SQL 专业知识与数据库进行交互。

ORM 处理数据水合和脱水的复杂工作。简而言之，它将数据库中的数据抽象出来并自动加载到内存对象中，并将对这些对象所做的修改持久化回数据库。

Odoo 开发者文档相当广泛地涵盖了 [ORM API](https://www.odoo.com/documentation/16.0/developer/reference/backend/orm.html) 。它几乎包含了开发人员可以在日常开发中利用的相关 API 方法。

有一些由 ORM API 实现的漂亮方法没有被写入 ORM [文档](https://www.odoo.com/documentation/16.0/developer/reference/backend/orm.html)。

了解这些方法可以节省您的按键次数和时间，并简化您作为 Odoo 开发人员的生活。

# 1.正在获取基本 URL

要动态确定 Odoo 实例的基本 URL，您可以手动引用系统参数`web.base_url`并像这样检索它的值

Odoo 实例的基本 URL 的详细确定

这种方法非常简单，因为它只有一条语句。

但是甚至有一种更简单、更紧密的方法来确定这一点，使用 ORM API 上可用的可重用方法。

## 使用 ORM API 中的方法

使用 ORM API 上可用的`get_base_url()`方法。

一个利用 Odoo ORM 内置方法的简洁版本

# 2.定义窗口操作

在 Odoo 中，窗口动作充当视图的容器。窗口操作可以包含多个视图，包括树、表单、看板等。

通常，窗口操作是用 XML 定义的。并由绑定菜单触发。但是有时一个模块的工作流和需求要求一个窗口动作由一个按钮而不是一个菜单来触发。

在这种情况下，开发人员有两种选择来定义窗口操作。

1.  在模型的方法中完全定义窗口操作，然后将方法绑定到 XML 定义的按钮来触发操作。
2.  用 XML 定义窗口操作，并在模型的方法中引用已定义操作的标识符，然后将该方法绑定到 XML 定义的按钮以触发该操作。

定义窗口动作的两种方法看起来都是这样的

完全在模型中定义的窗口操作。不需要 XML 定义。

XML 中定义的窗口操作被其在模型中的标识符引用

很明显，引用 XML 中已定义动作的窗口动作的第二个定义与第一个相比显得更简洁。

## 使用 ORM API 中的方法

虽然第二个定义很简洁，但有一种更好的方法，它不需要引用窗口动作的标识符，就像代码中用`self.env.ref(...)`做的那样。

第三个选项使用 ORM 方法`get_formview_action()`。

首先在 XML 中定义的窗口操作，后来使用 Odoo ORM 方法 get_formview_action()引用

# 3.检查用户对模型的访问权限

用户对模型的操作在 Odoo 模块的`security` 目录下的`ir.model.access.csv`文件中定义和管理。

模型访问文件定义了用户可以在模型上执行的 CRUD 操作。每当用户在没有`ir.model.access.csv`文件中定义的正确访问权限的情况下执行操作时，就会抛出一个`AccessError`错误。

开发人员通常将这种检查留给 Odoo，Odoo 会抛出一个默认的`AccessError` 错误消息。此消息是通用的。为了自定义此消息，并向用户提供更友好和特定于上下文的反馈，开发人员通常将他们的操作包装在一个 try-catch 块中，以优雅地捕捉并引发更友好的验证错误。

这就是我在代码中的意思

使用 try-catch 错误预测并妥善处理访问错误的 create 方法的重写

在上面的代码中，我们看到 try-catch 块用于保护创建操作。它捕捉默认的 Odoo `AccessError`，然后编写一个更友好的消息。

使用 try-catch 定制 Odoo 默认访问错误消息是不必要的，因为它会使 create 方法变得冗长。这是真的，即使我们上面的 create 方法里面几乎没有任何东西。

## 使用 ORM API 中的方法

现在，使用简单的方法`check_access_rights(...)`我们可以实现相同的目标，而不需要不必要的 try-catch 块，这会降低代码的清晰度。

调用该方法时有两个参数，首先是用户`operation`，然后是一个布尔值`raise_exception` ，指示当没有访问权限对模型执行指定操作时是否引发异常。对于下面例子中的控件，我们将`raise_exception`参数设置为 false。

现在，一个使用`check_access_rights(...)`方法的示例实现如下所示。

注意，除了操作`create`，还支持`read`、`write`、`unlink`。

# 4.检查用户对记录的访问权限

`check_access_rule(...)`方法的工作方式类似于`check_access_rights(...)`方法，但是基于已定义的记录规则。它根据定义的`ir.rule`验证用户对模型记录的访问。

值得注意的一个区别是，`check_access_rule(...)`方法采用单个 `operation`参数，而如前所述，`check_access_rights(...)`采用两个参数`operation`和`raise_exception`。

# 5.存档记录

当不再需要记录，但不适合出于审计或其他目的删除记录时，记录归档非常有用。

当一个模块的需求包括实现记录归档时，开发人员通常会在必要的模型上引入诸如`active`或`is_archived`的字段，并将值设置为`True`或`False`以响应用户对记录的某些特定操作。

根据选择用来存储记录存档状态的字段，Odoo 提供了支持记录存档操作的特性。

首先，Odoo 自动扩展了树形视图的操作菜单，增加了“存档”和“取消存档”选项，可以轻松地存档和取消存档一条或多条记录。

只有当开发人员在他们的模型上定义了这两个受支持的字段之一时，Odoo 才能识别并更新带有存档和取消存档选项的 Action 菜单。他们是`active`和`x_active`。

在某些情况下，模型的`active`字段可以管理一个状态，而不是一个记录的存档状态。在这种情况下，必须使用`x_active`。

当在模型上定义了`active`和`x_active`字段时，必须在模型上定义一个附加属性，以指示这两个字段中的哪一个用于记录归档。

为此，我们引入值为`x_active`的类属性`_active_name`，因为我们使用`active`字段的目的不同于归档。

既然我们已经用 Odoo 方式实现了记录归档，我们还可以利用 ORM API 设计中的内置方法在不同的上下文中归档和取消归档记录，而不是在树形视图的 Action 菜单中。与手动将归档状态设置为真/假相比，这些方法非常方便。

下面显示了方法`action_archive(...)`、`action_unarchive(...)`和`toggle_active(...)`的使用总结

记录归档和取消归档的一种假设实现

*感谢您的阅读。我希望这篇文章对你有用。如果有，请花一点时间到* [*订阅*](https://medium.com/subscribe/@ofelix03) *到* [*我的个人资料*](https://medium.com/subscribe/@ofelix03) *在我每次发表新文章时接收通知。*

还有一件事，如果你能支持我的写作，我将不胜感激。通过 [*加入媒介*](https://medium.com/membership/@ofelix03) *与我的* [*推荐链接*](https://medium.com/membership/@ofelix03) *，我从你的会员费中获得一小笔佣金，这有助于我投入时间和精力来撰写和发表这些有用的文章。*

[](https://ofelix03.medium.com/membership) [## 通过我的推荐链接加入媒体-费利克斯·奥托

### 阅读费利克斯·奥托(以及媒体上成千上万的其他作家)的每一个故事。您的会员费直接支持…

ofelix03.medium.com](https://ofelix03.medium.com/membership)