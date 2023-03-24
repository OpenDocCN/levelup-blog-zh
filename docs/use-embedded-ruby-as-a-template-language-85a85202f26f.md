# 使用嵌入式 Ruby 作为模板语言

> 原文：<https://levelup.gitconnected.com/use-embedded-ruby-as-a-template-language-85a85202f26f>

## 使用 ERB 在任何 Ruby 项目中动态构建内容

![](img/ea6d264120ac42bb1756ed3106395116.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=80575) 的[大卫马克](https://pixabay.com/users/12019-12019/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=80575)

# 介绍

我们都知道静态网站和动态网站的区别。静态网站的 HTML 内容已经准备好了，并且由 web 服务器立即提供服务。另一方面，一个动态的网站随时准备它的页面。页面的一部分是预先准备好的，但其他部分是在客户端请求网页时准备好的。

当你开发一个动态网站(或 web 应用程序)时，你通常需要使用一个 web 框架，比如 [Django](https://www.djangoproject.com/) 或 [Ruby on Rails](https://rubyonrails.org/) 。这些框架允许您编写包含静态和动态部分的网页。您使用标准 HTML 编写静态部分。对于动态部分，您需要使用一种特殊的语言，并将其语句插入到 HTML 内容中。

例如，对于 Django，您需要编写如下内容:

您可以看到这是一个 HTML 文档，但是有一些语句确保内容是动态构建的。例如，`{% if latest_question_list %}`启动了一个`if`块，当`if`中的条件为`true`时，该块嵌入了`<ul>`列表。

一个等价的 Ruby on Rails 示例应该是这样的:

在上面的例子中，你可以看到一个 HTML 内容文件，但是有些部分以`<%`开始，以`%>`结束。这些块中的语句是 Ruby 代码，它们由框架在动态构建 HTML 内容以返回给客户端(即浏览器)时执行。

像这样的工具非常有用，随时可以在任何现代 web 框架中使用。web 应用程序页面的 HTML 内容很少是静态的。所以，你应该明白为什么每个 web 框架都需要以这样或那样的方式提供这个特性。

我们称这个特性为模板语言。因为它允许您创建 HTML 模板，然后使用模板语言动态创建 HTML 页面的不同实例，以返回给发出请求的浏览器。

如上所述，Ruby on Rails 允许您在 HTML 内容中编写 Ruby。这被称为嵌入式 Ruby，这就是为什么你实际创建的文件有扩展名`.html.erb`。你编写的 HTML 中嵌入了 Ruby。

但是嵌入式 Ruby 并不是 Ruby on Rails 的特性。嵌入式 Ruby 是 Ruby 标准库的一部分。你可以在这里阅读它的文档。只是 Ruby on Rails 使用它来允许您动态构建 web 页面。

因此，由于 ERB 是 Ruby std lib 的一部分，这意味着你可以在任何 Ruby 项目中使用它，而不仅仅是在 Ruby on Rails 项目中。即使您仍然在一个 Ruby on Rails 项目中，您也可以在项目中的其他动态内容构建案例中使用它。不仅仅是构建视图。

在这篇文章中，我将展示一个在简单的 Ruby 项目中使用 ERB 的例子。

# 示例域描述

让我们简单描述一下这个示例域

我们是一家教育机构，教授计算机科学和编程领域的各种课程。我们保留以下几类记录:

*   学生，在一个`students.csv`文件中。保存着我们学生的姓名和联系方式。
*   课程，在一个`courses.csv`文件里。保存课程标题及其开始和计划日期。
*   学生课程，在一个`student_courses.csv`文件中。保存学生注册的课程。

我们将使用的用例是生成 TXT 格式的电子邮件内容的能力，该内容将作为欢迎电子邮件发送给学生。电子邮件将问候学生，并向他们确认他们已经注册了哪些课程以及课程的第一天是什么时候。

如你所知，我们将使用一个模板 TXT 文件，有静态和动态部分。这就是 ERB 派上用场的地方。让我们看看怎么做。

# 演示项目源代码

如果你想直接进入一个展示这篇博文想法的项目，[点击这里](https://github.com/pmatsinopoulos/erb_demo)。

# CSV 文件

以下是我们将使用的文件:

学生. csv

课程. csv

学生 _ 课程. csv

# 电子邮件模板文件

将用作电子邮件模板的文件如下:

关于这个文件最重要的是:

1.  它有使用 ERB 定义的动态部分。无论什么在`<%`和`%>`之间。
2.  动态部分依赖于两个变量:a)一个字符串变量`full_name`，b)一个由`Course`对象组成的数组`courses`。在 ERB 解析模板生成最终的电子邮件内容之前，这些变量需要在 ERB 上下文中被绑定。

# 将变量绑定到 ERB 上下文

为了让模板被 ERB 正确解析，我们需要定义模板使用的变量，并把内容放入其中。

下面是一段这样做的示例代码:

这段代码非常简单。

*   它遍历所有学生(第 5 行)
*   对于每个学生，它查找学生注册的课程。(第 6 行)
*   如果学生有一些课程(第 7 行)
*   代码用学生的名字构建变量`full_name`的内容(第 9 行)。
*   然后构建变量`courses`的内容，以包含学生注册的所有课程(第 10 行)。
*   在第 12 行，我们做了`binding`的把戏。我们调用方法`binding`，并将其结果保存到局部变量`b`。
*   现在我们可以在第 13 行将这个绑定赋予 ERB 处理方法`result`。因此，`result`将能够使用带有变量`full_name`和`courses`的`template`。
*   在第 13 行，我们得到了处理名为`email_content`的变量的结果。
*   然后我们将它交给方法`send_email`，以便将它发送到学生的电子邮件地址(第 14 行)。

# 结束语

这是一个简单的例子，说明如何使用 ERB 作为模板语言来构建内容的动态部分。

一如既往，我喜欢向你学习。因此，请不要犹豫，在下面留下你的评论。

## 完整项目回购

[可以在这里找到。](https://github.com/pmatsinopoulos/erb_demo)演示项目包括一个名为`main.rb`的文件，您可以调用并查看它的运行情况。在本地磁盘中克隆项目后，尝试`ruby main.rb`。它将创建一个文件夹`emails`，存放模板处理后生成的外发邮件。