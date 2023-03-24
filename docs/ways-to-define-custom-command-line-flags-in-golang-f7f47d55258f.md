# 在 Golang 中定义自定义命令行标志的方法

> 原文：<https://levelup.gitconnected.com/ways-to-define-custom-command-line-flags-in-golang-f7f47d55258f>

![](img/3ba6ebc03c58cfcbad8825af3ddedc65.png)

*Photo by*[*@ Joshua fuller*](https://unsplash.com/@joshuafuller)*on Unsplash*

# 介绍

如果您熟悉命令行界面，您可能会看到类似于`-h`、`-v`、`--name foo`等标志。一些标志使用原始数据类型，如`string`、`int`、`bool`等。其余的标志是使用特定模式或数据结构的自定义标志，如`IP`、`URL`、`Array`、`Map`等。Golang 提供了一个名为`flag`的内置库来定义命令行标志。提供的一些定义标志的函数非常简单，如`flag.String`、`flag.Int`等。但是，自定义的旗帜呢？在本文中，您将学习一些在 Golang 中定义自定义命令行标志的方法。

# 例子

假设您想要创建一个接受字符串值列表的自定义标志。标志的用法应该是这样的:

```
$ go run main.go --list a --list b,c,d
[a b c d]
```

下面是一些定义自定义标志的方法。

# 标志变量

`flag.Var`是定义自定义标志的一种方式。它接受实现`flag.Value`接口的自定义结构。

```
type ListFlag []stringfunc (f *ListFlag) String() string {
 b, _ := json.Marshal(*f)
 return string(b)
}func (f *ListFlag) Set(value string) error {
 for _, str := range strings.Split(value, ",") {
  *f = append(*f, str)
 }
 return nil
}func main() {
    // foo and bar as a default value
 list := ListFlag([]string{"foo", "bar"})
 flag.Var(&list, "list", "your flag usage") flag.Parse()
 fmt.Println(list) // [foo bar a b c d]
}
```

需要注意的重要一点是`Set`方法。每次调用您的标志时，都会调用`Set`方法。

# 标志功能

`flag.Func`是一种定义自定义标志的简单方法。它使用了`flag.Var`,所以你不需要创建一个定制的结构。

```
func main() {
 list := ListFlag([]string{"foo", "bar"})
 flag.Func("list", "your flag usage", func(s string) error {
  for _, str := range strings.Split(s, ",") {
   list = append(list, str)
  }
  return nil
 }) flag.Parse()
 fmt.Println(list) // [foo bar a b c d]
}
```

如您所见，`flag.Func`的最后一个参数是您在前一个示例中实现的一个`Set`方法。

# 标志文本变量

`flag.TextVar`是`go1.19`中引入的新方式。它像`flag.Func`一样使用了引擎盖下的`flag.Var`，但它接受了一个`encoding.TextUnmarshaler`和`encoding.TextMarshaler`接口，而不是`flag.Value`接口。这意味着您可以使用像`big.Int`、`netip.Addr`和`time.Time`这样的内置结构作为标志，而无需实现自定义结构。

```
type ListFlag []stringfunc (f *ListFlag) MarshalText() ([]byte, error) {
 return json.Marshal(*f)
}func (f *ListFlag) UnmarshalText(b []byte) error {
 for _, str := range strings.Split(string(b), ",") {
  *f = append(*f, str)
 }
 return nil
}func main() {
 list := ListFlag([]string{"foo", "bar"})
 flag.TextVar(&list, "list", &list, "your flag usage") flag.Parse()
 fmt.Println(list)
}
```

如你所见，它与`flag.Var`相似，但它使用`MarshalText`和`UnmarshalText`而不是`String`和`Set`方法。

# 结论

现在您知道了一些在 Golang 中定义自定义命令行标志的方法。如果你想了解更多关于`flag`包的信息，可以在这里阅读官方文档[。](https://pkg.go.dev/flag)

感谢您的阅读！

*原载于 2022 年 9 月 3 日*[*https://clavinjune . dev*](https://clavinjune.dev/en/blogs/ways-to-define-custom-command-line-flags-in-golang/)*。*

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)