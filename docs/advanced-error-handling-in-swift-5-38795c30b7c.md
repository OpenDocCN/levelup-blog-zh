# Swift 5 中的高级错误处理

> 原文：<https://levelup.gitconnected.com/advanced-error-handling-in-swift-5-38795c30b7c>

## 试试？试试！试一试

![](img/49f1cd843bcd893f23155f87b26e8a71.png)

演职员表[pixabay.com](https://pixabay.com/photos/office-freelancer-computer-business-583839/)

在 Swift 中，有许多处理错误的方法。在本文中，我将深入解释如何使用 throwable 函数处理错误。

如果你对`JSONDecoder()`很熟悉，那么你之前就看过下面这段代码。

```
do {
    try JSONDecoder().decode(MyStruct.self, from: someData)
} catch let error {
    print(error)
}
```

这被称为 Do-Catch 块，我们被迫使用它，否则 Xcode 会报错，无法编译，并给出以下错误:

> 调用可以引发，但它没有标记为“try ”,并且不会处理该错误

这是因为方法`decode(_:)`是一个 throwable，这意味着它可能会抛出一个错误，我们不能像处理普通函数一样处理它。

为了更好地理解尝试、尝试之间的区别！，且试？让我们设计自己的 throwable 方法。

比方说，我们必须设计一个允许人们乘坐过山车的系统，我们的要求是乘坐者不能低于 120 厘米，不能高于 210 厘米。

我们要做的第一件事是编写一个 enum，并使它符合错误协议。

```
enum RiderHeightError: Error {
    case tooShortToRide
    case tooTallToRide
}
```

然后，让我们创建一个描述错误情况的计算属性。

```
enum RiderHeightError: Error {

    case tooShortToRide
    case tooTallToRide var description: String {
        switch self {
        case .tooShortToRide:
            return "You are shorter than 120 cm, try next year."
        case .tooTallToRide:
            return "You are taller than 210 cm, try another game."
        }
    }
}
```

现在我们已经准备好了错误情况的枚举，让我们写一个 throwable 函数。

```
func safetyCheck(riderHeight: Int) throws {

    if riderHeight < 120 {

        throw RiderHeightError.tooShortToRide

    } else if riderHeight > 210 { throw RiderHeightError.tooTallToRide } else { print("You are good to go. Have fun.")
    }
}
```

现在我们已经创建了一个名为`safetyCheck()`的函数。它有一个参数是骑手的高度。该功能会对骑手的身高进行两次检查，以确保其符合安全规则。

你注意到我们使用了两个新的关键词“throws”和“throw”。两者之间的区别非常简单。

“throws”意味着这个函数是可抛出的，为了使用它，你必须在函数之前写 try。

“抛出”意味着出错了，必须抛出一个特定的错误。

## 基本上有三种方法来调用 throwable 函数。

> 第一种方法是编写 Do-Catch 块，这是处理错误的推荐方法。

```
do { try safetyCheck(riderHeight: 110)} catch let riderError { if let error = riderError as? RiderHeightError {
        print(error.description)
    }
}=> **You are shorter than 120 cm, try next year.**
```

我们看到，我们捕捉到了错误，并且还获得了对错误的清晰描述。有许多方法可以编写 catch 块。下一个例子我将展示另一种方法。

```
do { try safetyCheck(riderHeight: 230)} catch RiderHeightError.tooShortToRide { print("Too Short To Ride The Roller Coaster")} catch RiderHeightError.tooTallToRide {

    print("Too Tall To Ride The Roller Coaster")}**=> Too Tall To Ride The Roller Coaster**
```

> 第二种方法是用“试试？”而不是 Do-Catch 块。这将减少几行代码，但不会告诉您到底哪里出错了。如果抛出了一个错误，它将返回零。

```
try? safetyCheck(riderHeight: 100)**=> nil**
```

如果您不太关心可能会得到什么样的错误，可以使用这种方式。你只需要知道，这个函数不是 nil，你的 app 不会崩溃。

> 第三种方法是我认为最不推荐的方法，那就是使用“尝试！”这非常类似于强制展开，其中函数值可能为零，并且您并不真正关心它是否崩溃。

```
try! safetyCheck(riderHeight: 100)**This will make your app to crash for sure.**
```

## 摘要

*   使用 enum 设计我们自己的错误。
*   处理 catch 块中引发的错误。
*   试，试的区别？&试试！