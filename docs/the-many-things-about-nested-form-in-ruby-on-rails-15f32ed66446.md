# Ruby on Rails 中关于嵌套表单的许多事情

> 原文：<https://levelup.gitconnected.com/the-many-things-about-nested-form-in-ruby-on-rails-15f32ed66446>

![](img/95829aaf888d4bb71f2035f6985e86ad.png)

在开发应用程序时，很多时候你必须创建不同类型的表单供用户填写。可能有一个简单的表单，其中只包含一个模型，也可能有更多的表单包含一系列模型。在这篇文章中，我将向你展示如何处理不同类型的情况。

*   设置一个表单—有 _ 多或有 _ 一关系
*   删除关联的对象

> 我所有的 UI 组件都是基于 Fomantic-UI 的。有兴趣可以参考我之前的文章做参考。

[](https://medium.com/swlh/plugins-and-frameworks-for-your-next-ruby-on-rails-project-5793d8dee3eb) [## 下一个 Ruby on Rails 项目的插件和框架

### 对于 web 开发项目，你必须有使用不同开源插件和前端框架的经验…

medium.com](https://medium.com/swlh/plugins-and-frameworks-for-your-next-ruby-on-rails-project-5793d8dee3eb) 

# 设置一个表单—有 _ 多或有 _ 一关系

从 Rails 5.2 开始，它鼓励使用 form_with，默认情况下请求是异步提交的。使用我上一篇文章中的旅行模型，如果我们要在一个表单中完成它，它将是这样的:

```
**app/views/trips/_form.html.erb**<%= form_with @trip, class: "ui form" do |form| %>
  <%= form.text_field :name, placeholder: "An exciting football trip" %>
<% end %>
```

为了使我的旅行模型更加真实，增加了一些联想。我的旅行将有多条路线。每条路线都包括许多我想去的目的地。我会在旅行开始前决定走哪条路线。这是我的模型和关联的外观:

```
**app/models/trip.rb**class Trip < AppllicationRecord 
  has_many :itineraries
  accepts_nested_attributes_for :itineraries
end**app/models/itinerary.rb**class Itinerary < ApplicationRecord
  belongs_to :trip
  has_many :destinations
  accepts_nested_attributes_for :destinations
end**app/models/destination.rb**class Destination < ApplicationRecord
  belongs_to :itinerary 
end
```

让我们用我的新模型来重温一下表单。

```
**app/views/trips/_form.html.erb**<%= form_with @trip, class: ”ui form” do |trip_form| %>
<div class="fields">
  <div class="field">
    <%= trip_form.label :name %>
    <%= trip_form.text_field :name, placeholder: ”An exciting football trip” %>
  </div>
  <%= trip_form.fields_for :itineraries do |itinerary_form| %>
  <div class="fields">
    <div class="field">
      <%= itinerary_form.label :name %>
      <%= itinerary_form.text_field :name, placeholder: ”Direct to Manchester” %>
    </div>
    <div class="field">
      <%= itinerary_form.label :date_from %>
      <div class="ui calendar">
        <div class="ui input left icon">
        <i class="calendar icon"></i>
        <%= itinerary_form.text_field :text_field :date_from, placeholder: "Pick a start date" %>
      </div>
    </div>
    <div class="field">
      <%= itinerary_form.label :date_to %>
      <div class="ui calendar">
        <div class="ui input left icon">
        <i class="calendar icon"></i>
        <%= itinerary_form.text_field :text_field :date_to, placeholder: "Pick a end date" %>
      </div>
    </div>
  </div> <%= itinerary_form.fields_for :destinations, Destionation.new do |destination_form| %>
    <div class="fields>
      <div class="field">
        <%= destination_form.label :name %>
        <%= destination_form.text_field :name, placeholder: ”Old Trafford" %>
      </div>
    </div>
    <% end %> 
  <% end %>
  <button class=“ui icon button” type=“submit”>
    <i class="icon save"></i>&nbsp;Save
  </button>
<% end %>
```

现在我们有一个嵌套的表单，由 ***行程*** 和 ***目的地*** 组成。您现在可以看到表单是如何组织的。该表单在模型中有一个`**fields_for**`及其关联的`**accepts_nested_attributes_for**`。

控制器怎么样？

```
**app/controllers/trips_controller.rb**class TripsController < ApplicationController def new
    @trip = Trip.new
    @trip.itineraries.build
  end def create 
    @trip = Trip.new(trip_params) 
    if @trip.save 
      redirect_to trip_path(@trip) 
    else
      render :new 
    end
  end def trip_params
    params.require(:trip)
          .permit(:name, 
                  itineraries_attributes: [:id, :trip_id, :name, :date_from, :date_to, destinations_attributes: [:id, :itinerary_id, :name] ])
  endend
```

这真的很简单，rails 会在幕后自动完成这些技巧。现在您已经知道如何在单个表单中创建对象及其关联。如果你想改变呢？

```
**app/controllers/trips_controller.rb**class TripsController < ApplicationController

  ...    def edit
    @trip = Trip.find(params[:id]
  end

  ...end**app/views/trips/_form.html.erb**<%= form_with @trip, class: ”ui form” do |trip_form| %>
<div class="fields">
  <div class="field">
    <%= trip_form.label :name %>
    <%= trip_form.text_field :name, placeholder: ”An exciting football trip” %>
  </div> <%= trip_form.fields_for :itineraries, @trip.itineraries do |itinerary_form| %>
    <div class="fields">
      <div class="field">
        <%= itinerary_form.label :name %>
        <%= itinerary_form.text_field :name, placeholder: ”Direct to Manchester” %>
      </div>
      <div class="field">
        <%= itinerary_form.label :date_from %>
        <div class="ui calendar">
          <div class="ui input left icon">
          <i class="calendar icon"></i>
          <%= itinerary_form.text_field :text_field :date_from, placeholder: "Pick a start date" %>
        </div>
      </div>
      <div class="field">
        <%= itinerary_form.label :date_to %>
        <div class="ui calendar">
          <div class="ui input left icon">
          <i class="calendar icon"></i>
          <%= itinerary_form.text_field :text_field :date_to, placeholder: "Pick a end date" %>
        </div>
      </div>
    </div>

    <%= itinerary_form.fields_for :destinations, itinerary_form.object.destinations do |destination_form| %>
      <div class="fields>
        <div class="field">
          <%= destination_form.label :name %>
          <%= destination_form.text_field :name, placeholder: ”Old Trafford" %>
        </div>
      </div>
    <% end %> 
  <% end %>
  <button class=“ui icon button” type=“submit”>
    <i class="icon save"></i>&nbsp;Save
  </button>
<% end %>
```

您可以看到我们正在将实例*路线*和*目的地*传递给的*字段。*

# 删除关联的对象

比方说，在同一张表格上，您想从您的旅程中删除一个目的地。您可以轻松地创建一个标记，让 rails 知道该项目将被删除。我们是这样做的:

```
**app/models/itinerary.rb**class Itinerary < ApplicationRecord
  belongs_to :trip
  has_many :destinations
  accepts_nested_attributes_for :destinations, **allow_destroy: true** end**app/views/trips/_form.html.erb**<%= form_with @trip, class: ”ui form” do |trip_form| %>... <%= itinerary_form.fields_for :destinations, itinerary_form.object.destinations do |destination_form| %>
      <div class="fields>
        <div class="field">
          <%= destination_form.label :name %>
          <%= destination_form.text_field :name, placeholder: ”Old Trafford" %>
        </div>
        <div class=“field”>
          <div class=“ui checkbox”>
            <%= destination_form.check_box :_destroy %><%= destination_form.label ”Delete?” %>
          </div>
        </div>
      </div>
    <% end %>...<% end %>**app/controllers/trips_controller.rb**class TripsController < ApplicationController

  ... def trip_params
    params.require(:trip)
          .permit(:name, 
                  itineraries_attributes: [:id, :trip_id, :name, :date_from, :date_to, destinations_attributes: [:id, :itinerary_id, :name, **:_destroy**] ])
  end...end
```

在路线模型中，您必须启用 allow_destroy 选项，并在控制器中允许使用`:_destroy`属性。然后只需在表单中添加一个复选框。就是这样！

如果您想在同一个表单中添加一个新的目的地，或者您需要从远程位置加载动态数据并显示在下拉框中，事情会变得更加复杂。享受构建你的下一个表单，它可能会令人沮丧，但却很有成就感。

下次见！