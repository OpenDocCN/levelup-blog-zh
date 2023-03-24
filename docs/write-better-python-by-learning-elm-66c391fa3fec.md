# 通过学习 Elm 写出更好的 Python

> 原文：<https://levelup.gitconnected.com/write-better-python-by-learning-elm-66c391fa3fec>

![](img/0e39cc502a3377cda4190ce13c86429c.png)

最近，我用 [Elm](https://elm-lang.org/) 构建了一个小型的辅助项目，这是一种用于前端的函数式编程语言，以其极端的健壮性而闻名。回到 Python，我注意到我对这门语言的看法已经发生了变化。

在本文中，我重点介绍了 Python 中一些常见的反模式，并提出了一些替代方案，使您的代码不容易出错。

# 什么是 Elm，我为什么要关心？

那么关于 Elm 的炒作到底是怎么回事呢？Elm 是一种用于前端的函数式编程语言，具有出色的性能和一些独特的特性:

## 没有运行时异常

最重要的是，它以没有运行时异常而自豪。让这件事过去一会儿。尤其是在使用像 Python 这样的动态类型语言处理大型应用程序时，您(和您的用户)多久一次？)看到类似`TypeError: argument of type 'NoneType' is not iterable`或`KeyError: 'code'`的隐晦错误信息？

运行时异常是在运行时引发的异常，这意味着编译器在运行代码之前无法捕捉它们。即使有很好的测试覆盖率，一些问题也会从缝隙中溜走，只有当有人使用你的应用程序时才会被发现。此时修复它们是一个漫长而乏味的过程，因为您需要经历一个完整的

1.  重现 bug
2.  潜在地编写测试用例
3.  实际的代码更改
4.  代码审查
5.  合并
6.  部署
7.  以及又一轮点击/用户测试。

相反，如果我们的编译器能够在编译时捕获这些错误，它们就永远不会出现在我们的主分支中，我们将节省大量的时间。没有运行时异常的承诺是巨大的！

## 编译器驱动的开发

还有更多有趣的特性，比如友好的指导性错误消息，它不仅会告诉你编译器哪里出错了，还会轻轻地把你推向解决问题的正确方向。Elm 社区通常将此称为编译器驱动的开发:您更改一些代码，尝试编译，然后迭代地解决问题，直到编译器满意为止。一旦达到这种状态，您的代码就会运行。

在我看来，这就是榆树的伟大之处。一旦编译器对你的程序感到满意，你就可以对它相当有信心了。与 Python 相比，在处理大型要素时。即使有很高的测试覆盖率和同行评审，当在试运行的第一次用户测试中出现问题时，您也不会感到太惊讶。

# 外卖

作为一名皮达主义者，学习榆树对我来说是一次屈辱的经历。在深入研究具体的反模式之前，我想提一下两个关键的见解:

## 社区

首先，我现在更加欣赏 Python 这个庞大而成熟的社区。当你遇到问题时，你可以谷歌一下，99%的情况下，你会找到一个人的帖子，他有同样的问题和多个很好的解决方案。在 Elm 这样的新兴语言中，情况并不总是这样。你将需要搜索更长的时间，关于最佳实践的智能博客文章越来越少，语言本身仍在经历许多突破性的变化，这意味着一年前有效的解决方案现在可能已经过时。

## 灵活性是有代价的

其次，我对我为 Python 的灵活性所付出的代价有了更好的认识。动态类型既可以是礼物，也可以是负担。当你只是想抽出一些代码来让原型工作时，隐式是很好的，但是特别是当构建更大的应用程序时，它会带来很多麻烦。

# 反模式和替代方案

在本节中，我们将继续不断地重构一些 Python 代码，以提高其健壮性和可理解性。

## 脆弱代码

让我们看看下面的`is_paid`函数，它使用一个字典并检查是否设置了`"paid_date"`值:

```
def is_paid(invoice):
    return invoice["paid_date"] is not None
```

