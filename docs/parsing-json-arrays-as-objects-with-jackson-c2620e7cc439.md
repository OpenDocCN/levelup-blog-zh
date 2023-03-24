# 用 Jackson 将 JSON 数组解析为对象

> 原文：<https://levelup.gitconnected.com/parsing-json-arrays-as-objects-with-jackson-c2620e7cc439>

![](img/f898d11ceb3d490a77494f5b1280567b.png)

来自 [Pixabay](https://pixabay.com/ru/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=2178890) 的[像素](https://pixabay.com/ru/users/pexels-2286921/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=2178890)的图像

在本文中，我将描述来自 [Jackson](https://github.com/FasterXML/jackson) JSON 解析库的一个技巧，它允许以更大的灵活性解析非典型 JSON。

有时候，您无法影响所获得的 JSON 的结构——例如，您处理遗留数据或者从另一个系统使用它。当解析一个人的记录时，您通常会想到这样的事情:

```
{
  "name" : "Jane Smith",
  "rate" : 4.2,
  "age" : 42
}
```

但是，您可能获得一个人的所有属性，就像一个数组的元素一样，没有属性名:

```
[
  "Jane Smith",
  4.2,
  42
]
```

数组的第一个元素总是字符串，第二个是浮点值，第三个是整数。在 Java 代码中，您很可能希望将其建模为具有适当类型字段的对象，我们称之为`Person`:

```
class Person {

    private String name;

    private Double rate;

    private Integer age; // getters and setters omitted}
```

但是如果您尝试使用 Jackson 库像这样解析它，您将会得到一个错误——一个带有属性的 JSON 对象应该基于该模型，但是您尝试解析一个数组(在这个代码片段中，我使用了 Java 13 中的 Java 文本块语法和 Java 11 中的`var`关键字):

```
var objectMapper = new ObjectMapper();var person = objectMapper.readValue("""
    ["Jane Smith", 4.2, 42]
""", Person.class);
```

这是在这种情况下出现的典型错误:

```
Cannot deserialize value of type `Person` from Array value (token `JsonToken.START_ARRAY`)
 at [Source: (String)"    ["Jane Smith", 4.2, 42]
"; line: 1, column: 5]
```

要解决这个问题，您可以向模型类添加以下两个注释。带有`shape`参数的`@JsonFormat`注释指定您期望这个对象是一个 JSON 数组:

```
[@JsonFormat](http://twitter.com/JsonFormat)(shape = JsonFormat.Shape.ARRAY)
```

`@JsonPropertyOrder`注释描述了对象属性在数组中的顺序:

```
@JsonPropertyOrder({ "name", "rate", "age" })
```

这就是你的模型类的样子:

```
@JsonFormat(shape = JsonFormat.Shape.*ARRAY*)
@JsonPropertyOrder({ "name", "rate", "age" })
class Person {

    private String name;

    private Double rate;

    private Integer age; // getters and setters omitted
}
```

如果您现在尝试使用这个类作为目标来反序列化 JSON，您将得到您所期望的结果:

```
var person = objectMapper.readValue("""
    ["Jane Smith", 4.2, 42]
""", Person.class);

System.*out*.println(person2.getName()); // -> "Jane Smith"
System.*out*.println(person2.getRate()); // -> 4.2
System.*out*.println(person2.getAge());  // -> 42
```

希望这个小技巧可以帮助您解析非标准的 JSON 数据。