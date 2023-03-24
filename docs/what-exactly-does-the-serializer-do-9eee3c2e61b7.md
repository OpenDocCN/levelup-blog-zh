# 序列化器到底是做什么的？

> 原文：<https://levelup.gitconnected.com/what-exactly-does-the-serializer-do-9eee3c2e61b7>

![](img/16f0d52bf69aed6d976ce394c767817b.png)

麦片男

在涉猎 Ruby on Rails 和尝试 JavaScript 之后，我发现将 Rails 作为后端 API 非常有用。它的设置方式使得它可以轻松地呈现 JSON 数据，以便在 JavaScript 端和浏览器上使用。

构建我的应用程序进行得很顺利，直到需要渲染一个模型相对于另一个模型的属性。

在 Ruby 中，关系是这样建立的，通过命名约定，它知道在哪里寻找它要找的东西。这里，我有三个多对多关系的模型。

```
class Review < ApplicationRecord belongs_to :user belongs_to :wineendclass User < ApplicationRecord has_many :reviews has_many :wines, through: :reviewsendclass Wine < ApplicationRecord has_many :reviews, :dependent => :destroy has_many :users, through: :reviewsend
```

在 Rails 应用程序中，要创建前端，我只需要在一个文件夹(在 app/views 文件夹中)中创建 html.erb 文件，该文件夹通过共享相同的名称(命名约定)与上述模型相关联。然后，在控制器动作中，我只需要指定要呈现的内容。通过文件的命名约定，Rails 自动(几乎神奇地)知道什么链接到什么，从而知道要呈现什么。

但是，我冒险使用 JavaScript/HTML 作为我的前端，记得吗？

因此，在控制器动作中，我需要告诉应用程序渲染什么(通过“render json:”然后指定渲染什么)。

> 例如，app/controllers/users _ controller . Rb 中的显示操作:

```
class UsersController < ApplicationController def show render json: User.find(params[:id]) endend
```

经过几次尝试后，我意识到我在 rails 中建立的关系在前端无法实现。有一段时间，我认为我的 JavaScript 是错误的。不，我需要一个序列化程序。

# 什么是序列化？

根据微软文档:

> 序列化是将对象转换为字节流以存储对象或将其传输到内存、数据库或文件的过程。它的主要目的是保存对象的状态，以便能够在需要时重新创建它。

一些技术上的东西。我认为这是将数据(模型实例)转换成可以用 JSON(或者其他格式，比如 XML)呈现的东西。

# JSON 是什么？

JavaScript 对象符号。多才多艺是因为它便于我们读写，也便于机器解析和生成。

> json.org 进一步将其定义为:

> …一种完全独立于语言的文本格式，但使用 C 语言系列的程序员熟悉的约定，包括 C、C++、C#、Java、JavaScript、Perl、Python 和许多其他语言。这些特性使 JSON 成为理想的数据交换语言。

> 有关 JSON 的示例，请从这里开始:

