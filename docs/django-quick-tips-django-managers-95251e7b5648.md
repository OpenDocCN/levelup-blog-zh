# Django 快速提示:Django 经理

> 原文：<https://levelup.gitconnected.com/django-quick-tips-django-managers-95251e7b5648>

![](img/30f4c3bf417b516f9e388b0c6376aa38.png)

图片来自[推特:](https://unsplash.com/@jankolar?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的@jankolario

了解它们有多重要。

让我们谈谈 Django 的经理，因为没有多少人使用他们。Django project getting started guide 是一个简单的投票应用程序，通常情况下，人们会坚持阅读该指南所获得的实践和技巧。

> 管理器是一个接口，通过它向 Django 模型提供数据库查询操作。Django 应用程序中的每个模型至少有一个管理器。

管理器的一个非常简单的例子是在查询中使用的`**objects**` ，例如:

```
Model.objecst.all()
```

这是 Django 添加的默认管理器，但是您可以通过重命名管理器来使用除了`**objects**`之外的名称。要做到这一点，您只需:

```
class Post(models.Model):
   # ….
   article = models.Manager()
```

所以使用这个例子，而不是调用你现在使用的 Model.objects.all()

```
Model.article.all().
```

Django 不使用缺省值，而是让您编写自己的定制管理器，或者添加额外的方法，或者只是修改管理器返回的初始 QuerySet。

现在让我们看看如何创建海关经理，以及在什么情况下他们会对我们有用。

让我们创建一个简单的 Post 模型。

models.py

让我们添加一个 PostManager，它通过覆盖默认的 QuerySet 返回所有活动的帖子。

```
Class PostManager(models.Model):
    def get_queryser(self):
    qs = self.get_queryset().filter(active=True)
    return qsclass Post(models.Model):
    # ….
    live = PostManager()
```

现在，如果我们像下面这样使用它，应该会得到所有活动对象的列表。

```
Post.live.all()
```

无论您有什么复杂的查询集，都可以在管理器中编写，这是在视图中编写它们的一种替代方法。

让我们来看另一个例子，假设您的模型中有 view_count 属性，您希望根据文章的浏览量获得一个排名靠前的文章列表，并且希望将结果限制在一个特定的数字。您可以像下面这样做

```
### models.pyclass PostManager(models.Manager):
   # other methods
   def top_posts(self, limit):
      qs = self.get_queryset()
      qs = qs.order_by(‘-view_count’)[:limit]
      return qsclass Post(models.Model):
    # ….
    objects = PostManager()
```

现在创建 TopPosts 视图就像下面这样简单:

```
## Your viewfrom django.views.generic import ListView
from .models import Postclass TopPosts(ListView):
   template_name = ‘core/top_posts.html’
   queryset = Post.objects.top_post(limit=8)
```

这将返回一个列表，根据他们的观点计数前 8 个职位。

在 Django 中，通过模型类中的管理器构造查询集，从数据库中检索对象。因此，对于开发人员来说，知道什么是管理器以及它们在堆栈中的重要性是很有用的。

希望我说得够简单，否则，如果你有任何问题，请随时回复。

干杯。

*   https://docs.djangoproject.com/en/3.0/topics/db/managers/