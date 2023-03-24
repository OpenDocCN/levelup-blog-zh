# 在 Swift 中使用 JSON

> 原文：<https://levelup.gitconnected.com/working-with-json-in-swift-c5faea0b19a1>

![](img/9dc3a3e0736a87c0b6804d3646e69830.png)

免责声明:在这一系列的帖子中，你将学习与 JSON 一起工作，而不是与 JASON，来自*13 日星期五*系列的邪恶角色一起工作。此外，自从 Swift 4 以来，与 JSON 一起工作已经成为一种乐趣……而 JASON 一直很刻薄。所以，放下你的曲棍球面具，开始编码吧！

> 这个帖子最初是我在[Swift Delivery](https://www.leandrofournier.com/):[Part 1](https://www.leandrofournier.com/working-with-json-part-1/)写的:介绍 Codable、JSONDecoder 和一个简单的例子。[第二部分](https://www.leandrofournier.com/working-with-json-part-2/):使用自定义键和自定义对象。[第 3 部分](https://www.leandrofournier.com/working-with-json-part-3/):处理数组和顶级实体案例。

# 基础

让我们假设您有以下 JSON:

```
{
	"productId": 1423,
	"name": "Hockey Mask",
	"color": "white",
	"price": 39.90
}
```

您可能从一个 API 请求收到这个 json 响应，或者您读取了一个文件内容，它是一个. JSON 文件。无论哪种方式，通常的情况是你最终拥有一个`Data`类型。

首先，让我们创建一个包含我们的数据结构的`Struct`:

```
struct Product: Codable {
    let productId: Int
    let name: String
    let color: String?
    let price: Float
}
```

这里的关键词是`Codable`，这是一个协议。实际上，它是一个*协议组合类型*，因为它包含两个协议:`Encodable`和`Decodable`。基本上，`Encodable`让您将类型转换回外部表示(即`Data`)，而`Decodable`做相反的事情。

在这个例子中，我们想要从`Data`转换到`Product`，我们可以只使用`Decodable`。但是稍后我们可能想要反过来做(例如，将一些东西发送回 API，将数据存储在`UserDefaults`中，等等)。所以还是保持为`Codable`吧。

现在，让我们将`Data`转换为`Product`:

```
let decoder = JSONDecoder()
if let product = try? decoder.decode(Product.self, from: jsonData) {
    print(product)
}
```

1.  我们实例化了`JSONDecoder`，它“[从 JSON 对象](https://developer.apple.com/documentation/foundation/jsondecoder)中解码出一个数据类型的实例”。
2.  我们使用选项绑定(而不是强制解包)来解包结果或优雅地失败。你可以选择使用`try`和`catch`解码器可能抛出的错误。
3.  输出结果。

以下是我们得到的结果:

```
Product(productId: 1423, name: "Hockey Mask", color: Optional("white"), price: 39.9)
```

# 自定义键

API 通常使用蛇形大小写约定来命名键(this_is_snake_case)…甚至是 kebab 大小写(this-is-kebab-case)。Snake case 不是一个非常敏捷的东西，因为它不符合命名准则。斯威夫特爱骆驼案(thisIsCamelCase)。所以如果你得到这样的结果:

```
{
    "product_id": 1423,
    "name": "Hockey Mask",
    "color-name": "white",
    "price-key-to-piss-the-developer-off": 39.90
}
```

镜像这些名称并不是一个好的做法，因此您应该更好地创建自定义键。像这样:

```
struct Product: Codable {
    let productId: Int
    let name: String
    let colorName: String?
    let price: Float

    enum CodingKeys: String, CodingKey {
          case productId = "product_id"
          case name
          case colorName = "color-name"
          case price = "price-key-to-piss-the-developer-off"
    }
}
```

*   **第 2 行到第 5 行。我们设置我们的对象属性名，因为它们最适合我们的需要。**
*   **第七行。**这里的关键字是`CodingKeys`，是一个`Coding Key`枚举。它将每个属性连接到编码格式的值。
*   **第八行到第十一行。**对于每个属性，我们定义在编码格式中使用哪个键。

以下是输出结果:

```
Product(productId: 1423, name: "Hockey Mask", colorName: Optional("white"), price: 39.9)
```

# 自定义对象

假设我们有这个 JSON:

```
{
    "productId": 1423,
    "name": "Hockey Mask",
    "color": "white",
    "price": {
        "price": 39.90,
        "currency": "USD",
        "formatted": "39.90 USD"
    }
}
```

这是一个非常常见的场景，幸运的是，`Codable`协议让我们的生活变得非常简单:我们只需要创建一个符合`Codable`协议的对象`Price`，然后使用它作为我们的“价格”属性的类型:

```
struct Price: Codable {
    let price: Float
    let currency: String
    let formatted: String
}   

struct Product: Codable {
    let productId: Int
    let name: String
    let color: String?
    let price: Price?
}
```

以下是输出结果:

```
Product(productId: 1423, name: "Hockey Mask", color: Optional("white"), price: Optional(__lldb_expr_3.Price(price: 39.9, currency: "USD", formatted: "39.90 USD")))
```

如上所述，您也可以在自定义对象中使用自定义键。

# 数组

在 JSON 中使用数组很容易。让我们举这个例子:

```
{
   "productId":1423,
   "name":"Hockey Mask",
   "availableColors":[
      "white",
      "brown",
      "black"
   ],
   "price":39.90
}
```

`Product`对象将如下所示:

```
struct Product: Codable {
    let productId: Int
    let name: String
    let availableColors: [String]
    let price: Float
}
```

正如您在第 4 行中看到的，我们将属性`availableColors`设置为一组`String`对象。

让我们举一个更复杂的例子:

```
{
   "productId":1423,
   "name":"Hockey Mask",
   "availableColors":[
      "white",
      "brown",
      "black"
   ],
   "price":39.90,
   "stores":[
      {
         "name":"Gerry Cosby & Co., Inc.",
         "address":"11 Pennsylvania Plaza, New York, NY 10001, United States",
         "phone":"+1 877-563-6464"
      },
      {
         "name":"National Hockey League",
         "address":"1185 6th Ave, New York, NY 10036, United States",
         "phone":"+1 212-789-2000"
      },
      {
         "name":"Modell's Sporting Goods",
         "address":"234 W 42nd St, New York, NY 10036, United States",
         "phone":"+1 212-764-7030"
      }
   ]
}
```

有一个新领域:“商店”。首先，我们为此创建一个`struct`:

```
struct Store: Codable {
    let name: String
    let address: String
    let phone: String?
}
```

请注意，我们将`phone`设置为可选的，因为我们假设 API 文档说明该字段可能不会出现在所有条目的响应中。

现在让我们使用它:

```
struct Product: Codable {
    let productId: Int
    let name: String
    let availableColors: [String]
    let price: Float
    let stores: [Store]
}
```

就这么简单。请看第 6 行:我们将属性`stores`声明为`Store`的`Array`。

# 顶级实体

API 倾向于使用包装器键，这样 JSON 的顶层实体就是一个对象。让我们假设我们正在请求可用的商店，我们得到这样的响应:

```
{
   "stores":[
      {
         "name":"Gerry Cosby & Co., Inc.",
         "address":"11 Pennsylvania Plaza, New York, NY 10001, United States",
         "phone":"+1 877-563-6464"
      },
      {
         "name":"National Hockey League",
         "address":"1185 6th Ave, New York, NY 10036, United States",
         "phone":"+1 212-789-2000"
      },
      {
         "name":"Modell's Sporting Goods",
         "address":"234 W 42nd St, New York, NY 10036, United States",
         "phone":"+1 212-764-7030"
      }
   ]
}
```

如您所见，顶级实体是字段`stores`，它是`Store`的`Array`。这里没有什么新东西:我们刚刚在上一节中看到了。我们可以这样处理:

```
struct StoresList: Codable {
    let stores: [Store]
}
```

就是这样。

但是如果没有包装键呢？JSON 可能看起来像这样:

```
[
   {
      "name":"Gerry Cosby & Co., Inc.",
      "address":"11 Pennsylvania Plaza, New York, NY 10001, United States",
      "phone":"+1 877-563-6464"
   },
   {
      "name":"National Hockey League",
      "address":"1185 6th Ave, New York, NY 10036, United States",
      "phone":"+1 212-789-2000"
   },
   {
      "name":"Modell's Sporting Goods",
      "address":"234 W 42nd St, New York, NY 10036, United States",
      "phone":"+1 212-764-7030"
   }
]
```

注意，第 1 行不是花括号:它是一个括号，因此它是一个数组。我们如何解码？非常简单:

```
let decoder = JSONDecoder()
if let stores = try? decoder.decode([Store].self, from: jsonData) {
    print(stores)
}
```

看第 2 行:`[Store].self`在告诉解码器顶层对象是`Store`的一个`Array`。很整洁，是吧？

还有一种可能的情况会让你毛骨悚然。看看这个:

```
[
   {
      "Gerry Cosby & Co., Inc.":{
         "address":"11 Pennsylvania Plaza, New York, NY 10001, United States",
         "phone":"+1 877-563-6464"
      },
      "National Hockey League":{
         "address":"1185 6th Ave, New York, NY 10036, United States",
         "phone":"+1 212-789-2000"
      },
      "Modell's Sporting Goods":{
         "address":"234 W 42nd St, New York, NY 10036, United States",
         "phone":"+1 212-764-7030"
      }
   }
]
```

不要慌！我们稍微分析一下。从上面开始阅读:JSON 是一个数组，包含用不同键包装的实体。所以“格里·考斯比…”包装了一个地址和一个电话，“国家曲棍球…”和“模特体育…”也是如此。用键包装的实体数组。

让我们首先创建实体:

```
struct Store: Codable {
    let address: String
    let phone: String?
}
```

每个商店实体都用一个密钥包装，这样看起来就像`String: Store`，不是吗？

现在，我们在一个数组中列出了所有的商店(请看第 2 行和第 15 行)。我们通过写`[String: Store]`来表达。

但是看看第 1 行和第 16 行:顶级实体也是一个数组(在这种情况下只有一个条目——`[String: Store]`——但它是一个数组)，这给了我们这个结构:`[[String: Store]]`。

我们是这样解码的:

```
let decoder = JSONDecoder()
if let stores = try? decoder.decode([[String:Store]].self, from: jsonData) {
    print(stores)
}
```

我们告诉解码器我们的顶级实体是类型`[[String: Store]]`。

# 就这样吗？

不。还有很多。我们将在这里停下来，因为它涵盖了您可能遇到的大多数常见场景。但请继续关注，因为一个高级系列即将推出。

# 现在怎么办？

开始编码！你也可以将我的个人网站 [Swift Delivery](https://www.leandrofournier.com/) 加入书签，阅读这篇文章和我写的其他文章。