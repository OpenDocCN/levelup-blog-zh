# 用喀尔刻解析 JSON 超越基础

> 原文：<https://levelup.gitconnected.com/parsing-json-with-circe-beyond-the-basics-334014de393e>

![](img/11706541d05e467685c7b578450a4cce.png)

*原载于*[*https://edward-huang.com*](https://edward-huang.com/circe/2020/07/21/parsing-json-with-circe-beyond-the-basics/)*。*

喀尔刻一直是 Scala 中解析 Json 库的首选。喀尔刻的强大之处在于它可以将 Json 字符串多态地派生为 ADT。然而，我第一次使用喀尔刻时遇到了挫折——部分原因是我刚接触 Scala 编程语言，接触了函数式编程的世界。有时，错误消息是不透明的，或者有一些特定的配置需要通过源代码来实现特定的目标。

本文是文章 [7 用喀尔刻解析 Json 的快速技巧](https://medium.com/@edwardgunawan880/6-quick-tips-to-parse-json-with-circe-9bbe51ce5778)的继续。

随着我用 Scala 开发了越来越多的应用程序，并对函数式编程范式有了更多的了解，我想分享我在用喀尔刻解析 Json 时遇到的所有问题。这些是我在工作场所遇到的用例，以及我是如何解决它们的。

# ADT 中的编码/解码余积(和)类型

有时，我们希望在 ADT 中用不同于余积类型的字符串表示来解码余积。

例如，我们在《哈利·波特》中的[霍格沃茨魔法学校](https://en.wikipedia.org/wiki/Hogwarts)有 4 所房子。

我们希望将四个房屋建模为 ADT 的联产品类型，并能够对各自的`case object`进行多形态编码/解码:

```
case class House(`type` : HouseType)

sealed trait HouseType
case object GodricGryffindor extends Houses
case object SalazarSlyntherin extends Houses
case object RowenaRavenclaw extends Houses
case object HelgaHufflepuff extends Houses
```

然而，在与其他团队讨论了合同之后，他们决定将`Houses`类型作为 Snake_Case 发送。

```
"Godric_Gryffindor" => GodricGryffindor
```

如果我们使用`circe.generic.semiauto.{deriveEncoder,deriveDecoder}`，JSON 类型的结果将是`GodricGryffindor`。

```
{
  "type" : "GodricGryffindor"
}
```

首先，为`House`定义编码器和解码器实例。

```
import io.circe.generic.semiauto.{deriveEncoder, deriveDecoder}

implicit val houseEncoder:encoder[Houses] = deriveEncoder
implicit val houseDecoder:encoder[Houses] = deriveDecoder
```

对传入的 Json 进行快速转换，并将它们转换成一个`case object`。

```
implicit val housesEncoder: Encoder[HouseType] = (obj: HouseType) => obj match {
    case HelgaHufflepuff => Json.fromString("Helga_Hufflepuff")
    case RowenaRavenclaw => Json.fromString("Rowena_Ravenclaw")
    case GodricGryffindor => Json.fromString("Godric_Gryffindor")
    case SalazarSlyntherin => Json.fromString("Salazar_Slyntherin")
  }

  implicit val housesDecoder: Decoder[HouseType] = (hcursor:HCursor) => for {
    value <- hcursor.as[String]
    result <- value match {
      case "Helga_Hufflepuff" => HelgaHufflepuff.asRight
      case "Rowena_Ravenclaw" => RowenaRavenclaw.asRight
      case "Godric_Gryffindor" => GodricGryffindor.asRight
      case "Salazar_Slyntherin" => SalazarSlyntherin.asRight
      case s => DecodingFailure(s"Invalid house type ${s}", hcursor.history).asLeft
    }
  } yield result
```

我们可以通过定义编码器和解码器来实现“类型”字段成员的不同。

```
val gryffindor = (Houses(`type` = GodricGryffindor)).asJson
println(gryffindor.spaces2)

*// {*
*//  "type" : "Godric_Gryffindor"*
*// }*
```

# 在产品类型 ADT 中将 EpochMillis 转化为 Instant

您想要创建一个具有字段`createdDate`的`Currency`类。

样品`Currency`类:

```
case class Currency(id: Int, name:String, description:String, isoCodeAlphabetic:String, createdDate:Instant)
```

JSON 字符串作为 EpochMillis 传递`createdDate`。但是，您希望将其转换为一个瞬间，以便更容易在`createdDate`上进行任何操作。

样本`Currency` JSON 字符串:

```
{
  "id" : 1,
  "name" : "US Dollars",
  "description" : "United States Dollar",
  "isoCodeAlphabetic" : "USD",
  "createdDate" : 1595270691417
}
```

如果您想在喀尔刻转换`case class`的特定成员，创建另一个编码/解码实例。

您只需要在隐式作用域中创建另一个实例，用于从`Long`，EpochMillis 到`Instant`的转换。

```
implicit val encoder:Encoder[Instant] = Encoder.instance(time => Json.fromLong(time.toEpochMilli))
 implicit val decoder:Decoder[Instant] = Decoder.decodeLong.emap(l => Either.catchNonFatal(Instant.ofEpochMilli(l)).leftMap(t => "Instant"))
```

然后，为`Currency`创建一个编码器/解码器:

```
implicit val encoderCurrency: Encoder[Currency] = deriveEncoder
implicit val deoderCurrency: Decoder[Currency] = deriveDecoder
```

喀尔刻将查看隐式作用域，检查从一个值到另一个值是否有编码器/解码器实例。使用隐式解析，如果您提供一种类型的编码器/解码器实例，喀尔刻可以从该类型派生到另一种类型。

# 编码/解码多态 ADT

让我们定义您想要在这个用例中派生的 JSON 字符串:

```
{
  "houseType" : {
    "type" : "Rowena_Ravenclaw",
    "characteristics" : [
      "Loyal"
    ],
    "animalRepresentation" : "eagle"
  },
  "number" : 12
}
```

我们想把它转换成:

```
House(RowenaRavenclaw(List(Loyal),eagle),12)
```

注意到`type`表示您希望 JSON 字符串转换成什么构造函数名(在本例中，它是 RowenaRavenclaw)。

用常规 CirceDecoder 解码将返回下面的 case 类。

```
House(houseType(`type`: "Rowena_Ravenclaw", List(Loyal),eagle),12)
```

如何通过将 JSON 字符串的字段成员与构造函数名称进行匹配来对 JSON 字符串进行多态解码？

有两种方法。第一个将是常规编码和解码，第二个将使用`Circe.extras`。

# 常规编码和解码

构建 ADT 的方式有很大的不同。为了解释上述用例的解决方法，我将使用`@JsonCodec`自动派生一个常规 case 类的编码器和解码器。

定义`House`和`HouseType`的型号类型:

```
@JsonCodec
case class House(houseType: HousesTypes, number:Int)

trait House
object House {
    @JsonCodec
    case class GodricGryffindor(characteristics:List[String]) extends HousesTypes

    object GodricGryffindor{
      val typeId: String = "Godric_Gryffindor"
    }

    case object SalazarSlyntherin extends HousesTypes {
      val typeId: String = "Salazar_Slyntherin"
    }

    @JsonCodec
    case class RowenaRavenclaw(characteristics:List[String], animalRepresentation:String) extends HousesTypes

    object RowenaRavenclaw{
       val typeId: String = "Rowena_Ravenclaw"
    }

    @JsonCodec
    case class HelgaHufflepuff(animalRepresentation:String, colours:String) extends HousesTypes

    object HelgaHufflepuff{
      val typeId: String = "Helga_Hufflepuff"
    }

}
```

在上面的 ADT 定义中，我们希望为`HouseType`提供一个隐式编码器和解码器。

在对特定的`HouseType`进行编码的过程中，我们希望在 JSON 字符串中添加一个`type`字段。

```
{
  "houseType" : {
    "type" : "Rowena_Ravenclaw", << - We want to append this based on the specific HouseType
    "characteristics" : [
      "Loyal"
    ],
    "animalRepresentation" : "eagle"
  },
  "number" : 12
}
```

喀尔刻编码实例:

```
implicit val encoder:Encoder[HousesTypes] =  {
    *// deepMerge - insert the encoded Json with another field `type`*
    *// Basically overriding the current encoder with the `type`*
    case obj: GodricGryffindor => obj.asJson deepMerge(Json.obj("type" -> Json.fromString(GodricGryffindor.typeId)))
    case obj: RowenaRavenclaw => obj.asJson deepMerge(Json.obj("type" -> Json.fromString(RowenaRavenclaw.typeId)))

    case obj: HelgaHufflepuff => obj.asJson deepMerge(Json.obj("type" -> Json.fromString(HelgaHufflepuff.typeId)))
    case obj: HousesTypes => Json.obj("type" -> Json.fromString(SalazarSlyntherin.typeId))
  }
```

我们希望检索`houseType` JSON 字符串中的`type`字段，并在解码过程中基于该类型解码整个 JSON 字符串。例如，`Rowena_Ravenclaw`将指向`RowenaRavenclaw`案例类。

```
implicit val decoder:Decoder[HousesTypes] = (cursor:HCursor) => for {
    tpe <- cursor.get[String]("type")
    result <- tpe match {
      case GodricGryffindor.typeId => cursor.as[GodricGryffindor]
      case RowenaRavenclaw.typeId => cursor.as[RowenaRavenclaw]
      case HelgaHufflepuff.typeId => cursor.as[HelgaHufflepuff]
      case SalazarSlyntherin.typeId => SalazarSlyntherin.asRight
      case s => DecodingFailure(s"Invalid house type ${s}", cursor.history).asLeft
    }
  } yield result
```

# 使用 Circe.extras

喀尔刻有一个专门的库，`circe.extras`，解决了编码/解码多态 ADT。

首先，让我们通过将 HouseType 更改为`sealed trait`来重写我们的模型:

```
sealed trait HouseType {
    def `type`: String
  }

  object HouseType {
    case class GodricGryffindor(characteristics:List[String]) extends HouseType {
      override def `type`: String = "Godric_Gryffindor"
    }
    case object SalazarSlyntherin extends HouseType {
      override def `type`: String = "Salazar_Slyntherin"
    }
    case class RowenaRavenclaw(characteristics:List[String], animalRepresentation:String) extends HouseType {
      override def `type`: String = "Rowena_Ravenclaw"
    }
    case class HelgaHufflepuff(animalRepresentation:String, colours:String) extends HouseType {
      override def `type`: String = "Helga_Hufflepuff"
    }
  }
```

您可以将 JSON 字符串中的`type`设置为鉴别符，在配置中指示构造函数，并隐式声明配置。

```
implicit val houseTypeConfig = Configuration.default.withDiscriminator("type").copy(
      transformConstructorNames = {
        case "GodricGryffindor" => "Godric_Gryffindor" *// from `type` on the right transform the case changes to the left* case "SalazarSlyntherin" => "Salazar_Slyntherin" case "RowenaRavenclaw" => "Rowena_Ravenclaw" case "HelgaHufflepuff" => "Helga_Hufflepuff" }
    )
```

我正在设置转换构造函数名称的配置。我想基于其中一个字段`type`转换整个 JsonString。JSON 字段`type`包含 case 语句右侧的值(" Godric_Gryffindor "，" Salazar_Slyntherin "，...).我想把 JSON 字符串转换成 case 语句左边的 case 类(“godrick 格兰芬多”、“SalazarSlyntherin”)。case 语句的左侧将与我们上面定义的模型相匹配。

然后，在编码/解码过程中，使用`circe.generic.extras`中的`deriveConfiguredEncoder`和`deriveConfiguredDecoder`对 JSON 字符串进行多态编码/解码:

```
implicit val house2Encoder = {
    implicit val config = houseTypeConfig
    deriveConfiguredEncoder[HouseType]
}

implicit val house2Decoder = {
  implicit val config = houseTypeConfig
  deriveConfiguredDecoder[HouseType]
}
```

测试并运行上面的命令:

```
val ravenClaw: HouseType = RowenaRavenclaw(characteristics = List("Loyal"), animalRepresentation = "eagle")val ravenClawJson = ravenClaw.asJsonval ravenClawStr = ravenClawJson.noSpacesprintln(ravenClaw.asJson.spaces2)
println(decode[HouseType](ravenClawStr).right.get)
```

# 外卖食品

*   您可以通过提供这些类型的编码器/解码器实例来编码和解码特定的 Json 字段。喀尔刻利用编译器隐式解析将 JsonString 转换为所需的 ADT。
*   通过使用`circe.generic.extras.Configuration`，使用 CirceExtra 对联产品类型 ADT 进行编码/解码。
*   使用`deepMerge`将一个 JSON 对象合并到另一个 JSON 对象，并注入您想要的任何特定字段。

这就是喀尔刻 ADT 的编码和解码！

我希望这篇文章可以帮助你开始你的下一个项目，用喀尔刻编码/解码 ADT 类型。

所有源代码都是[这里](https://github.com/edwardGunawan/Blog-Tutorial/blob/master/ScalaTutorial/parsejsonwithcircetutorial/src/main/scala/DecodingComplexCoproduct.scala)。

*最初发表于*[*https://edward-huang.com*](https://edward-huang.com/circe/2020/07/21/parsing-json-with-circe-beyond-the-basics/)*。*