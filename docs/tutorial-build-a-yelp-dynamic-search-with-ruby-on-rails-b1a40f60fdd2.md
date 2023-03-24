# 教程:用 Ruby on Rails 构建 Yelp 动态搜索

> 原文：<https://levelup.gitconnected.com/tutorial-build-a-yelp-dynamic-search-with-ruby-on-rails-b1a40f60fdd2>

## 防止用户输入错误！

![](img/42f200969f0811e2081817b73f272369.png)

(由 [Fernando Hernandez](https://unsplash.com/@_ferh97?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄)

在从事编码项目时，我遇到了一个有趣的问题。目标是使用 Ruby on Rails 实现一个类似 Yelp 搜索栏的特性。我们如何构建它，以便用户不仅可以通过搜索活动的名称或组织，还可以通过搜索任何关键字甚至邮政编码来找到活动？最重要的是，如果用户错误地将活动大写，或者在单词中间添加了一个随机的空格，我们如何产生想要的搜索结果？

这篇文章将一步一步地向你介绍，我们如何创建一个动态搜索域来满足上面提到的所有期望的特性。

## 步骤 0。设置

从一些上下文开始，我们希望用户能够过滤数据库中的所有活动，所以我在 index 方法下的 activities 控制器中编写代码，我们还可以将页面格式化为独立的搜索栏(我将更详细地介绍这一点)。如果想看项目代码做参考，勾选 [*这个 github repo*](https://github.com/stephaniezou1/acorn-search-engine) *。*

搜索栏本身将通过 activities > index view 页面与用户交互。

请注意，在我的 HTML 文件中，我构建了一个“form_tag ”,这样当用户在表单中键入并提交搜索词时，它会保存在带有关键字[:search]的 params 中。

## 第一步。创建搜索变量

从这里，我们知道可以将 params[:search]分配给一个变量，并开始在活动控制器中使用它来创建我们想要的搜索栏。

让我们将“params[:search]”存储在变量“search_term”中，并将“@activities”初始化为“Activity.all”

## 第二步。设置搜索功能

现在回到基本原则。我们想在这里完成什么？我们希望能够通过输入名称、组织(或者您正在使用的任何关联模型，在我的例子中，活动属于组织)、关键字，甚至邮政编码来找到活动。所以让我们把代码写进去。

内置的 Ruby 数组方法`. include？`允许我们检查用户输入的搜索词是否可以在这些类别中找到。双管||在 Ruby 中代表“或”。注意对于邮政编码，我们使用双等号代替，因为邮政编码应该是精确的。

## 第三步。防止用户输入错误

现在转到我们的下一个目标，如果用户在搜索词中间添加一个大写字母会怎么样？Ruby 也有一个方便的函数，叫做‘down case’。我们不仅要为搜索词实现它，还要为“activities”的所有搜索类别实现它，以便它们能够匹配。

现在，最后，我们的最后一个功能是将它格式化为一个独立的搜索栏。我们如何消除空白？ **Regex** ！只需在搜索词的末尾插入这个方便的 regex 代码片段` gsub(/\s+/，"")`来清除用户可能错误地输入到搜索栏中的空格。

万岁！我们创建了一个动态搜索栏，允许您以多种不同的方式缩小搜索结果。

## 第四步。格式化独立的搜索栏

现在，在我的 HTML 文件中，我有一个`@activities.each do `块，它遍历数据库中的所有活动。所以现在，搜索栏出现在我们完整的活动列表上方。

要创建一个独立的搜索栏，只需在 activities 控制器中编写一个条件，如果“params[:search]”不存在，就将“@activities”设置为一个空数组。

现在，用户应该只能看到页面上的搜索栏。只有在搜索词中提交后，结果才会开始填充。

看着它，我很想再重构一下代码，让它更[干巴巴](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，但事实上，我们刚刚成功实现了一个非常棒的搜索功能！

—

*非常感谢*[*syl wia Vargas*](https://medium.com/u/95a187e4f558?source=post_page-----b1a40f60fdd2--------------------------------)*所有不可思议的资源和指点！*