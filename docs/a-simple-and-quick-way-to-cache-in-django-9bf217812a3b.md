# Django 中的缓存使您的应用程序在 99%的情况下都很快

> 原文：<https://levelup.gitconnected.com/a-simple-and-quick-way-to-cache-in-django-9bf217812a3b>

## 如何在 Django 中使用 cache_page

有很多文章解释了 Django 中缓存的工作原理。大部分都很复杂，所以我决定为你化繁为简。

![](img/65a36a2088315ddb288513c62e6a5011.png)

## 为什么要缓存？

您的应用程序中可能有些页面不会经常更改。您的应用程序仍然需要调用您的函数并呈现一个 HTML。

如果是一些应该计算的统计数据，并且可以访问您的数据库，那就更糟了。例如，它可能是 YouTube 上某个视频的观看次数。或者点赞数。

为了计算视频的观看次数，服务器需要执行一个 SQL 请求。如果有数百万用户观看这个视频，那么做一百万次是没有意义的。

## 如何缓存？

要缓存一个页面，在视图中添加一个装饰器就足够了。就是这样。

首先，导入装饰器:

```
from django.views.decorators.cache import cache_page
```

然后如下装饰您的视图:

```
@cache_page(60 * 30)
def index(request):
  ....
```

这将为所有用户缓存您的页面 30 分钟。随意缓存 24 小时:`60*60*24`。

## 不同的用户有不同的看法吗？

没问题。再加一个装饰工。

```
from django.views.decorators.vary import vary_on_cookie
from django.views.decorators.cache import cache_page
```

并按以下顺序使用它们:

```
@cache_page(60 * 30)
@vary_on_cookie
def index(request):
  ...
```

顺序很重要！参见[我的另一篇文章，更好地理解装修工](/write-your-own-decorators-in-python-in-5-minutes-f32171c50241)。

这种“随 cookie 变化”意味着如果 cookie 中的内容发生了变化，Django 就不应该使用缓存。用户只是其中的一个特例。

因此，如果用户通过身份验证或注销，缓存将再次被清理。

## 清理缓存？

假设情况改变了。又过了一天，我们需要更新统计数据。或者视频被删了。或者某些数据已经更改，缓存不再相关。

清理缓存就像这样简单:

```
from django.core.cache import cache
cache.clear()
```

你把它放在哪里？这取决于你。

你可以把它放到模型的“保存”信号中。或者删除视频的方法。

## 基于类的视图(REST)呢？

如果使用 Django Rest 框架，通常使用基于类的视图。

然后装饰器的语法有点不同。

```
from django.utils.decorators import method_decorator
from django.views.decorators.cache import cache_page
from django.views.decorators.vary import vary_on_cookie
```

并且用法是:

```
class UserViewSet(viewsets.ViewSet):

    @method_decorator(cache_page(60*60*2))
    @method_decorator(vary_on_cookie)
    def list(self, request):
        ...
```

## 但是我的 URL 中有一个正则表达式！

没问题。

Django 对每个 URL 都有一个单独的缓存。

例如，如果您有一个如下所示的 URL:

```
path(**'groups/<int:id>'**, groups_views.group_view, name=**'group_view'**),
```

然后有一个单独的缓存用于`groups/1`和`groups/2`。所以你不用担心。

## 贴吧，放吧，删吧？

不会。这些类型的请求不会被缓存。

甚至导致错误的 GET 请求(状态不是“200 OK”的任何内容)也不会被缓存。

因此，只有 GET 请求返回了“200 OK”。

## 我的申请还是太慢了！

别担心。

您可能需要索引您的数据库:

[](/just-one-index-in-django-makes-your-app-15x-faster-742e2f13108e) [## Django 中的一个索引就能让您的应用速度提高 15 倍！

### 你的应用有了索引会快多少？

levelup.gitconnected.com](/just-one-index-in-django-makes-your-app-15x-faster-742e2f13108e)