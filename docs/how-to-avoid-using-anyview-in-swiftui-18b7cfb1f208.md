# 如何避免在 SwiftUI 中使用 AnyView

> 原文：<https://levelup.gitconnected.com/how-to-avoid-using-anyview-in-swiftui-18b7cfb1f208>

SwiftUI 提供了 *type-erased 视图* AnyView，可以用作任何其他 SwiftUI 视图的包装器，例如能够从一个函数返回多种视图类型。

虽然在某些情况下使用 AnyView 可能是一个不错的选择，但苹果建议尽可能使用替代产品。

![](img/59a915fb0ab4ac01e32de3fc98411a88.png)

由于 AnyView 删除了视图的类型，它降低了 SwiftUI 有效更新视图的能力。

这是因为 SwiftUI 使用了一种所谓的*结构标识*机制，SwiftUI 使用视图的类型来识别视图，并确定何时应该更新视图。由于 AnyView 删除了视图的类型，它降低了 SwiftUI 有效更新视图的能力。

# 使用 ViewBuilder 而不是 AnyView

让我们来看一个例子，在这个例子中，我们可能会尝试使用 AnyView。

当定义返回视图的视图属性或函数时，我们经常使用 some View opaque 返回类型，因此我们不需要显式定义确切的返回类型。

```
*private* *var* nameView: *some* View { *if* isEditable { *return* TextField("Your name", text: $name) } *else* { *return* Text(name) }}
```

但是上面的代码无法编译，并显示错误消息*函数声明了一个不透明的返回类型，但是其主体中的返回语句没有匹配的底层类型*。

为了解决错误并为两个视图返回相同的类型，我们可能会尝试将它们包装在 AnyView 中。

```
*private* *var* nameView: *some* View { *if* isEditable { *return* AnyView(TextField("Your name", text: $name)) } *else* { *return* AnyView(Text(name)) }}
```

但是有一个更好的解决编译器错误的方案——**view builder 属性**。

```
@ViewBuilder*private* *var* nameView: *some* View { *if* isEditable { TextField("Your name", text: $name) } *else* { Text(name) }}
```

ViewBuilder 属性允许我们将多个视图组合成一个返回类型。

我们所要做的就是将属性添加到我们的属性或函数中，并删除返回语句。

这是 SwiftUI 视图主体使用的相同机制。唯一的区别是，我们必须明确地在我们自己的属性和函数上添加 ViewBuilder 属性。

# 使用 Group 而不是 AnyView

通过使用*组*类型，我们可以将多个视图收集到一个视图中，而不会影响这些视图的布局。

```
*private* *var* nameView: *some* View { Group { *if* isEditable { TextField("Your name", text: $name) } *else* { Text(name) } }}
```

由于 Group 使用 ViewBuilder，我们可以用条件语句对不同种类的视图进行分组。

# 使用泛型而不是 AnyView

另一个我们可能想使用 AnyView 的常见情况是当我们想存储一个视图而不知道它的类型时。

```
*struct* FlyoutView: View { *let* headerView: AnyView *var* body: *some* View { VStack { headerView Spacer() // other views } }}
```

在上面的例子中，我们希望 headerView 是一个灵活的类型。由于我们不能对存储的属性使用 some View opaque 返回类型，AnyView 似乎是一个不错的选择。

更优雅、更高效的解决方案是使用泛型。

```
*struct* FlyoutView<HeaderView> : View *where* HeaderView : View { *let* headerView: HeaderView *var* body: *some* View { VStack { headerView Spacer() } }}
```

现在，当创建一个弹出视图时，我们不需要在任何视图中包装标题视图。

```
FlyoutView(headerView: Text("Header view"))
```

SwiftUI 提供的许多视图都使用相同的泛型方法。

```
*struct* VStack<Content> : View *where* Content : View {}
```

正如我们在上面看到的，VStack 的内容视图是符合 view 的通用类型。

# 结论

在大多数情况下，可以避免使用 AnyView。探索的替代方案不仅为我们的 SwiftUI 视图增加了更多性能，还产生了更优雅的可读代码。

*最初发表于*[*【https://tanaschita.com】*](https://tanaschita.com/20210802-how-to-avoid-using-anyview-in-swiftui/)*。*