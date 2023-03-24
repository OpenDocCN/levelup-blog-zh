# Go 1.16 嵌入:第一印象

> 原文：<https://levelup.gitconnected.com/go-1-16-embed-first-impressions-464e07020cca>

![](img/e58bbaaa3f60088d7b8b823cc1de95f3.png)

Go 1.16 增加了[嵌入包](https://golang.org/doc/go1.16#library-embed)。理论上，它为我以前如何实现一些东西提供了一个更好的解决方案，所以我尝试了一下。

## 形势

所以，我有一个大的字符串模板，我用它来生成一些输出。之前我做了以下工作:

```
const (
  TemplateStr = `blah blah blah some big string contents goes here ... Mauris mollis neque nisl, sit amet facilisis nulla finibus ut. Aliquam massa metus, imperdiet a finibus accumsan, interdum vitae felis. Ut et dapibus purus, sed mattis nunc. Mauris dui nunc, suscipit consectetur vulputate sit amet, luctus feugiat leo. Quisque fermentum lacus vitae facilisis feugiat. Nulla ut turpis ac nisl faucibus aliquet vitae eu dolor. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos. Suspendisse vitae tempor mi, eu posuere nisi.`
)
```

简单地说，我在 Go 文件中创建了一个又大又丑的字符串常量。

用*嵌入*肯定更好；

```
//go:embed template.txt
var TemplateStr string
```

然后一个文件 *template.txt* 里面有内容。非常好。

## 非常好，但不完美…

要嵌入文件，你需要使用一个*变量*。从实际实现的角度来看，我可以理解为什么。 *var* 语法提供类型信息，没有 const 的特定类型约束，也不需要初始值。但是在一个完美的世界里，如果我嵌入一个文件的内容，它很可能被用作一个不可变的值，也就是一个常量。我希望能够将变量保留为常量，并使用 embed 来设置它。