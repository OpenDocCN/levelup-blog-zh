# 我离不开的 Python“技巧”

> 原文：<https://levelup.gitconnected.com/python-tricks-i-can-not-live-without-87ae6aff3af8>

![](img/70491c3b98bfa5870c37a5def85d256b.png)

是 python。

网上有很多类似的文章，但我只是想我可以添加我对这个话题的看法，使“技巧”列表更加完整。这里使用的代码片段在某种程度上对我的工作流程至关重要，我一遍又一遍地重用它们。

# 设置

开发人员经常会忘记 python 已经设置了数据类型，并试图对所有事情都使用列表。什么是设置？嗯，长话短说:

> 集合是没有重复元素的无序集合。

如果你熟悉集合和它们的逻辑，它可以为你解决很多问题。例如，如何获得一个单词中使用的所有唯一字母？

```
myword = "NanananaBatman"
set(myword){'N', 'm', 'n', 'B', 'a', 't'}
```

嘣。问题解决了，但老实说这是来自官方的 python 文档，所以没什么好惊讶的。

这个呢？获取没有元素重复的项目列表？

```
# first you can easily change set to list and other way aroundmylist = ["a", "b", "c", "c"]# let's make a set out of itmyset = set(mylist)# myset will be:
{'a', 'b', 'c'}# and, it's already iterable so you can do:
for element in myset:
    print(element)# but you can also convert it to list again:
mynewlist = list(myset)# and mynewlist will be:
['a', 'b', 'c']
```

如你所见，重复的“c”不再是这种情况了。您应该知道的唯一一件事是， *mylist* 和 *mynewlist* 之间的元素顺序可以不同:

```
mylist = ["c", "c", "a", "b"]
mynewlist = list(set(mylist))
# mynewlist is:
['a', 'b', 'c']# as you can see it's different order;
```

但是！我们可以更深入。

想象一下，在一些实体之间有一对多的关系，更具体地说，就是用户和权限；通常情况下，一个用户可以拥有多个权限。现在，假设有人想要修改多个权限——同时添加和删除一些权限，如何解决这个问题？

```
# this is the set of permissions before change;
original_permission_set = {"is_admin", "can_post_entry", "can_edit_entry", "can_view_settings"}# this is new set of permissions;
new_permission_set = {"can_edit_settings", "is_member", "can_view_entry", "can_edit_entry"}# now permissions to add will be:
new_permission_set.difference(original_permission_set)
# which will result:
{'can_edit_settings', 'can_view_entry', 'is_member'}# As you can see can_edit_entry is in both sets; so we do not need 
# to worry about handling it# now permissions to remove will be:
original_permission_set.difference(new_permission_set)
# which will result:
{'is_admin', 'can_view_settings', 'can_post_entry'}# and basically it's also true; we switched admin to member, and add
# more permission on settings; and removed the post_entry permission
```

长话短说——不要害怕 set，因为它们可以解决你的许多痛苦，更多关于 set 的信息你可以在[官方 python 文档](https://docs.python.org/3/tutorial/datastructures.html#sets)中找到。

# 日历乐趣

当你开发一些严重依赖于日期和时间段(如月或周)的东西时，你经常会对一些信息感兴趣，如给定年份中一个月的最后一天是哪一天。这看起来很简单，但是请相信我，正确处理日期和时间是一个非常困难的话题，我想说日历实现的修饰非常差，这是一个噩梦，会导致大量的边缘情况。

那么，如何找到一个月的最后一天呢？

```
import calendarcalendar.monthrange(2020, 12)# will result:
(1, 31)# BUT! you need to be careful here, why? Let's read the documentation:help(calendar.monthrange)# Help on function monthrange in module calendar:# monthrange(year, month)
#     Return weekday (0-6 ~ Mon-Sun) and number of days (28-31) for
#    year, month.# As you can see the first value returned in tuple is a weekday, 
# not the number of the first day for a given month; let's try 
# to get the same for 2021calendar.monthrange(2021, 12)
(2, 31)# So this basically means that the first day of December 2021 is Wed
# and the last day of December 2021 is 31 (which is obvious, cause
# December always has 31 days)# let's play with Februarycalendar.monthrange(2021, 2)
(0, 28)
calendar.monthrange(2022, 2)
(1, 28)
calendar.monthrange(2023, 2)
(2, 28)
calendar.monthrange(2024, 2)
(3, 29)
calendar.monthrange(2025, 2)
(5, 28)# as you can see it handled nicely the leap year;
```

至于月初，这真的很简单——总是从 1 号开始:)