你能看出这个功能最大的问题吗？没错。你不能保证`"paid_date"`在字典里有。调用这个函数可能会导致一个`KeyError: 'paid_date'`。这段代码相当脆弱，你需要在重构时密切关注。

更安全的方法是将其重写为

```
def is_paid(invoice):
    return invoice.get("paid_date") is not None
```

我不会在这里讨论更多的细节，但是如果你感兴趣的话，一定要查看一下 [returns](https://github.com/dry-python/returns) 。这个库通过向 Python 引入一个类似 Elm 的`Maybe`容器，为这类问题提供了一个非常优雅的解决方案。

## 打字模块

我们再来看看`is_paid`:你怎么知道`invoice`是什么？一点都不明显。它可能是一本字典。它也可以是实现`.get`方法的自定义类型。您只能通过研究调用该函数的代码库的其他部分来获得答案。

更具体的一个好方法是 Python 的`typing`模块。我们通常使用一种混合的方法，在这种方法中，我们不一定要输入整个代码库，而是关注关键的、复杂的或不明显的部分。它将记录你的代码并节省大量时间，特别是当新的开发人员加入你的团队时。

一个改进的版本是:

```
from typing import Dictdef is_paid(invoice: Dict[str, Any]) -> bool:
    return invoice.get("paid_date") is not None
```

现在很明显，invoice 被期望成为一个字典，并且`.get`的行为变得很明显。

如果您使用像 [PyCharm](https://www.jetbrains.com/de-de/pycharm/) 这样的 IDE，这将为您带来[额外的代码检查和警告](https://www.jetbrains.com/help/pycharm/type-hinting-in-product.html#validate-type-hints)的好处。

当你想学习更多关于打字的知识时，我推荐阅读 realpython.com 关于这个主题的伟大指南。

## 自定义类型

知道发票是一本字典更好，但是我们仍然对它的内容一无所知。`invoice`应该有哪些键？`"paid_date"`键是否总是可用？我们对发票的内容缺乏明确的定义。这需要一个自定义类型！

尤其是因为 Python 的类语法可能相当笨拙，所以很容易陷入反模式，即没有定义足够多的自定义类型，而是过于依赖字典或元组之类的原生类型。

为了简单起见，让我们的发票由三个属性组成:`amount`、`paid_date`和`status`。当把它定义为一个常规类时，你会得到这样的结果:

```
class Invoice:
    def __init__(self, amount, paid_date, status):
        self.amount = amount
        self.paid_date = paid_date
        self.status = status
```

嗯，这是相当多的杂乱和重复…因此非常不和谐。幸运的是，有更简洁的定义方法。这里有一个使用`dataclasses`的例子:

```
from dataclasses import dataclass
from datetime import datetime
from typing import Optional@dataclass
class Invoice:
    id: int
    amount: float
    paid_date: Optional[datetime]
    status: str
```

利用这一点，我们可以重构代码，只接受新的自定义类型:

```
def is_paid(invoice: Invoice) -> bool:
    return invoice.paid_date is not None
```

***注意:*** *在这一点上，你也可以仅仅让它成为* `*Invoice*` *的一个属性，但是我把这个练习留给有野心的读者；)*

请注意`dataclasses`只有 Python 3.7 以后才有。好的替代品是`[attr](https://github.com/python-attrs/attrs)`[项目](https://github.com/python-attrs/attrs)或[命名的双](https://docs.python.org/3/library/collections.html#collections.namedtuple)项目，尽管它们在其他特性上也有所不同。

如果你想深入这个主题，我推荐 [Raymond Hettinger 关于 PyCon2018 的数据类的演讲](https://www.youtube.com/watch?v=T-TwcmT6Rcw)和 [realpython.com 的“终极指南”](https://realpython.com/python-data-classes/)。

## 可变性:

Python 这样的语言的另一个问题是可变性。列表和字典是可变的，这会在你的代码中引起令人惊讶的错误。对于函数或方法中的[可变默认参数](https://docs.quantifiedcode.com/python-anti-patterns/correctness/mutable_default_value_as_argument.html)，您很可能至少遇到过一次这种情况。我不会在这里深入讨论细节，但至少我想提一下，`dataclasses`和`attr`都支持一个可选的`frozen`关键字来使你的类([几乎是](https://docs.python.org/3/library/dataclasses.html#frozen-instances))不可变。

## 枚举:

在大多数应用程序中，您会发现枚举值的用例，例如，一个属性应该只有有限的一组可能值。我们的`Invoice.status`就是一个很好的例子。当然，这个字段只允许一小组值，例如`"Draft"`、`"Sent"`、`"Paid"`和`"Cancelled"`。在我们当前的实现中，这些只是字符串。虽然这种方法有效，但它对输入错误的抵抗力不是很强。

让我们考虑下面的例子:

```
def is_open(invoice: Invoice) -> bool:
    return invoice.status == "Send"
```

你看出问题了吗？我们输入了`"Send"`而不是`"Sent"`。在最坏的情况下，这可能会导致过期发票被标记为已支付，您的公司会损失相当多的钱。吓人！当您使用 enum 时，您可以更好地防范这些错误:

```
...
from enums import Enumclass InvoiceStatus(Enum):
    DRAFT = "Draft"
    SENT = "Sent"
    PAID = "Paid"
    CANCELLED = "Cancelled"@dataclass
class Invoice:
    ...
    status: InvoiceStatus
```

现在我们的`is_open`函数变成了

```
def is_open(invoice: Invoice) -> bool:
    return invoice.status === InvoiceStatus.SEND
```

这将在调用该函数时引发一个`AttributeError: SEND`。因此，即使您只有一个调用该功能的测试用例，您也一定会在部署之前发现这个 bug。

## 模糊类型

最后但同样重要的是，我想强调使用模糊参数或返回类型的问题。它们将不可避免地增加代码的复杂性，如果您在处理它们时不太注意的话，还会导致许多错误。

以下面的例子为例，我们添加了一个新的`InvoiceMeta`类型，它携带了一些发票的元数据(这是一个玩具示例，但我希望您能明白这一点):

```
def get_meta(invoice, metas):
    for meta in metas:
        if invoice.id == meta.id:
            return metameta = get_meta(invoice, metas)
print(meta.recipient)
```

这里有什么问题？代码隐含地假设所有的发票都有一个相应的元实体。但是如果这个假设不成立，我们就会遇到一个例外:

```
AttributeError: 'NoneType' object has no attribute 'recipient'
```

啪！好的，如果我们完全使用类型注释，我们会发现这一点，因为返回类型必须是`Optional[InvoiceMeta]`。但即使这样，你也会发现自己在编写更复杂的代码，因为你总是需要检查 meta 是`InvoiceMeta`还是`None`:

```
meta = get_meta(invoice, metas)
if meta is None:
    print("Recipient unknown as no InvoiceMeta was recorded.")
else:
    print(meta.recipient)
```

正如在 [Python 反模式](https://docs.quantifiedcode.com/python-anti-patterns/maintainability/returning_more_than_one_variable_type_from_function_call.html)中所建议的，更好的方法是直接在`get_meta`中引发异常，然后在代码的不同层处理错误。

```
def get_meta(invoice, metas):
    for meta in metas:
        if invoice.id == meta.id:
            return meta
    raise LookupError(f"No InvoiceMeta available for {invoice.id}")
```

# 结论

尽管我没有过多地谈论它，但潜入 Elm 是一次很棒的经历和许多乐趣！希望以后能多找时间陪它玩。同时，我很感激我在软件开发中获得的新观点。

我希望这些模式能帮助您编写更健壮的代码。试着在代码评审中找出它们，并与你的队友分享。