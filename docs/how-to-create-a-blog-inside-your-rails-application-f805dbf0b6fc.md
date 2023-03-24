# 如何在 Rails 应用程序中创建博客

> 原文：<https://levelup.gitconnected.com/how-to-create-a-blog-inside-your-rails-application-f805dbf0b6fc>

## 在 Ruby on Rails 应用程序中定制开发的博客

![](img/a04f056384c090194d15a2872e0df3c7.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=744077) 的 [Gerhard Lipold](https://pixabay.com/users/LaughingRaven-834173/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=744077)

你有没有一个 Ruby on Rails (RoR)应用程序，你想在其中添加博客功能？

手头有各种选择。例如，有很多 [RoR 博客引擎](https://www.ruby-toolbox.com/categories/Blog_Engines)可以嵌入(安装)到你的 RoR 应用中。但是，您也可以选择使用自己的定制编程来构建最基本的功能。

让我们看看如何做到这一点。

# 帖子内容

首先，您可以将文章内容存储在一个`Github`私有存储库中。将内容存储在`Github` repo 上有多种优势:

1.  您可以使用 [Markdown](https://en.wikipedia.org/wiki/Markdown) 来编写内容。Markdown 是一种非常友好的编写技术文档或博客的语言。
2.  你可以依靠`git`的版本控制。所以，你可以有不同版本的文章内容。此外，您可以在内容上进行协作，直到您的团队中的所有人同时完成它，并处理“拉”请求。换句话说，在合并到`master`分支并准备发布之前，内容正在由您团队的其他成员进行审查。
3.  `GitHub`提供了一个非常友好的 API，可以用来轻松获取 HTML 格式的文档内容。然后，你只需要展示它。

# 路线

准备好内容是不够的。您必须将其与您的 RoR 应用程序集成在一起。

整合从设置必要的路线开始。在应用程序的`config/routes.rb`文件中，添加以下路线:

上述路由允许您发布以下 URL:

1.  `https://<your-domain>/blog`。这是显示可用博客文章列表的页面。
2.  `https://<your-domain>/blog/:title`。这是特定博客文章的页面。

# 模型

为了能够列出博客文章并显示博客文章，您必须定义一个表示博客文章的数据模型。因此，在您的数据库中，您使用以下模式结构定义表`blog_posts`:

关于上表，需要注意以下几点:

1.  它也可能有一个`subtitle`，但这是可选的。
2.  有一个重要的`state`属性接受值`draft`或`published`。您将在公共页面上显示`published`博客文章。
3.  `content_url`是一个指向`GitHub`文档的 URL 字符串。
4.  `image_path`指向一个图像，它将被用作博客文章页面标题的背景图像。
5.  `publisher`是作者的名字。
6.  `published_at`是文章发表的日期和时间。

然后，你必须定义相应的`ActiveRecord`模型。这是在文件`blog_post.rb`内的`app/models`文件夹内定义的。

上面的代码:

1.  通过应用一系列验证来确保模型是有效的。
2.  使用`[friendly_id](https://github.com/norman/friendly_id)`为博客文章创建 SEO 友好的公共 URL。
3.  使用`[GitHub API gem](https://github.com/octokit/octokit.rb)`获取文件内容。

# 控制器

`blog_posts_controller`负责`index`和`show`的动作。

## 索引操作

这个`index`动作很简单:

它实例化了一个变量`@blog_posts`。它保存了一个分页查询，该查询按照发布日期的降序获取`published`的博客文章。

## 显示动作

这个`show`动作也很简单:

它通过`title`查找请求的博客文章。

# 视图

你必须有两个与博客文章相关的视图，一个用于列出所有博客文章的`index`动作。另一个用于显示特定博客文章内容的`show`动作。

## 布局

两个视图应该使用相同的布局。你可以调用这个布局`blog`，它应该存储在`app/views/layouts`文件夹中(文件:`blog.html.haml`)。
基本上，对于布局你也可以从[这个](http://blackrockdigital.github.io/startbootstrap-clean-blog/)中得到一些思路。

`blog.html.haml`的一个基本例子可以是这样的:

## 索引视图

这个视图依赖于上面的布局，基本上遍历了`@blog_posts`实例变量，并打印了每一篇博文的摘要细节。

以下是部分代码片段，可以让您了解`index`视图应该如何或多或少地编码:

## 显示视图

与`index`视图相同，它依赖于布局并打印`@blog_post`实例变量的内容。

# 迪斯克斯

为了让访问者在你的博客文章上留下评论，你可以集成 [Disqus](https://disqus.com) 。在每个博客文章展示页面的底部，你必须呈现必要的 JavaScript 代码片段(由 Disqus 提供)，这样就完成了。

# 结束语

将博客功能引入您的 RoR 应用程序可能需要许多不同的技术方法。你可以使用自己的代码，而不是依赖第三方博客引擎。您可以使用的工具有:

1.  `GitHub`存储博文内容的私有回购。
2.  [GitHub API Octokit gem](https://github.com/octokit/octokit.rb) 使用 Ruby 访问`GitHub`内容。
3.  一个适合博客帖子的 [Twitter 自举布局](http://blackrockdigital.github.io/startbootstrap-clean-blog/)。
4.  集成用户对你的博客文章发表评论的能力。

# 您的反馈

你能告诉我们你是如何将博客与你的 RoR 应用整合在一起的吗？我们很高兴在这里听到您的评论。

让我知道你的想法。我喜欢向你学习。