# 如何在 iOS 上序列化和反序列化对象

> 原文：<https://levelup.gitconnected.com/how-to-serialize-and-deserialize-objects-on-ios-a728b46e110f>

苹果喜欢用不同的方式命名一些东西，但是为序列化对象提供了很好的 API

![](img/c045b3cb38ccdc8fa2f2744cd6ee989a.png)

David Fekke 的图片说明

*原发布于*[*https://fek . io*](https://fek.io/blog/how-to-serialize-and-deserialize-objects-on-i-os/)*。*

苹果公司不仅思考方式不同，他们还喜欢给事物起不同的名字。在像 Java 和 C#这样的语言中，如果你想在保证相同接口的对象之间共享一个契约，那就叫做‘接口’。在 MacOS、iOS/iPadOS、watchOS 和 tvOS 上，这被称为“协议”。就像你可以让一个类或结构符合一个接口一样，你也可以让一个 Swift 类或结构符合一个“协议”。

在 Objective-C 中，如果你想扩展一个现有的类，那就叫做‘Category ’,但是在 C#和 Swift 中它叫做`extension`。

正在学习如何为苹果操作系统编程的开发人员可能对他们如何序列化和反序列化对象感到好奇。在 MacOS 和 iOS 上，这被称为“存档”和“取消存档”。

# 实施 ns 编码

如果您有一个想要序列化或存档的类，您的类将需要实现“NSCoding”协议。NSCoding 协议要求您的类中有一个名为`encode`的方法，以及一个`init`构造函数，该构造函数使用一个`coder`来解码存档的对象。

让我们来看看我定义的用于跟踪车辆的类。

如果您正在使用 Xcode，它有一个很好的特性，如果您正在向类中添加协议，它会提示开发人员创建任何缺少的方法。当我们将`NSObject`和`NSCoding`协议添加到我们的类中时，最终结果在我们的编辑器中将是这样的。

现在让我们实现这两个方法。对于`init?(coder: NSCoder)`,我们想让它成为一个方便的 init，因为我们想让它在被解码后调用现有的`init`构造函数。

对于`encode`方法，我们需要获取类的值，并使用传入该方法的编码器对它们进行编码。

通过添加这两个方法，我们使我们的对象可序列化或可存档，这取决于您想要使用的术语。最终的类应该类似于下面的例子。

# 安全编码

对于归档和非归档类，苹果为他们不同的操作系统提供了以下类。

*   恩斯基达尔奇维尔
*   NSKeyedUnarchiver

从 iOS 12 开始，苹果弃用了这些类中用于执行存档的一些原始方法，并添加了支持“安全编码”的新方法。苹果提供了一个新的协议，如果你想支持“安全编码”，你的对象必须支持这个协议。

这个名为`NSSecureCoding`的新协议需要将一个名为“supportsSecureCoding”的布尔属性添加到您的类中。此标志可与存档和取消存档方法的参数一起使用，以实施“安全编码”。

安全编码将使编码和解码能够防止对象替换攻击。如果发生对象替换攻击，被取消存档对象可能膨胀成可能允许攻击的对象。

让我们将`NSSecureCoding`协议添加到我们的类中。

```
class Vehicle: NSObject, NSCoding, NSSecureCoding {
    public static var supportsSecureCoding = true

    var make: String
    var model: String
    var tires: Int = 0
...
```

现在我们可以创建这个`Vehicle`类的一个新实例，并归档到一个`Data`对象。

在上面的例子中，我们可以看到`archivedData`函数有第二个参数叫做`requiringSecureCoding`。如果该函数无法归档对象，它也会抛出错误。

`flatVehicle`对象属于`Data`类型，可以写入文件系统或其他持久存储。

如果我们想对数据进行解归档，我们可以使用`NSKeyedUnarchiver`类。

# 一些其他的事情

并不是所有的类都不能使用`unarchivedObject(ofClass:from:`函数进行分类。如果您使用 Swift `Dictionary`这样的数据类型，您将不得不使用另一个函数，比如`unarchiveTopLevelObjectWithData`函数，它没有指定特定的数据类型。

如您所见，使用 NSKeyedArchiver 和 NSKeyedUnarchiver 类可以在 MAC、iPhone、iPad 和其他 Apple 设备上序列化和反序列化对象。使用这些实用程序时应小心谨慎，注意安全。