# 高级 Python:数据类

> 原文：<https://levelup.gitconnected.com/advanced-python-dataclasses-6a1e53bc4d8d>

让我们看看 Python 3.7 中引入的 Python 数据类。

![](img/c39087c39630340e645006c36730f128.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/python-programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

数据类是 python 模块中的新成员，它将使 Python 开发人员的生活变得更加轻松。尽管这不是一个高级主题，但我们将在本系列中讨论它。这个模块提供了一个装饰器和函数，用于自动将生成的[特殊方法](https://docs.python.org/3.7/glossary.html#term-special-method)如`**__init__()**`和`**__repr__()**`添加到用户定义的类中。最初在 [**PEP 557**](https://www.python.org/dev/peps/pep-0557) 中有描述。

# 到目前为止，这个系列

*   [Python 类、对象和 MRO](https://python.plainenglish.io/advanced-python-classes-objects-and-mro-423bb01521fb)
*   数据类(当前员额)

# 为什么和如何？

现在，对于任何新功能的添加，现有系统中肯定有一些东西需要更多的功能，或者只是不符合标准(在替换的情况下)。所以我们需要知道为什么[python.org](https://www.python.org/)引入了[数据类](https://docs.python.org/3.7/library/dataclasses.html#module-dataclasses)。

## 不使用数据类:

假设我们正在编写一个新的类似 twitter 的应用程序，这个应用程序有一个名为“Tweets”的类，用于跟踪 tweet 的详细信息。让我们看看这个快速且接近生产就绪的类将会是什么。

哦，等等！您的产品经理现在希望您添加在 tweet 帖子中添加评论的功能，您需要引入一个新的类变量！！！！

现在你需要重写几乎整个类。又浪费了 2 个小时。

## 使用数据类:

好的，现在让我们试试`**@dataclass**`。

```
**from dataclasses import dataclass, field
from datetime import datetime
from uuid import UUID, uuid4****@dataclass(frozen=True, order=True)
class Tweets:
    *""" Class to store tweets of users """* tweet_body: str = None
    tweet_time: datetime = datetime.utcnow()
    tweet_id: UUID = uuid4()
    tweet_lang: str = 'en-IN'
    tweet_place: str = 'IN'
    tweet_retweet_count: int = 0
    tweet_hashtags: list[str] = field(default_factory=list)
    tweet_user_id: str = None
    tweet_user_name: str = None**
```

就是这样。我们完了。Python 自己处理了所有的样板代码。对于任何新增加的类变量，我们可以像上面那样声明，然后忘掉它。让我们看看这个类中所有可用的方法。

```
>>> inspect.getmembers(Tweets, predicate=inspect.isfunction) 
[('**__delattr__**', <function Tweets.__delattr__ at 0x0000015EE175A440>), 
('**__eq__**', <function Tweets.__eq__ at 0x0000015EE1759EA0>), ('**__ge__**', <function Tweets.__ge__ at 0x0000015EE175A320>), ('**__gt__**', <function Tweets.__gt__ at 0x0000015EE175A200>), ('**__hash__**', <function Tweets.__hash__ at 0x0000015EE175A4D0>), ('**__init__**', <function Tweets.__init__ at 0x0000015EE1759C60>), ('**__le__**', <function Tweets.__le__ at 0x0000015EE175A0E0>), ('**__lt__**', <function Tweets.__lt__ at 0x0000015EE1759FC0>), ('**__repr__**', <function Tweets.__repr__ at 0x0000015EE1759BD0>), ('**__setattr__**', <function Tweets.__setattr__ at0x0000015EE175A3B0>)]
```

似乎 python 已经创建了`**__setattr__()**`和`**__delattr__()**`，这使得它是不可变的。Python 这样做是因为我们添加了`**frozen=True**`，它添加了`**__le__()**`、`**__lt__()**`、`**__gt__()**`、`**__ge__()**`、**、**，就像我们添加了`**order=True**`、**、**(注意，我们甚至没有在我们的旧风格类声明中实现这四个，因为代码已经相当长了)。

对于默认值，记得在没有默认值的值之后声明有默认值的值。对于可变对象(这里是列表对象)的默认值，您需要使用一个`**default_factory**`。这可以通过使用从`**dataclass**`导入的`**field()**`来声明。这非常重要，因为我们不希望该类的所有实例都使用同一个列表。

如果你不想在你的班级里有一个`**__eq__()**`、`**__init__()**`或`**__hash__()**`就这样做，

```
**@dataclass(init=False, repr=False, eq=Flase, unsafe_hash=False)
class Tweets():**
```

我们可以控制从`**__repr__()**` 和`**__eq__()**` 方法中返回什么。

```
**tweet_retweet_count: int = field(repr=False, compare=False,                    
                                 default=0)**
```

由于我们制作的`**repr=False**`这个字段不包含在生成的`**__repr__()**`方法返回的字符串中，对于`**compare=False**` 它将排除生成的等式和比较方法中的`**tweet_retweet_count**`**(`**__eq__()**`、`**__gt__()**` 、 `**__lt__()**`等)。).**

**对于 post init 处理，我们有`**__post_init__()**` **。****

```
**@dataclass**
**class** **ResponseOnTweet**:
    **
    tweet_total_response_count: int = field(init=False)
    tweet_retweet_count: int = 0
    tweet_comment_count: int = 0 
    tweet_like_count: int = 0** **def** **__post_init__(self):
        self.tweet_total_response_count = self.tweet_retweet_count  
                 + self.tweet_comment_count + self.tweet_like_count**
```

# **结论**

**是不是很好玩？嗯，我当然喜欢使用数据类。我认为你也永远不会回到老方法，除非你想要一些真正具体和定制的东西。就这样，我将结束这篇文章，在下一篇文章中，我们将研究 Python 中的装饰者，一如既往，如果你有任何建议或想法，请随时通过 [Twitter](https://twitter.com/duttasandipan_) 或 [LinkedIn](https://www.linkedin.com/in/duttasandipan/) 联系我。回头见。**