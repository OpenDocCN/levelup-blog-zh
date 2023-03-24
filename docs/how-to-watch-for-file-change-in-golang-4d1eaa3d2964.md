# 如何在 Golang 中查看文件更改

> 原文：<https://levelup.gitconnected.com/how-to-watch-for-file-change-in-golang-4d1eaa3d2964>

![](img/4499618d1de7933389f35344b1051e09.png)

Patrick Perkins 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在开发环境中，观察本地文件系统的变化是非常常见的。在谷歌搜索了几个小时后，我找到了一个简单的工具来做这件事。

这个工具叫做 [fsnotify](https://github.com/fsnotify/fsnotify) ，一个跨平台的文件系统通知工具。它提供了一个简单的界面来观察本地文件系统中的变化。

让我们看看如何在这个帖子中使用它。

# 装置

首先，安装非常简单。

```
$ go get github.com/fsnotify/fsnotify
```

# 关键类型

让我们熟悉一下所有类型的 fsnotify 工具。

## 事件结构

事件结构表示单个文件系统通知。函数`String()`以“file: REMOVE|WRITE|…”的形式返回事件的字符串表示。

```
type Event struct {
	Name [string](https://pkg.go.dev/builtin#string)      // Relative path to the file or directory.
	Op   [Op](https://pkg.go.dev/github.com/fsnotify/fsnotify#Op)          // File operation that triggered the event.
}func (e [Event](https://pkg.go.dev/github.com/fsnotify/fsnotify#Event)) String() [string](https://pkg.go.dev/builtin#string)
```

## Op 类型

该工具描述了一组文件操作。它们是可以触发通知的通用文件操作。

```
type Op [uint32](https://pkg.go.dev/builtin#uint32)const (
	Create [Op](https://pkg.go.dev/github.com/fsnotify/fsnotify#Op) = 1 << [iota](https://pkg.go.dev/builtin#iota)
	Write
	Remove
	Rename
	Chmod
)
```

## 观察器结构

`Watcher`结构是包的核心。它包含两个通道和三个功能。

```
type **Watcher** struct {
	Events chan [Event](https://pkg.go.dev/github.com/fsnotify/fsnotify@v1.5.1#Event)
	Errors chan [error](https://pkg.go.dev/builtin#error) // contains filtered or unexported fields
}func (w *[Watcher](https://pkg.go.dev/github.com/fsnotify/fsnotify@v1.5.1#Watcher)) **Add**(name [string](https://pkg.go.dev/builtin#string)) [error](https://pkg.go.dev/builtin#error)func (w *[Watcher](https://pkg.go.dev/github.com/fsnotify/fsnotify@v1.5.1#Watcher)) **Remove**(name [string](https://pkg.go.dev/builtin#string)) [error](https://pkg.go.dev/builtin#error)func (w *[Watcher](https://pkg.go.dev/github.com/fsnotify/fsnotify@v1.5.1#Watcher)) **Close**() [error](https://pkg.go.dev/builtin#error)
```

**频道:**

*   `Event`渠道
*   `Errors`渠道

**功能:**

*   `Add`:开始监视指定的文件或目录(非递归)。
*   `Remove`:停止监视指定的文件或目录(非递归)。
*   `Close`:移除所有手表，关闭事件频道。

## 创建观察者

函数`NewWatcher`用底层操作系统建立一个新的观察器，并等待事件。

```
func **NewWatcher**() (*[Watcher](https://pkg.go.dev/github.com/fsnotify/fsnotify@v1.5.1#Watcher), [error](https://pkg.go.dev/builtin#error))
```

# 怎么用？

下面是观看一个文件的一步一步的流程。

*   在第 10 行创建一个新的观察者
*   在第 37 行添加要监视的本地文件路径
*   启动一个新的 goroutine 来监视第 22 行事件通道中的文件更改事件

发现这篇文章很有用👏？看看我下面的其他文章吧！

1.  [Golang 频道是如何工作的](/how-does-golang-channel-works-6d66acd54753)
2.  [Golang 中的观察者设计模式与实例](/observer-design-pattern-in-golang-with-an-example-6c24898059b1)
3.  [用实例解释戈朗语中的固体原理](/solid-principles-in-golang-explained-by-examples-4a4cccf47388)