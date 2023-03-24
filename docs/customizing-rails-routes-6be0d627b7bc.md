# 定制铁路路线

> 原文：<https://levelup.gitconnected.com/customizing-rails-routes-6be0d627b7bc>

在熨斗学校的第二个模块中，我有幸与一名团队成员一起工作，他愿意集成人类可读的 slugs，最终使我们的项目看起来更干净/更像样。

![](img/b6ffc6b838bf4f23eae841f27e8f30f9.png)

蛞蝓舞

是的，鼻涕虫。起初，我对鼻涕虫到底是什么感到困惑。他们指的是一种“无壳陆地腹足类软体动物”，还是有更多的含义？

可以将 slug 定义为 URL 的一部分，它通过人类可读的关键字来标识用户所在的页面。虽然并不总是如此，但它通常位于 URL 的末尾。有时是当前正在访问的文件名或资源名。

一个例子就是[https://www.facebook.com/](https://www.facebook.com/CowsArePro)(你的名字)。在 rails 中，用户的显示页面通常以他们的 userID 结束，但是在这个例子中，用户将被标识，但是是他们自己设置的自定义标识符，或者可以通过创建用户时使用的不同属性自定义生成。

将 slugs 集成到你的 next Rail 项目中的一个方法是通过一个名为“FriendlyID”的 gem
friendly id 将自己定义为 slugng 和 ActiveRecord 的 permalink 插件的“瑞士军队推土机”。

FriendlyID 可以为您做的是将链接变成:

![](img/60ab59ba8326210428851e2019b910d2.png)

变成这样一个链接:

![](img/af7c9b42aedf469cfbe2afffd9ef20ff.png)

FriendlyID 非常容易设置，下面是让你的 URL 更加友好的简单步骤。

确保您的 Gem 文件中包含了 Gem Friendly_ID，注意，如果您运行的是 rails 4.0+版本，那么 Friendly_ID 的版本至少必须是 5.0.0！

```
gem 'friendly_id', '~> 5.2.4'
```

接下来，我们可以继续向项目中任何需要的模型添加 slugs！

```
rails g migration AddSlugToUsers slug:uniq
```

就像我之前说过的，slugs 可以添加到任何现有的模型中！

```
rails generate friendly_id
```

这将创建一个新文件，该文件本质上包含 FriendlyID 的配置设置(我们在这里不会涉及它！)

完成迁移后，还有几个步骤！

```
class User < ApplicationRecord
  extend FriendlyId
  friendly_id :name, use: :slugged
endclass UserController < ApplicationController
  def show
    @user = User.friendly.find(params[:id])
  end
endUser.find_each(&:save)
```

我们在这里做的是告诉用户类这里是自定义的 URL 段，我们希望显示的是用户名！

我们现在必须使用 User.friendly.find(params[:id])来搜索我们的用户，而不是。查找以返回正确的用户。

**最重要的最后一步是 find_each( & :save)** 。任何新创建的用户都将自动拥有该唯一的 slug 属性，并自动拥有其新的自定义路线，但是任何先前存在的用户则不会。通过这个方法重新保存它们将会给它们分配自定义的 slug！

等等，还有更多！

```
resources :users, param: :slug
```

以这种方式编辑您的路线完全消除了使用号码搜索用户的能力！

# 定制鼻涕虫！

拥有真正定制鼻涕虫的最后一个技巧是自己制作！

```
friendly_id :slug_candidates, use: :slugged
```

我们可以在模型中创建一个自定义的 getter 函数，并使用它的值来创建我们的 slug，而不是使用对象的单个属性。

```
def slug_candidates 
 ["#{self.name}-#{self.age}-#{self.bio}"] 
end
```

将它添加到我们的用户模型中会生成一个链接，看起来像

![](img/957ce582dddd03ee1ad48d77721b44c8.png)

FriendlyID 是一个非常强大和方便的方法来为您的用户创建更多的人可读的页面！它根本不需要花很长时间来实现，它也有助于确保用户不能从一个资源跳到另一个资源！(在正确执行 auth 之前)

现在出去开始制作你自己的定制鼻涕虫吧！

![](img/9feada40619758f9f09993c01e1f367f.png)

兔子鼻涕虫

参考资料:

[https://github.com/norman/friendly_id](https://github.com/norman/friendly_id)