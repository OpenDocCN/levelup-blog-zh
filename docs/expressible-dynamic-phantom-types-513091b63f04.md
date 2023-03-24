# 可表达的动态幻象类型

> 原文：<https://levelup.gitconnected.com/expressible-dynamic-phantom-types-513091b63f04>

![](img/79747d03caae926a2d7b8b5ca9b7b9ea.png)

由[串联 X 视觉效果](https://unsplash.com/@tandemxvisuals?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/ghost?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

**虚拟类型**是定制类型，具有一个或多个*未使用的*类型参数。

最简单的例子是:

```
**struct** Phantom<Context, WrappedValue> {
  **let** wrappedValue: WrappedValue
}
```

这为什么有用？这有助于我们确保开发人员不会意外地在字段中放入意外的值，例如在`lastName`字段中放入`firstName`可能会受到这样的限制…

```
**enum** PhantomTypes {
  **enum** FirstName {}
  **enum** LastName {}
}**typealias** FirstName = Phantom<PhantomTypes.FirstName, String>
**typealias** LastName = Phantom<PhantomTypes.LastName, String>**struct** Client {
  **let** firstName: FirstName
  **let** lastName: LastName
}
```

# 类型安全

所以现在开发人员实际上不能在编译器不抛出类型不匹配错误的情况下执行`firstName = lastName`。

如果我们有两个具有`firstName`属性的实体会怎么样？有什么能阻止我把客户的`firstName`设置成制片人的`firstName`？我们可以在第一个未使用的属性上引入幻影类型的嵌套。

```
**enum** PhantomTypes {
  **enum** FirstName {}
  **enum** LastName {}
}**protocol** FirstNameHaving {}
**extension** FirstNameHaving {
  **typealias** FirstName = Phantom<Phantom<Self, FirstName>, String>
}**struct** Client: FirstNameHaving {
  **let** firstName: FirstName
}**struct** Producer: FirstNameHaving {
  **let** firstName: FirstName
}client.firstName = producer.firstName **// type-mismatch**
```

这太棒了！我们可以确保在编译时正确设置我们的值，而不必编写任何值映射器的测试。

然而，这些仍然有点笨重。如果你想在上面创建一个`Producer`，你必须写:

```
Producer(firstName: FirstName("firstName"))
```

如果有一种方法可以隐藏我们正在使用的自定义类型，并且只向`firstName`属性提供内容就好了...

# expressable by…Literal

谢天谢地，有！这里有一个`ExpressibleByStringLiteral`的例子。

```
**extension** Phantom: ExpressibleByStringLiteral **where** WrappedValue: ExpressibleByStringLiteral {
  **init**(stringLiteral value: StringLiteralType) {
    **self** = **Self**(WrappedValue(stringLiteral: value **as**! WrappedValue.StringLiteralType))
  }
}
```

现在我们可以这样写上面的初始化式:

```
Producer(firstName: "firstName")
```

> 注意:它仍然是一个`FirstName`，你仍然可以传入一个`FirstName`对象。

还有一个缺点，如果我们想检查`firstName`是否不为空(举例来说),我们必须编写如下代码:

```
producer.firstName.wrappedValue.isEmpty
```

如果我们能去掉那个`wrappedValue`就太好了，这样我们对幻影类型的使用就透明了…

# @ dynamicMemberLookup

语法糖完成了拼图的最后一块，下面是我们如何将它添加到我们的`Phantom`结构中:

```
**@dynamicMemberLookup
struct** Phantom<Context, WrappedValue> {
  **var** wrappedValue: WrappedValue
  **init**(_ wrappedValue: WrappedValue) {
    **self**.wrappedValue = wrappedValue
  } **subscript**<T>(dynamicMember member: KeyPath<WrappedValue, T>) -> T {
    **get** { **return** wrappedValue[keyPath: member] }
  }
}
```

这允许我们直接从`Phantom`类型访问`WrappedValue`的属性，例如:

```
producer.firstName.isEmpty
```

# @属性包装器

我们可以通过引入带`WrappedType`的`@propertyWrappers`来更进一步。

```
**struct** Producer: FirstNameHaving {
  **@Truncated**(maxLength: 10) 
  **var** firstName: FirstName
}producer.firstName = "Christopher"
print(producer.firstName) // "Christophe"
```

感谢阅读！

我已经创建了一个 Swift 包，其中包含上述内容以及更多的协议一致性，请查看……[https://github.com/cjnevin/PhantomTypes](https://github.com/cjnevin/PhantomTypes)