# 用替换器替换 Golang 中的多字符串

> 原文：<https://levelup.gitconnected.com/multi-string-replace-in-golang-with-replacer-148d4173f439>

![](img/60ebc91ddecd5947dab0979ec25cc2e7.png)

使用`[strings](https://pkg.go.dev/strings)`包，Golang 中的字符串替换非常容易。在他们的文档中:“包字符串实现了简单的函数来操作 UTF-8 编码的字符串”。拿下面这句话来说:

```
"Hello World!"
```

现在假设我的产品负责人认为用感叹号说“你好，世界”太多了，如果我发现我需要用一个`.`替换`!`，这在 Golang 中很容易做到:

```
myString := "Hello World!"myString = strings.ReplaceAll(myString, "!", ".")fmt.Println(myString)
```

这将导致`Hello World.`被打印出来，你可以在这里[测试](https://go.dev/play/p/bBlbOqCfSoE)。如你所见，使用`strings.ReplaceAll`非常简单:

*   提供要操作的字符串
*   提供要替换的字符
*   提供替换价值

现在你知道如何用 Golang 替换字符串了，恭喜你！边注，还有一个`[strings.Replace](https://pkg.go.dev/strings#Replace)`，你可以控制在你的字符串中发生多少次替换。

# 当事情没那么简单的时候

替换很好，但是如果你的字符串看起来像这样呢:

```
"Hello_World!_This.sentence.has_chars_I.want_to.remove!"
```

我们产品负责人说这句话不会飞，需要用空格替换 and `_`和任何`.`。既然我们知道如何使用`strings.ReplaceAll`，我们可以很容易地做这样的事情:

```
myString := "Hello_World!_This.sentence.has_chars_I.want_to.remove!"myString = strings.ReplaceAll(myString, "_", " ")
myString = strings.ReplaceAll(myString, ".", " ")fmt.Println(myString)
```

这将导致`Hello World! This sentence has chars I want to remove!`被打印出来，你可以在这里[测试](https://go.dev/play/p/wN4ajNm6hJ8)。在这个例子中，我们仍然有一个相当简单的句子，用两个不同的字符替换。这并不坏，我们可以很容易地使用两条`strings.ReplaceAll`语句实现我们的目标。

但是，如果我们需要替换 5 个、10 个或 20 个字符呢？我们可以有 20 个`strings.ReplaceAll`语句，它会工作得很好，但是很麻烦。或者，如果我需要在多个函数中执行这种替换，该怎么办？这可能会很快变得一团糟。让我们看看更好的方法。

# 使用替代品

`strings`包包括一个`[strings.Replacer](https://pkg.go.dev/strings#Replacer)`选项。在他们的文档中:“Replacer 用替换来替换字符串列表。多个 goroutines 同时使用是安全的”。基本上，您可以指定要执行的替换列表，然后针对您的目标字符串一次完成所有替换:

```
myString := "Hello_World!_This.sentence.has_chars_I.want_to.remove!"replacer := strings.NewReplacer("_", " ", ".", " ")myString = replacer.Replace(myString)fmt.Println(myString)
```

这将导致`Hello World! This sentence has chars I want to remove!`被打印出来，你可以[在这里测试](https://go.dev/play/p/Aehy-zlKdSp)。使用 Replacer 函数也非常简单:

*   用您想要替换的所有字符创建一个新的替换符
*   把它应用到你的弦上

在这个例子中，它没有为我们节省任何代码行，但是没关系，这是一个简单的例子。但是这给我们设置了更复杂场景，比如:

*   需要替换 5，10，20 个不同的字符
*   需要将 replacer 传递给任何需要执行完全相同的替换的函数

# 包扎

这篇文章展示了`strings`包中 Replacer 功能的简单用法。使用它，你可以轻松快速地替换单个字符串中的多个字符。希望有帮助！

## 参考

*   弦包:[https://pkg.go.dev/strings](https://pkg.go.dev/strings#Replacer)
*   replace all:[https://pkg.go.dev/strings#ReplaceAll](https://pkg.go.dev/strings#ReplaceAll)
*   更换:[https://pkg.go.dev/strings#Replace](https://pkg.go.dev/strings#ReplaceAll)
*   替换者:[https://pkg.go.dev/strings#Replacer](https://pkg.go.dev/strings#Replacer)

# 分级编码

感谢您成为我们社区的一员！更多内容见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在转型理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)