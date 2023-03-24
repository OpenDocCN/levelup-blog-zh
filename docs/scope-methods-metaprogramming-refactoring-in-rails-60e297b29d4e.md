# Rails 中的范围方法、元编程和重构

> 原文：<https://levelup.gitconnected.com/scope-methods-metaprogramming-refactoring-in-rails-60e297b29d4e>

![](img/fc6da2ee7960eb2b5eaa386cc94ab75e.png)

[日志](http://wine-log.herokuapp.com/)

构建范围方法是构建我的 rails 项目中更具挑战性(也更有回报)的方面之一。

第一个很简单——在创建新酒的表单中，用户可以将一款酒标记为“最爱”。目标是使用活动记录查询接口查询数据库，并返回一组喜爱的葡萄酒。因为我想返回多个对象，所以`where`方法是最好的选择。

`scope :is_favorite, -> {where(favorite: true)}`

我还希望能够返回高评级的葡萄酒——即使用户没有将该葡萄酒标记为最爱。`where`在这里也很完美。

`scope :highly_rated, -> {where("rating > ?", 7)}`

下一组方法更加复杂——我想按类型和平均评级对葡萄酒进行分类，以了解用户平均更喜欢什么类型的葡萄酒。

构建一个`average rating`的方法很简单:

```
def self.average_rating
    average(:rating)
  end
```

然而，按葡萄酒类型查询则不然。葡萄酒的种类(红、白、玫瑰红)是有限的——所以我选择不为它创建模型。

尽管如此，我还是希望能够按类型对葡萄酒进行排序，以便为用户提供见解——所以我创建了一组 scope 方法，以便我可以基于葡萄酒类型进行查询:

```
scope :red, -> {where(wine_type: "Red")}
  scope :white, -> {where(wine_type: "White")}      
  scope :rose, -> {where(wine_type: "Rose")}    
  scope :sweet, -> {where(wine_type: "Sweet")}      
  scope :sparkling, -> {where(wine_type: "Sparkling")}     
  scope :other, -> {where(wine_type: "other")}
```

现在，我可以打电话给`.red`退回所有红酒！

第一个版本的`top_rated`类方法是:

```
def self.top_rated
    averages = {"Red" => Wine.red.average_rating.to_f,
                "White" => Wine.white.average_rating.to_f,
                "Rose"=> Wine.rose.average_rating.to_f,
                "Sparkling"=> Wine.sparkling.average_rating.to_f,
                "sweet"=> Wine.sweet.average_rating.to_f}
    top_type = averages.sort_by { |wine_type, avg| avg }.last[0]
  end
```

它工作了——但是它很长，而且，因为我写了不止一个基于葡萄酒类型的 scope 方法，所以是重复的。

我分两步让代码变得更加枯燥。首先，我消除了散列，以便使用`pluck`遍历 wines 表的`:wine_type`中的值。

```
def self.top_rated
    averages = {}
    types = Wine.pluck(:wine_type).uniq
    types.each do |type|
      averages[type] = Wine.where(wine_type: type).average_rating.to_f
    end
    top_type = averages.sort_by { |wine_type, avg| avg }.last[0]
end
```

这更干净，但仍不完美。为什么要在数据库中查询一个甚至不是对象的属性，而且我知道可能的选项？

创建一个类常量消除了一行代码和对数据库的查询。

`WINE_TYPES = ["Red", "White", "Rose", "Sparkling", "Sweet", "Other"]`

最后一种方法如下:

```
def self.top_rated
    averages = {}
    WINE_TYPES.each do |type|
      averages[type] = Wine.where(wine_type: type).average_rating.to_f
    end
    top_type = averages.sort_by { |wine_type, avg| avg }.last[0]
  end
```

我还能够用类常量来整理葡萄酒表单:

```
<%= f.label :wine_type, "Type:" %>
<%= f.select :wine_type, options_for_select(Wine::WINE_TYPES, selected: @wine.wine_type) %>
```

而不是:

```
<%= f.label :wine_type, "Type:" %>
<%= f.select :wine_type, options_for_select(["Red", "White", "Rose", "Sparking", "Sweet", "Other"], selected: @wine.wine_type) %>
```

能够根据葡萄酒类型进行过滤的 scope 方法是有效的，但是不优雅且不可伸缩。如果我想创造更多的选择呢？六行代码可能会快速增长，变得枯燥和笨拙。我能够再次重构，使用[单例类方法](http://https//apidock.com/ruby/Object/singleton_class)。singleton 类是元编程的一种形式，允许更大的灵活性。它在类内部被调用——但不是作为一个方法，因为它定义了一个方法本身。我的应用程序使用的实现如下。

```
self.singleton_class.class_eval do
    WINE_TYPES.each do |wine_type|
      define_method(wine_type.downcase.to_sym) do
        where(wine_type: wine_type)
      end
    end
  end
```

重构是一个多步骤的过程，它让我的代码更容易理解，更简单，也让我更容易添加额外的方法。