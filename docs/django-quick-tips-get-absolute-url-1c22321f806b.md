# Django 快速提示:get_absolute_url()

> 原文：<https://levelup.gitconnected.com/django-quick-tips-get-absolute-url-1c22321f806b>

![](img/5409dd81be464c0d8b68da5fd3a3649c.png)

说到底，都是吻的问题。

如果您是一名经验丰富的 Django 开发人员，您可能会遇到这种情况。然而，如果你是一个初学者，接触良好的实践将大大降低你的学习曲线。

Django URLs 日积月累:`get_absolute_url().`函数名现在恰好是 Django 社区的标准，但毕竟它只是另一个名字，所以这取决于你。

我们将从用`Posts`资源实现一个简单的 CRUD 开始，这样我们以后就可以很容易地正确看待事情了。

# 模型

用两个属性 a `title`和`content`创建一个基本的`Post`模型。

models.py

# 视图

然后添加基本的视图函数，一个负责返回所有的帖子——在这个特殊的例子中过滤最后三个——另一个负责显示每个帖子的详细页面。

views.py

出于本教程的考虑，我选择了基于函数的视图，但这同样适用于 CBV(基于类的视图)。

# 资源定位符

最后，注意 URL。

urls.py

并且不要忘记更新位于主项目目录中的 urls.py。

# 模板

现在，让我们用一个简单的 HTML 来显示我们的内容。

```
// index.html
{% for post in posts %}                       
<ul>                         
   <li><a href="{% url 'app_name:post_detail' pk=post.id %}">{{ post.title }}</a>
    </li>                      
</ul>                       
{% endfor %}
```

到目前为止，这是 Django 中资源生命周期的基础:收集数据，然后处理，最后显示。

**模型- >视图- >模板。**

然后在 URL 被触发时呈现`detail`页面。

```
// detail.html
<h5>{{ post.title }}</h5>                       
<p>{{ post.content }}</p>
```

浏览管理页面添加数据，以便我们可以测试它。

如果你点击你的应用程序网址，它应该会返回你刚刚添加的文章标题的链接。

我们在`index.htm` l 中将`id`传递给 URL 的方式可能会导致拼写错误，原因很简单:app_name，url_name 然后将 post_id 赋给你在`views.py`中声明的适当变量。

在这里，您应该有一个类似于`get_absolute_url()`的函数来处理资源细节页面的呈现。

让我们添加该功能并进行必要的调整。

```
// models.py
from django.urls import reverse   // ---> add
...
def __str__(self):
   return self.title
def get_absolute_url(self):       // ---> new function
   return reverse('post_detail', args**=**[str(*self*.id)])
```

现在我们已经添加了函数，让我们看看如何使用它来呈现详细页面。

```
// index.html
{% for post in posts %}                       
<ul>                         
   <li><a href="{{ post.get_absolute_url }}">{{ post.title }}</a></li>    // there you have it.                     
</ul>                       
{% endfor %}
```

就是这样，一切都在后面完成，只需替换所有不必要的参数并调用函数来获得每个帖子的绝对 URL。

这里的关键要点是尽可能避免使用模板进行处理，如果在后端可以做任何事情，**就去做**并保持模板真正的和主要的用途—数据和内容显示。

我希望你会发现这个快速洞察有用。

干杯。

## 关于 Django URLs 的更多信息

*   [https://docs.djangoproject.com/en/3.0/topics/http/urls/](https://docs.djangoproject.com/en/3.0/topics/http/urls/)
*   【https://tutorial.djangogirls.org/en/django_urls/ 号