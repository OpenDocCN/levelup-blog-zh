# 开始编程|实例化单例对象

> 原文：<https://levelup.gitconnected.com/go-programming-instantiating-a-singleton-object-3e3a425346e4>

![](img/82278f42f62add755b6957ca55176e18.png)

西蒙·伯杰在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

实例化单例对象是一种确保只创建一个类实例的方法。

在这篇文章中，我将展示实例化单例对象的不同方法。

## 1.包装级别变量

变量可以被同一个包中的所有文件访问。在不同的功能之间共享数据是有帮助的。

我们可以通过给包范围内的变量赋值来创建一个单例对象。

例如:

*"abc.go"*

```
package abcimport "time"var StartTime = time.Now()
```

*"main.go"*

```
package mainimport (
    "fmt"
    "time"
    "example.com/m/abc"
)func main() {
    fmt.Println(abc.StartTime)
    time.Sleep(1 * time.Second)
    fmt.Println(abc.StartTime)
}
```

“结果”:

```
2022-05-10 11:58:06.076867 +0800 CST m=+0.000095853
2022-05-10 11:58:06.076867 +0800 CST m=+0.000095853
```

正如我们所料，单例对象`StartTime`在两次连续调用中是相同的。

## 2.初始化功能

函数`init`是一个预定义的函数，在导入包时调用。它通常用于初始化包的特定状态。

*"abc.go"*

```
package abcimport "time"var StartTime time.Time func init() {
    StartTime = time.Now()
}
```

## 3.主函数中的函数调用

在主函数开始时，我们调用`initApp`函数来初始化一些变量。

*"main.go"*

```
package mainimport (
    "fmt"
    "time"
    )var startTime time.Timefunc initApp() {
    startTime = time.Now()
}func main() {
    initApp()

    fmt.Println(startTime)
}
```

## 4.`sync.Once`

有时，将昂贵的初始化步骤推迟到需要它的时候是一个好的做法。

`sync.Once`包是一个完美的工具来完成这种惰性初始化，并确保我们只运行一次初始化代码**。**

*"abc.go"*

```
package abcimport (
    "sync"
    "time"
)var (
    StartTime time.Time
    once      sync.Once
)func InitStartTime() {
    once.Do(func() { 
        StartTime = time.Now()
    })
}
```

" main.go "

```
package mainimport (
    "fmt"
    "example.com/m/abc"
)func main() {
    fmt.Println(abc.StartTime)
    abc.InitStartTime()
    fmt.Println(abc.StartTime)
}
```

# 最后

实例化单例对象是一种超级基本的编程模式；我希望你喜欢这篇文章:)

感谢阅读。