如何使用该月开始于给定工作日的信息？您可以轻松确定任何一天的工作日:

```
calendar.monthrange(2024, 2)
(3, 29)# means that February 2024 starts on Thursday
# let's define simple helper:
weekdays = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"]# now we can do something like:
weekdays[3]# will result in:
'Thursday'# now simple math to tell what day is 15th of February 2020:
offset = 3  # it's the first value from monthrangefor day in range(1, 29):
    print(day, weekdays[(day + offset - 1) % 7])1 Thursday
2 Friday
3 Saturday
4 Sunday
...
18 Sunday
19 Monday
20 Tuesday
21 Wednesday
22 Thursday
23 Friday
24 Saturday
...
28 Wednesday
29 Thursday# which basically makes sense;
```

这可能不是超级“生产”就绪示例，因为使用 *datetime* 模块可以很容易地找到工作日:

```
from datetime import datetimemydate = datetime(2024, 2, 15)
datetime.weekday(mydate)
# will result:
3# or:datetime.strftime(mydate, "%A")
'Thursday'
```

无论如何，日历模块中肯定有一些有趣的东西，知道这一点很好:

```
# checking if year is leap:calendar.isleap(2021)  # False
calendar.isleap(2024)  # True# or checking how many days will be leap days for given year span:calendar.leapdays(2021, 2026)  # 1
calendar.leapdays(2020, 2026)  # 2# read the help here, as range is: [y1, y2), meaning that second
# year is not included;calendar.leapdays(2020, 2024)  # 1
```

# 枚举有第二个参数

是的，它有第二个论点。很搞笑，但是一些真正有经验的开发者并没有意识到；您应该查看示例:

```
mylist = ['a', 'b', 'd', 'c', 'g', 'e']for i, item in enumerate(mylist):
    print(i, item)# Will give:
0 a
1 b
2 d
3 c
4 g
5 e# but, you can add a start for enumeration:for i, item in enumerate(mylist, 16):
    print(i, item)# and now you will get:
16 a
17 b
18 d
19 c
20 g
21 e
```

这是一个简单的开始点*枚举应该从哪里开始。它可以帮助您处理某种偏移逻辑。*

# 处理 if-else 逻辑

经常发生的情况是，您需要根据具体情况处理许多不同的逻辑，一个没有经验的开发人员最终会遇到这样的情况:

```
OPEN = 1
IN_PROGRESS = 2
CLOSED = 3def handle_open_status():
    print('Handling open status')def handle_in_progress_status():
    print('Handling in progress status')def handle_closed_status():
    print('Handling closed status')def handle_status_change(status):
    if status == OPEN:
        handle_open_status()
    elif status == IN_PROGRESS:
        handle_in_progress_status()
    elif status == CLOSED:
        handle_closed_status()handle_status_change(1)  # Handling open status
handle_status_change(2)  # Handling in progress status
handle_status_change(3)  # Handling closed status
```

看起来不算太差，但我见过像这样有 20 个甚至更多条件的代码库。

那么应该如何处理呢？