[](https://www.w3schools.com/js/js_json_objects.asp) [## JSON 对象

### " name":"John "，" age":30，" car":null } JSON 对象用大括号{}括起来。JSON 对象是用…

www.w3schools.com](https://www.w3schools.com/js/js_json_objects.asp) 

# 为什么要使用序列化程序？

*   通过将庞大的逻辑保留在单独的服务类中，使其远离控制器操作
*   重构代码以消除重复
*   单个控制器动作可以在 Rails API 上呈现来自多个模型的数据
*   可以指定渲染什么以及不渲染什么

> 呈现 JSON 的方法示例，但可以通过使用序列化程序来避免:

```
def show sighting = Sighting.find_by(id: params[:id]) render json: sighting.to_json(:include => { :bird => {:only => [:name, :species]}, :location => {:only => [:latitude, :longitude]} }, :except => [:updated_at])end
```

上面将呈现一个“景点”,加上一个“鸟”实例的相关名称和种类，以及一个“位置”实例的纬度和经度。

> 在浏览器中呈现 JSON 时结果:

```
{ "id": 2, "bird_id": 2, "location_id": 2, "created_at": "2019-05-14T14:56:35.978Z", "bird": { "name": "Grackle", "species": "Quiscalus Quiscula" }, "location": { "latitude": 30.26715, "longitude": -97.74306 }}
```

可以在 index 操作中使用相同的 render 语句，而不做任何修改，但这会给控制器增加大量重复。控制器是模型和视图之间的中继(显示 JSON 数据的前端 HTML 和 JavaScript)。

> 通过将工作从控制器转移到序列化器中，render 语句返回到只有一行。

```
class SightingsController < ApplicationController def index sightings = Sighting.all render json: SightingSerializer.new(sightings).to_serialized_jsonend
```

# 活动模型序列化程序

有一些 ruby 序列化程序，但是最流行的似乎是活动模型序列化程序。

[](https://github.com/rails-api/active_model_serializers) [## rails API/active _ model _ serializer

### ActiveModelSerializers 正在进行一些更新。参见开发状态。如果你发现一个错误，请报告…

github.com](https://github.com/rails-api/active_model_serializers) 

> 添加到 Gemfile:

```
gem ‘active_model_serializers’
```

然后，捆绑安装。

# 使用活动模型序列化程序

> 使用以下格式从命令行生成序列化程序文件:

```
rails g serializer user
```

> 这将创建一个文件:/app/serializer/user _ serializer . Rb

```
class UserSerializer < ActiveModel::Serializerattributes :idend
```

如果我想引入除:id 之外的其他用户属性，我只需用逗号分隔它们(例如，如果用户有:name，:age 等。).

> 此外，如果我想引入与特定 user :id 相关的其他模型实例，我只需用逗号分隔它们。

```
class UserSerializer < ActiveModel::Serializer attributes :id, :winesend
```

> 浏览器/users/1 中呈现的 JSON 现在看起来像这样:

```
{“id":1,“wines":[{"id":1,"name":"Buena Vista","varietal":"Cabernet Sauvignon”,"wine_type":"red","country":"USA","image_url":"https://images.vivino.com/thumbs/f7tR4MRISRWWdrXFoGzG_w_pb_x600.png","price":"49.99","created_at":"2020-10-23T05:43:56.758Z","updated_at":"2020-10-23T17:44:46.241Z"},{"id":2,"name":"Ken Wright","varietal":"Pinot Noir","wine_type":"red","country":"USA","image_url":"https://images.vivino.com/thumbs/Z5_ArJJxTEagV2s89FJhUA_pb_x600.png","price":"44.99","created_at":"2020-10-23T05:43:56.761Z","updated_at":"2020-10-23T05:43:56.761Z"},{"id":3,"name":"Domaine Fouassier","varietal":"Sauvignon...
```

它从 user :id 开始，并开始列出与该 user :id 相关的每个 single :wine 对象。

这是大量的信息。关于葡萄酒的一切都展示出来了。如果我只想呈现与该用户相关的葡萄酒的名称，我可以在 UserSerializer 类中进一步定义葡萄酒。

> 定义要渲染的葡萄酒的属性:

```
class UserSerializer < ActiveModel::Serializer attributes :id, :wines def wines self.object.wines.map do |wine| {name: wine.name} end endend
```

这将在浏览器中呈现简单明了的 JSON(反过来，只让需要的信息可用于呈现)。

> 下面是用户:id 加上链接到它的:wines 的:名称。

```
{"id":1,"wines":[{"name":"Buena Vista"},{"name":"Ken Wright"},{"name":"Domaine Fouassier"},{"name":"Dr. H. Thanisch"},{"name":"Billecart-Salmon"},{"name":"Giusti"}]}
```

![](img/194984158b0c229957b436394e159eb6.png)

麦片男

# 结论

这只是使用序列化程序的一个小尝试。不要像我一样纠结于认为 rails 中的关系会延续到 JavaScript/HTML 前端。尝试使用序列化程序。

## 额外资源

[](https://itnext.io/a-quickstart-guide-to-using-serializer-with-your-ruby-on-rails-api-d5052dea52c5) [## 在 Ruby on Rails API 中使用序列化程序的快速入门指南

### Ruby on Rails 是 API 的绝佳选择，因为渲染 JSON 就像 render :json 一样简单。然而，出…

itnext.io](https://itnext.io/a-quickstart-guide-to-using-serializer-with-your-ruby-on-rails-api-d5052dea52c5) [](https://www.json.org/json-en.html) [## JSON

### 编辑描述

www.json.org](https://www.json.org/json-en.html)