# 使用 JSON —数据类型和模式

> 原文：<https://levelup.gitconnected.com/working-with-json-data-types-and-schemas-ba11121aa4dc>

![](img/eb46227e6245b0f56cd0df4c12343ea0.png)

由 [David Vig](https://unsplash.com/@davidvig?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将看看如何使用 JSON。

# JSON 数字数据类型

JSON 支持数字。我们只是把它们作为值相加。

例如，我们可以写:

```
{
    "latitude": 49.606209,
    "longitude": -122.332071
}
```

将数字作为值相加。

JSON 中有十进制数。我们也可以有整数。

# JSON 布尔数据类型

JSON 对象也可以有布尔值。布尔值可以有值`true`或`false`。

例如，我们可以写:

```
{
    "toastWithBreakfast": false,
    "breadWithLunch": true
}
```

将它们作为值相加。

# JSON 空数据类型

空数据类型可以有值`null`。

它用来表示什么都没有。

例如，我们可以写:

```
{
    "freckleCount": 1,
    "hairy": false,
    "color": null
}
```

然后我们将`color`的值设置为`null`，这意味着没有设置颜色。

# JSON 数组数据类型

JSON 支持数组来存储数据集合。

例如，我们可以写:

```
{
    "fruits": [
        "apple",
        "orange",
        "grape"
    ]
}
```

将`fruits`属性添加到 JSON 中，并将值设置为一个数组，并用方括号将值括起来。

我们可以向数组添加`null`来添加空值:

```
{
    "fruits": [
        "apple",
        "orange",
        null,
        "grape"
    ]
}
```

我们可以像 JavaScript 数组一样在 JSON 数组中混合和匹配数据类型。

JSON 数组可以在一个对象中包含任何 JSON 支持的数据类型。

所以我们可以写:

```
{
    "scores": [
        92.5,
        62.7,
        84.6,
        92
    ]
}
```

或者

```
{
    "answers": [
        true,
        false,
        false,
        true,
        false,
        true,
        true
    ]
}
```

或者

```
{
    "students": [
        "james smith",
        "bob jones",
        "jane smith"
    ]
}
```

数组中也可以有数组。

例如，我们可以写:

```
{
    "tests": [
        [
            true,
            false,
            false
        ],
        [
            true,
            true,
            false
        ],
        [
            true,
            false,
            true
        ]
    ]
}
```

我们在一个数组中添加布尔值，然后将数组放在它外面的另一个数组中。

# JSON 模式

JSON 模式让我们可以检查我们的 JSON 结构是否符合我们的期望。

我们可以验证数据与模式的一致性，并修复发现的错误。

验证错误让我们修复发现的错误。

这让我们对数据充满信心。

要将 JSON 模式添加到我们的对象中，我们可以编写:

```
{
    "$schema": "http://json-schema.org/draft-04/schema#",
    "title": "Person",
    "properties": {
        "name": {
            "type": "string"
        },
        "age": {
            "type": "number",
            "description": "Your person's age in years."
        },
        "gender": {
            "type": "string"
        }
    }
}
```

我们用`$schema`属性定义了模式对象。

然后我们使用`title`将标题添加到模式中。

然后我们有了带有 JSON 对象中允许的属性的`properties`对象，我们用它来检查这个模式。

例如，如果我们有:

```
{
    "name": "james",
    "age": 20,
    "gender": "male"
}
```

那么这符合我们的模式。

# 结论

JSON 允许我们在 JSON 对象中使用各种数据类型。

此外，我们可以创建 JSON 模式来验证对象。