# 如何在不打印的情况下格式化 Go 字符串

> 原文：<https://levelup.gitconnected.com/how-to-format-go-string-without-printing-d3e3d8c1fc1e>

![](img/0d1aa2b86332f72240d77e2efcc55912.png)

[Nathan Fertig](https://unsplash.com/@nathanfertig?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

寻找快速答案？这里，

```
s := fmt.Sprintf("When old is %s? its %d years old.", "Eiffel tower", 135)
```

对于“简单”的字符串，最简单的方法是使用`[fmt.Sprintf()](https://golang.org/pkg/fmt/#Sprintf)`及其不同的版本，如`[fmt.Sprint()](https://golang.org/pkg/fmt/#Sprint)`、`[fmt.Sprintln()](https://golang.org/pkg/fmt/#Sprintln)`。这些函数返回结果`string`，而不是将它们打印到标准输出。

**提示:**要连接不同数据类型的值，可以使用`Sprint()`

示例:

```
i := 135
str := fmt.Sprint("years old :", i) // str will be "years old :135"
```

可以在连接字符串时使用`[strings.Join()](https://golang.org/pkg/strings/#Join)`，也可以指定一个自定义分隔符`string`放置在字符串之间。

# **高级格式:**

如果你想要创建的字符串更复杂，比如一封多行的电子邮件，使用`fmt.Sprintf()`会变得更难阅读，并且使用更多的内存，尤其是如果你必须做很多的话。

标准库为此提供了包`[text/template](https://golang.org/pkg/text/template/)`和`[html/template](https://golang.org/pkg/text/template/)`。为了生成文本输出，这些包将数据驱动的模板放入 action.HTML 输出中，这样可以避免代码注入，并且可以使用`[html/template](https://golang.org/pkg/text/template/)`生成。它有与包`[text/template](https://golang.org/pkg/text/template/)`相同的用户界面，可以用于 HTML 输出。

使用提供字符串形式的静态模板所需的模板包，该静态模板可能包含静态文本和动作，这些文本和动作在模板被处理并生成输出时被处理。

可以在生成时提供模板中包含/替换的参数。通常，这样的参数是结构和映射值。

**例如，**如果我们需要生成以下需要打印成 pdf 的消息:

```
Hello [name]

Thanks for enrolling into class [year]

You have enrolled to following classes for this year:
[class#1], [class#2], ... [class#n]
```

要生成这样的消息体，可以使用以下模板:

```
const welcomeTemplate = `Hello {{.Name}}

Your account is ready, your user name is: {{.UserName}}
Thanks for enrolling into class {{.Year}}

You have enrolled to following classes for this year:
{{range $i, $c := .Classes}}{{if $i}}, {{end}}{{.}}{{end}}
`
```

并按如下方式注入数据:

```
data := map[string]interface{}{
    "Name":     "Rakesh",
    "Year": "2022",
    "Classes":    []string{"ds", "ui/ux", "algorithms"},
}
```

模板的输出被写入一个`[io.Writer](https://golang.org/pkg/io/#Writer)`，如果我们需要结果作为一个`string`，可以创建并写入一个实现`io.Writer`的`[bytes.Buffer](https://golang.org/pkg/bytes/#Buffer)`。

用数据执行上面的模板将产生`string`

```
t := template.Must(template.New("letter").Parse(welcomeTemplate))
buf := &bytes.Buffer{}
if err := t.Execute(buf, data); err != nil {
    panic(err)
}
s := buf.String()
```

这将产生预期的输出:

```
Hello Rakesh,

Thanks for enrolling into class 2022

You have enrolled to following classes for this year:
ds, ui/ux, algorithms
```

# 结论

我们看到了两种格式化字符串的方法，如果您对 Go 中的片段连接有任何进一步的见解，欢迎留下您的评论。

编码快乐！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)