```
from enum import IntEnumclass StatusE(IntEnum):
    OPEN = 1
    IN_PROGRESS = 2
    CLOSED = 3def handle_open_status():
    print('Handling open status')def handle_in_progress_status():
    print('Handling in progress status')def handle_closed_status():
    print('Handling closed status')handlers = {
    StatusE.OPEN.value: handle_open_status,
    StatusE.IN_PROGRESS.value: handle_in_progress_status,
    StatusE.CLOSED.value: handle_closed_status
}def handle_status_change(status):
    if status not in handlers:
         raise Exception(f'No handler found for status: {status}')
    handler = handlers[status]
    handler()handle_status_change(StatusE.OPEN.value)  # Handling open status
handle_status_change(StatusE.IN_PROGRESS.value)  # Handling in progress status
handle_status_change(StatusE.CLOSED.value)  # Handling closed status
handle_status_change(4)  # Will raise the exception
```

这是一种可以在 python 中使用的常见模式，一般来说，它使代码看起来更整洁一些——尤其是当您的主处理方法很庞大并且有很多条件要处理时。

# 枚举模块

我把上面一段的题目稍微划了一下，我们再深入一下。

`enum`模块提供了处理枚举的实用程序，最有趣的有:`Enum, IntEnum` —我们来挖掘一下:

```
from enum import Enum, IntEnum, Flag, IntFlagclass MyEnum(Enum):
    FIRST = "first"
    SECOND = "second"
    THIRD = "third" class MyIntEnum(IntEnum):
    ONE = 1
    TWO = 2
    THREE = 3# Now we can do things like:MyEnum.FIRST  # <MyEnum.FIRST: 'first'># it has value and name attributes, which are handy:MyEnum.FIRST.value  # 'first'
MyEnum.FIRST.name  # 'FIRST'# additionally we can do things like:MyEnum('first')  # <MyEnum.FIRST: 'first'>, get enum by value
MyEnum['FIRST']  # <MyEnum.FIRST: 'first'>, get enum by name
```

与`IntEnum`相似——但也有一些不同:

```
MyEnum.FIRST == "first"  # False# butMyIntEnum.ONE == 1  # True# to make first example to work:MyEnum.FIRST.value == "first"  # True
```

在中型代码库`enum`模块是非常有助于管理你的项目中的常数。

enum 的本地化可能有点棘手，但这是可行的，让我快速展示一下我在`django`是如何处理的:

```
from enum import Enumfrom django.utils.translation import gettext_lazy as _class MyEnum(Enum):
    FIRST = "first"
    SECOND = "second"
    THIRD = "third" @classmethod
    def choices(cls):
        return [
             (cls.FIRST.value, _('first')),
             (cls.SECOND.value, _('second')),
             (cls.THIRD.value, _('third'))
         ]# And later in eg. model definiton:some_field = models.CharField(max_length=10, choices=MyEnum.choices())
```

# IPython FTW

`ipython`意思是交互式 python，它是一个用于交互式计算的命令外壳，它就像一个 python 解释器，但包含了电池，或者以不同的方式放置:类固醇。

要使用`ipython`,您需要安装它:

```
pip install ipython
```

稍后，不使用典型的`python`命令进入解释器，而是使用`ipython`

```
# you should see something like this after you start:Python 3.8.5 (default, Jul 28 2020, 12:59:40) 
Type 'copyright', 'credits' or 'license' for more information
IPython 7.18.1 -- An enhanced Interactive Python. Type '?' for help.In [1]:
```

它支持系统命令，如:`ls`或`cat``tab`键会显示提示，这使得交互式编程的会话更加愉快。你也可以使用向上/向下箭头来搜索最近的命令，一般来说，你可以做很多事情——参考[ipython](https://ipython.readthedocs.io/en/stable/)的官方文档。我特别推荐熟悉一下[魔法函数](https://ipython.readthedocs.io/en/stable/interactive/tutorial.html#magic-functions)，使用它们你可以保存、分享你使用过的代码、测量执行时间等等。

暂时就这样了。希望你会在这里发现一些有趣的东西。

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)