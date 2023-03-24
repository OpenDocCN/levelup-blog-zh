# 使用 Kotlin 编码的技巧

> 原文：<https://levelup.gitconnected.com/tips-for-coding-with-kotlin-e90880ff5dbc>

Kotlin 是一种跨平台、静态类型的通用编程语言，具有类型推断功能。Kotlin 被设计为完全与 Java 互操作，Kotlin 的标准库的 JVM 版本依赖于 Java 类库，但类型推断允许其语法更加简洁。我使用这种语言已经有一段时间了，下面我将向初学者展示一些技巧。

![](img/402e642c745f37082296105e858de1fa.png)

由[萨法尔·萨法罗夫](https://unsplash.com/@codestorm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 应用

`apply`的一个常见用例是在最近创建的对象上进行附加配置:

```
**val** turtle = **Turtle**().**apply**{
    **Name** = "Joe"
    **Age** = 50
}
```

注意当你使用`apply`时，它会将你修改的对象返回给你。

# 随着

`with`的一个常见用例是对同一个对象调用多个方法。这样的话，这个:

```
file.**load**()
file.**modify**()
file.**modifyMore**()
file.**save**()
```

会变成这样:

```
**with**(file){
    **load**()
    **modify**()
    **modifyMore**()
    **save**()
}
```

请记住，上面返回的是最后一个表达式的结果。

# 方法使用`=`返回

如果你有一个只包含一个返回语句的方法，你可以省略返回类型、返回关键字和大括号，用`=`代替它们。这样的话，这个:

```
**fun** **sum**(**val** a: **Int**, **val** b: **Int**): **Int** {
    **return** a+b
}
```

变成了这样:

```
**fun** **sum**(**val** a: **Int**, **val** b: **Int**) = a + b
```

# 范围

范围对循环条件很有用，如下所示:

```
**for** (i **in** 1**..**5) **print**(i) *// results in "12345"*
```

然而，这并不反过来:

```
**for** (i **in** 5**..**1) **print**(i) *// prints nothing*
```

相反，下面是正确的向后迭代:

```
**for** (i **in** 5 downTo 1) **print**(i) *// results in "54321"*
```

也可以用`until`，像这样:

```
**for** (i **in** 0 until 5) { *// results in "01234"*
     **println**(i)
}
```

这通常用于循环访问集合:

```
**for**(i **in** 0 until list.**size**()){
    **println**(i)
}
```

# 当...的时候

`When`是 Java 的`switch`的一个更强大的版本，它很好地结合了范围，如下所示:

```
**when** (x) {
    **in** 0 until 5 -> **println**("x is in range")
    **is** 10 -> **println**("the special case, when x is 10")
    **else** -> **println**("none of the above")
}
```

它也可以用作 return 语句，但是您需要指定所有可能的情况(使其详尽)或者包括`else`:

```
**enum** **class** **Theme** {
    **DEFAULT**,
    **DARK**,
    **XMAS**
}**fun** **getThemeLogo**() = **when** (theme) {
    **DEFAULT** -> **getDefaultLogo**()
    **DARK** -> **getDarkLogo**()
    **XMAS** -> **getXmasLogo**()
}**fun** **getThemeLogoExcludingPromos**() = **when** (theme) {
    **DEFAULT** -> **getDefaultLogo**()
    **DARK** -> **getDarkLogo**()
    **else** -> **getDefaultLogo**()
}
```

此外，您可以使用`when`作为表达式，但在这种情况下不需要穷举:

```
**fun** **showAnimations**() {
    **when** (theme) {
        **DEFAULT** -> **showAnimation**()
        **XMAS** -> **showXmasAnimation**()
    }
}
```

最后，`when`可以与智能强制转换一起使用:

```
**when**(file) {
    **is** **Directory** -> **processDirectory**()
    **is** **Document** -> **processDocument**()
}
```

# 数据类别

数据类是具有保存数据的特定目的的类:

```
**data class** **Turtle**(**val** name: **String**, **val** age: **Int**)
```

数据类将具有以下现成的功能:

`toString`的形式有`"Turtle(name=Joe, age=50)"` `equals()`和`hashCode()` `copy()`

它也将成为析构声明的主题:

```
**val** turtleJoe = **Turtle**("Joe", 50)
**val** (name, age) = turtleJoe
**println**(name)
**println**(age)
```

# 静态字段和常数

静态字段在 Java 中很常用，但是在 Kotlin 中有点棘手。在 Kotlin 中，最接近静态字段概念的是在类内部声明的一个伴随对象。

下面是如何声明一个静态常量:

```
**class** **Theme** {
    **companion** **object** { **const** **val** THEME_KEY = "a_theme_key" }
}
```

# 零安全

Kotlin 提供了一些工具来帮助您防止最常见的运行时异常类型:`NullPointerException` s (NPEs)。

要做到这一点，您首先需要明确指定您希望某个字段/变量保存`null`:

```
var text: String // can't hold null
text = null // compilation errorvar textOrNull: String? // can hold null (specified by adding ?)
textOrNull = null // compiles just fine
```

如果您使用第二个版本，Kotlin 将期待一个空处理逻辑，并且有工具可以简化它。

外管局调用`?.`对一个可空对象执行操作:

```
**var** textOrNull: **String**?
textOrNull**?.**length *// will return length or null*
```

Elvis 操作员`?:`指定在发生`null`时采取的行动:

```
textOrNull**?.**length **?:** 0 *// will return length or 0 in case of a null*
```

还有一个`let`操作符，如果您需要一个代码块仅在某些内容不为空时执行，可以使用它:

```
currentTheme**?.let** {
    **applyTheme**(theme)*// we are certain it is not null here*
    **sendAnalyticsThemeEvent**(theme)
}
```

最后是`!!`，它将任何值转换为非空类型，如果值为`null`则抛出异常。这个操作符看起来像是空处理杂务的简单逃避，但是应该不惜一切代价避免它，因为 Kotlin 在帮助摆脱 npe 方面做了很多工作。

Kotlin 还有很多内容，但为了简单起见，这是一个很好的起点。希望这将是一个关于常用 Kotlin 实践的有用指南，帮助您入门和运行。