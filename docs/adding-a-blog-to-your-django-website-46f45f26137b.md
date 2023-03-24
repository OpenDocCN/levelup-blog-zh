# 在 Django 网站上添加博客

> 原文：<https://levelup.gitconnected.com/adding-a-blog-to-your-django-website-46f45f26137b>

![](img/4e906b3cb9cba2cc96660c3f5af4f664.png)

如今，有大量的解决方案可以轻松创建和托管博客。然而，有时你已经有了一个网站，只是想给它添加一个博客，除了手头现有的工具之外，没有使用其他工具。在这篇文章中，我们将介绍用 Django 创建博客的过程，看看它有多简单明了。这篇文章假设你已经有了一个 Django 应用程序/站点。如果没有，可以通过遵循[官方指令](https://docs.djangoproject.com/en/2.2/intro/tutorial01/)轻松创建一个。使用的 Django 版本是 3.1.2，但是它也可以与 Django v2 一起使用。

首先，我们开始运行一个方便的 **startapp** 命令，它将搭建我们的博客应用。这应该从 **manage.py** 所在的同一个文件夹中运行:

```
$ python manage.py startapp blog
```

这将添加一个具有以下结构的**博客**目录:

```
blog/
  __init__.py
  admin.py
  apps.py
  migrations/
  __init__.py
  models.py
  tests.py
  views.py
```

第一步是将新创建的应用添加到 **settings.py:** 中的 **INSTALLED_APPS** 列表中

```
INSTALLED_APPS = [
     # other apps ...
    'mywebsite.blog'
]
```

现在我们可以开始建立博客了。我们首先打开 **models.py** 并在那里添加一个 **BlogPost** 模型。

除了预期的**标题**、**图像**、**描述**和**文本**之外，我们还有几个额外的字段，它们将在发布和导航到博客帖子时使用:

*   **slug** —是 bog 帖子 URL 的唯一可识别部分。我们正在使用 [django-autoslug](https://django-autoslug.readthedocs.io/en/latest/) 包，这将使保持字段的唯一性和自动填充变得轻而易举。在这种情况下，我们通过使用 **populate_from** param 从**标题**填充 slug。
*   **已发布** —指示帖子何时应该在网站上可见。
*   **发布日期** —帖子发布的日期，也用于对帖子进行排序。

到目前为止一切顺利。然而，当帖子被设置为**已发布**时，自动设置**发布日期**是有意义的。这可以通过扩展 Django 模型**保存**方法来实现:

为了完成模型设置，我们将向它添加两个实用方法: **__str__()** ，它将用于在整个应用程序中表示模型(特别是在管理中)，以及 **get_absolute_url()** ，它将使在视图中检索博客文章的 url 更加容易。

模型出来后，我们可以继续创建迁移并运行它们。

```
# Create migrations
$ python manage.py makemigrations blog

# Apply them
$ python manage.py migrate blog
```

此时，将新创建的迁移添加到版本控制中(我似乎总是忘记这一点)并提交/推动进度将是一个好主意。

现在我们已经有了一个合适的功能正常的 **BlogPost** 模型，是时候把它添加到 Django 的管理中了。考虑到管理员的强大，启用新模型是一项非常快速的工作:

就是这样！添加了这几行代码后，我们现在可以导航到[**http://localhost:8000/admin/blog/**](http://localhost:8000/admin/blog/)**(默认情况下，Django 在端口 8000 上运行开发服务器)并开始创建博客文章。**

**通过 admin 添加一些帖子后，下一步自然是在视图中显示它们。我们的博客将有一个按时间顺序排列的所有文章的分页列表视图，以及每个文章的单独详细视图。我们将使用基于[类的视图](https://docs.djangoproject.com/en/2.2/topics/class-based-views/),因为它们允许很好的代码重用，并且能够用最少的代码编写最常见的视图。我们可以像这样在**视图. py** 中设置它们:**

**非常简单。注意，在视图中启用分页只需要一行代码。这将为我们提供 **is_paginated** 和一些其他有用的上下文变量来呈现模板中的分页。**

**请注意，我们在两个地方对 queryset 应用了相同的过滤器。这在这里没什么大不了的，但是在某些情况下，我们希望将这种功能抽象出来。在当前情况下，我们可以将带有自定义 **QuerySet** 方法的 [Manager](https://docs.djangoproject.com/en/2.2/topics/db/managers/#calling-custom-queryset-methods-from-the-manager) 的实例添加到我们的模型中。**

**现在在视图中，我们可以使用**blog post . objects . published()**来获取所有发布的博客文章。在这一点上，我们已经得到了大部分的功能，它只需要一堆文件中的几行代码。**

**最后，我们可以设置列表和详细信息模板。按照 Django 惯例，我们将把它们放到**my website/blog/templates/blog/**中。首先，我们将扩展主基础模板，还将添加一个可重用的导航栏模板，将**“blog”**设置为活动标签:**

**注意`staticfiles`模板标签[在 Django 3.0](https://docs.djangoproject.com/en/3.0/releases/3.0/#features-removed-in-3-0) 中已经被移除，取而代之的是`static`标签。**

**作为参考，**navbar.html**可能是这样的:**

**因为 HTML 结构和它的样式不是这篇文章的主要目的，我们将简单的讨论一下。可以用这个 HTML 设置一个简单的帖子列表:**

**这里有几件事值得注意:**

*   **使用**背景图**代替 **< img/ >** 是因为在大多数情况下更容易做出反应。**
*   **我们使用添加到模型中的 **get_absolute_url** ，在帖子的标题 **href** 中轻松地重定向到帖子的详细视图，而不需要在模板中显式传递帖子 id。**
*   **如前所述，在视图中启用分页会在模板中暴露一堆有用的上下文变量，即 **is_paginated** ，用于检查是否必须呈现分页，以及 **page_obj** ，包含关于页面编号的信息。**

**说到 URL，如果我们试图导航到博客文章列表页面，我们将会收到一个 **NoReverseMatch** 错误，因为我们还没有为博客设置 URL。为了解决这个问题，让我们将 **urls.py** 添加到 **blog** 应用程序(与 **models.py** 和 **views.py** 相同级别)并启用必要的路线。注意，Django 从版本 2 开始引入了一个新的用于声明 URL 的 **path** 函数。**

**之后，我们需要转到 root **urls.py** 并在那里添加博客 URL:**

**这样我们就可以导航到 **localhost:8000/blog** 并查看我们创建的博客列表。单击文章标题应该会将我们重定向到文章详细信息页面，但是由于我们没有设置它，我们会看到一个空白页面。为了解决这个问题，让我们添加一些简单的 HTML 来显示帖子:**

**这里我们使用**安全的**模板过滤器来逃避编辑器的 HTML markdown。**

**差不多就是这样！现在我们有了一个功能齐全的个人博客，虽然很简单，但包含了所有必要的 CRUD 功能。有几种方法可以对它进行样式化和扩展，但这留给读者作为练习:)**

****奖励:添加 RSS 源****

**尽管 RSS 订阅源不像以前那么流行了，但作为一个额外的帖子发布渠道，它仍然很有帮助。此外，添加到 Django 站点非常容易。**

**第一步是将提要视图添加到 **views.py** 文件中。**

**在这里，我们给出了我们的提要标题和描述以及链接。提要将显示所有已发布的帖子，此外，我们需要映射要在提要中显示的帖子标题和描述。**

**接下来，我们将提要 url 添加到我们的博客 URL 中。确保提要 url 在博客详细信息的 url 之前，因为`slug`参数将与`feed`匹配，并且将使用`BlogPostDetailView`。**

**我们做到了！导航到`localhost:8000/blog/feed`后，我们会看到发布的文章的 RSS 提要。**