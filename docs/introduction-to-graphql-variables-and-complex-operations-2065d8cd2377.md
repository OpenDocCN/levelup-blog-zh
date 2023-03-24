# GraphQL 简介—变量和复杂运算

> 原文：<https://levelup.gitconnected.com/introduction-to-graphql-variables-and-complex-operations-2065d8cd2377>

![](img/677794e13df1cb4b689893c1018e63b3.png)

照片由 [Eric Prouzet](https://unsplash.com/@eprouzet?utm_source=medium&utm_medium=referral) 拍摄于 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

GraphQL 是我们的 API 的一种查询语言，也是一个服务器端运行时，通过使用数据的类型系统来运行查询。

在本文中，我们将研究更复杂的 GraphQL 操作，包括传入变量、指令、突变等等。

# 变量

在大多数应用程序中，查询的参数需要是动态的。将动态参数直接传递给查询字符串并不是一个好主意。

幸运的是，GraphQL 允许我们在操作请求中传递变量。

例如，我们可以写:

```
query PersonName($id: Int) {
  person(id: $id) {
    name
  }
}
```

将一个`$id`变量传入我们的`person`查询。

然后我们可以通过一个对象传入变量来发出请求:

```
{
  "id": 1000
}
```

然后我们会得到这样的结果:

```
{
  "data": {
    "person": {
      "name": "Jane"
    }
  }
}
```

作为回应。

# 变量定义

变量定义以前缀为`$`的变量名开始，然后是变量的类型。

在上面的例子中，我们用`$id`作为变量名，用`Int`作为类型。

所有声明的变量必须是标量、枚举或其他输入对象类型。

如果我们想将一个复杂的对象传递到一个字段中，我们需要知道服务器上匹配的输入类型。

# 默认变量

我们可以如下设置变量的默认值:

```
query PersonName($id: Int = 1) {
  person(id: $id) {
    name
  }
}
```

在上面的代码中，我们在`$id: Int`后添加了`= 1`，将`$id`的默认值设置为 1。

# 指令

我们可以使用指令来动态地改变使用变量的查询的结构和形状。

例如，我们可以包含如下指令，使我们的查询是动态的:

```
query Person($id: Int, $withFriends: Boolean!) {
  person(id: $id) {
    name
    friends @include(if: $withFriends) {
      name
    }
  }
}
```

在上面的代码中，如果`$withFriends`是`true`，我们有`@include(if: $withFriends)`指令来有条件地包含`friends`。

因此，如果我们使用以下变量进行以下查询:

```
{
  "id": 1000,
  "withFriends": false
}
```

那么我们可能会得到这样的结果:

```
{
  "data": {
    "person": {
      "name": "Jane"
    }
  }
}
```

作为回应。

指令可以附加到字段或片段包含中，并且可以以服务器期望的任何方式影响查询的执行。

核心的 GraphQL 规范包括两个指令，任何符合规范的 GraphQL 服务器实现都必须支持这两个指令:

*   `@include(if: Boolean)` —仅在参数 if `true`时包含该字段。
*   `@skip(if: Boolean)` —如果参数为`true`，则跳过此字段。

它帮助我们摆脱了在查询中添加和删除字段时必须进行字符串操作的情况。

![](img/eec6450ef6b00459e4eee62bc3cd087a.png)

照片由[raphal Biscaldi](https://unsplash.com/@les_photos_de_raph?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 突变

我们可以使用突变发送请求来改变服务器上的数据。

它们类似于查询，除了它以关键字`mutation`开始。

例如，我们可以如下定义突变:

```
mutation CreatePerson($firstName: String, $lastName: String) {
  createPerson(firstName: $firstName, lastName: $lastName) {
    firstName
    lastName
  }
}
```

然后，如果我们进行以下查询:

```
{
  "firstName": "Joe",
  "lastName": "Smith"
}
```

我们可能会得到以下回应:

```
{
  "data": {
    "createPerson": {
      "firstName": "Joe",
      "lastName": "Smith"
    }
  }
}
```

在响应中，我们返回请求中指定的`firstName`和`lastName`字段。

像查询一样，一个变异可以包含多个字段。突变字段连续运行。

这意味着如果我们在一个请求中发送 2 个`createPerson`突变。第一个会在第二个开始之前结束。

# 内嵌片段

我们可以用 GraphQL 请求定义接口和联合类型。

为此，我们使用内联片段来访问底层具体类型上的数据。

例如，我们可以编写以下查询:

```
query Thing($id: Int!) {
  person(id: $id) {
    name
    ... on Person{
      gender
    }
    ... on Robot{
      memory
    }
  }
}
```

在上面的查询中，`...on`操作符表示我们在查询中包含了一个内联片段，其中`Person`和`Robot`是我们的片段。

命名片段也可以是内联片段，因为它们附加了一个类型。

# 元字段

我们可以请求一个`__typename`字段来获取响应中返回的数据类型。

例如，如果我们有以下查询:

```
{
  search(text: "an") {
    __typename
    ... on Human {
      name
    }
    ... on Robot {
      name
    }
  }
}
```

那么我们可能会从服务器得到以下响应:

```
{
  "data": {
    "search": [
      {
        "__typename": "Human",
        "name": "Hans"
      },
      {
        "__typename": "Robot",
        "name": "Jane"
      }
    ]
  }
}
```

# 结论

我们可以定义突变来请求改变数据。

为了使请求动态化，我们可以使用变量来包含变量数据，并使用指令在响应中有条件地包含数据。

我们还可以对联合类型使用内联片段，并通过包含`__typename`属性在响应中包含返回的数据类型。