# 如何使用。包括？在 Rails 7 中

> 原文：<https://levelup.gitconnected.com/how-to-use-includes-in-rails-7-643b5e1451c4>

## 渴望加载关联基础知识以优化您的下一个应用程序

![](img/e91a0154bcd4f23e229fc258d542aeb7.png)

照片由[米卡拉·马莱克](https://unsplash.com/@mikaylamallek?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 什么是急切加载，为什么它很重要？

想象一下:你已经完成了你的编码训练营。您已经学习了面向对象编程、JavaScript 和 Ruby on Rails 的基础知识。你是垃圾的主人。现在，您已经准备好构建您的第一个应用程序，将您学到的一切付诸实践，用您的创业想法颠覆世界，就像您一直梦想的那样。

然而，随着您的应用程序开始增长，您的数据库也会随之增长。突然感觉每一页每一行代码都慢得像在爬行。数据库关系和 SQL 查询不断扩展，管理不断增长的数据变得越来越困难。心烦意乱的你突然意识到一周的数据库是不够的。您找到的每个 StackOverflow 答案都提到了一个叫做 N+1 查询的东西。有常规连接，左外连接，内连接，但是没有一个真正有意义。

这就是渴望装载来拯救。

## TL；速度三角形定位法(dead reckoning)

*   使用控制器中的`:includes`获取加载记录，以便立即加载。
*   当获取子代的子代时，在模型中使用`:includes`(多态关联)。
*   不要用得太多。预加载大量数据会降低页面速度。

## 1.如何用`:includes`解决 N+1 问题

让我们开始吧。假设您正在构建一个列出并评论餐馆的应用程序——一个 [Yelp](https://www.yelp.com/) 或 [The Fork](https://www.thefork.com/) 的克隆。通常，会有一个简单的餐馆数据库，每个餐馆会有许多用户可以留下的评论(当然也会有用户，但对于这个例子来说这是不必要的)。

restaurant 和 review 的模型如下所示:

```
#app/models/restaurant.rb
Class Restaurant << ActiveRecord::Base
   has_many :reviews
end#app/models/review.rb
Class Review << ActiveRecord::Base
   belongs_to :restaurant
end
```

为了展示一家餐馆并显示它的评论列表，控制器和视图应该是这样的:(我们保持简单并排除了 HTML 格式，但您已经明白了)

```
#app/controllers/restaurants_controller.rb
  def show
    @restaurant = Restaurant.find(params[:id])
  end #app/views/restaurants/show.html.erb
<%= @restaurant.name %><% @restaurant.reviews.each do |review| %>
<p><%= review.content %></p>
<% end %>
```

页面加载时的情况是这样的:将从数据库加载餐馆，然后对于`@restaurant.reviews`中的每个评论，将调用另一个数据库查询。这意味着一个有十条评论的餐馆需要 11 次数据库调用来显示所有信息。呀!..

这是一个可怕的 N+1 问题——对于每一个`has_many`关系实例，数据库都被称为额外时间。服务器日志看起来像这样，加载一个餐馆，然后加载多个评论。

```
Started GET "/restaurants/65" for ::1 at 2020-04-09 15:09:21 +0800
Processing by RestaurantsController#show as HTML
  Parameters: {"id"=>"65"}
  Restaurant Load (0.2ms)  SELECT  "restaurants".* FROM "restaurants" WHERE "restaurants"."id" = ? LIMIT ?  [["id", 65], ["LIMIT", 1]]
  ↳ app/controllers/restaurants_controller.rb:64
  Rendering restaurants/show.html.erb within layouts/application
  Review Load (0.1ms)  SELECT "reviews".* FROM "reviews" WHERE "reviews"."restaurant_id" = ?  [["restaurant_id", 65]]
  ↳ app/views/restaurants/show.html.erb:16
  Rendered restaurants/show.html.erb within layouts/application (8.2ms)
Completed 200 OK in 40ms (Views: 30.7ms | ActiveRecord: 0.6ms)
```

通过在表上创建一个左外连接，在一个查询中返回所有数据，快速装载解决了这个问题。添加一个急切的加载就像向控制器添加一个`:includes`语句一样简单。瞧啊。

```
#app/controllers/restaurants_controller.rb
  def show
    @restaurant = Restaurant.includes(:reviews).find(params[:id])
  end#app/views/restaurants/show.html.erb
<%= @restaurant.name %><% @restaurant.reviews.each do |review| %>
<p><%= review.content %></p>
<% end %>
```

## 2.preload()和 eager_load()呢？

实际上，在 Rails 中有三种方式可以进行急切加载。

1.  使用`preload()`通过一个单独的数据库查询获取数据(尽管只有一个，避免了 N+1 个查询)
2.  使用`eager_load()`通过一个大型查询和一个外部连接获取数据
3.  使用`includes()`动态寻找最佳解决方案并预加载或渴望加载数据。

简而言之，如果您使用的是急切加载，就不需要担心哪种情况更合适。要更深入地了解每个查询背后发生了什么，我建议查看这篇博文。

## 3.如何知道何时需要急切装载？

因此，如果不使用急切加载会导致 N+1 次查询，会降低应用程序的速度，并且使用过多的急切加载会不必要地增加应用程序的容量，那么您如何知道何时使用(或不使用)它呢？

一种简单的方法是检查服务器日志。一个页面调用五个以上的 sql WHERE 查询就足以证明出了问题。另一种方法是检查子模型何时被调用。如果一个餐馆有很多评论，并且餐馆和它的评论都在同一个页面上被调用，那么急切的加载很可能会提高页面的性能。

到目前为止，最简单的(也是我最喜欢的)检查方法是使用 [Bullet](https://github.com/flyerhzm/bullet) gem，尤其是对初学者来说。Bullet 不仅会检查代码中的 N+1 个查询，还会检查调用紧急加载的地方，但这并不十分必要。然而，在使用 gem 时，要确保只在开发环境中调用它，以免在生产环境中收到关于恶意 N+1 查询的警告。

## 4.结论

承蒙 [johncip](https://gist.github.com/johncip/17c496f04dcd032d594a785e2ad09824) 的好意，我给你们留下一份简短的备忘单:

```
eager_load()
- single query (left outer join)
- can reference the other table’s columns in wherepreload()
- a few queries (one per table)
- typically fasterincludes()
- acts like preload by default
- falls back on eager_load if you reference the associated tables
```

*你最喜欢的优化 Rails 应用的方法是什么？如果你想让我在评论中写一篇关于它的文章，请告诉我！*