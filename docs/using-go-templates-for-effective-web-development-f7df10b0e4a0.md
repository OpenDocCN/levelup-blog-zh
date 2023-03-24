# 在 Go 中使用嵌套模板实现高效的 Web 开发

> 原文：<https://levelup.gitconnected.com/using-go-templates-for-effective-web-development-f7df10b0e4a0>

![](img/60e050c566a8bed953e787a07869a3be.png)

尽管我很喜欢 Go 语言及其标准库文档，但我总是很难准确掌握如何使用标准模板包。Go 模板库小巧、简洁、高性能——Go 本身的所有优点。对于简单的模板插值，如邮件合并或呈现单个 HTML 页面，它的效用是显而易见的。

然而，当我试图在模板中包含其他模板时，我发现自己很快陷入了困境。考虑到使用嵌套模板是一个相当标准的 web 设计模式，我觉得我好像遗漏了一些基本的东西。粗略地搜索一下文档，可以发现在模板标签中使用了一个`template`函数，它带有一个“模板名”和一个可选的数据指针。然而，这就是困惑的开始。考虑以下代码:

```
package mainimport (
 "text/template"
 "os"
)func main() {
 tmpl := "Hello, {{ .Name }}"
 t, _ := template.New("greeting").Parse(tmpl)
 t.Execute(os.Stdout, struct{ Name string }{"Prince Adam"})
}
```

这个例子看起来很简单。我们声明我们的文本字符串，它将作为模板本身，然后我们调用一系列函数，这些函数:

1.  分配一个新模板并返回一个`*template.Template`指针
2.  解析包含在`tmpl`中的字符串，从而为执行做准备。

最后，我们调用`Execute`，它带有一个`io.Writer`(在我们的例子中是 STDOUT)和一个空接口。对于这个例子，为了简洁起见，我只加入了一个匿名结构。

这是一个模板的简单实现，类似于 Go text/template 文档中的实现。然而，文档在这方面是混乱的。当文档提到一个“模板”时，它是指我在上面的例子中实例化的`*template.Template`的实例，还是指“一个名称与我们的`*template.Template`相关联的东西”？

现在，假设我们想要编写一个程序来执行所谓的“邮件合并”，或者使用一个文本模板并将变量插入到所述文本中来生成一封群发电子邮件。我们的业务需求是，将有许多不同的消息模板，但消息头和消息尾将很少改变。困难是双重的:编写新的模板需要向每个模板文件添加不经常改变的数据，并且更新包含在标题中的信息需要对每个模板文件进行所述更新。这两个问题都会导致糟糕的、容易出错的模板。这是嵌套模板的完美用例！

假设这是我们的标题模板:

```
WidgetCo, Ltd.
463 Shoe Factory Rd.
Hamford, VT 20202
```

我们的页脚模板:

```
Thank you for your business,
WidgetCo Order Fulfillment DepartmentPh: 818-555-0123 Email: orders@widgetco.com
```

我们与客户的第一次沟通将是“感谢您的订单”信息。我们希望像这样“嵌套”页眉和页脚模板:

```
{{ template "header" }}Dear {{ .Name }},Thank you for your order! Your order number is {{ .OrderNumber }} and will be shipped on {{ .ShipDate }}.{{ template "footer" }}
```

对我来说，这就是线索变冷的地方，因为文档中没有一个清晰的例子来说明如何编写代码来实现这一点。经过大量的反复试验，我得出了以下解决方案:

```
package mainimport (
    "os"
    "text/template"
)type orderData struct {
    Name        string
    OrderNumber int
    ShipDate    string
}var header = `
    WidgetCo, Ltd.
    463 Shoe Factory Rd.
    Hamford, VT 20202
`var footer = `
    Thank you for your business,
    WidgetCo Order Fulfillment Department
    Ph: 818-555-0123 Email: [orders@widgetco.com](mailto:orders@widgetco.com)
`var thanks = `
    {{ template "header" }}
    Dear {{ .Name }},
    Thank you for your order! Your order number is {{ .OrderNumber }} and will be shipped on {{ .ShipDate }}.
    {{ template "footer" }}
`func main() {
    t, _ := template.New("header").Parse(header)
    t.New("footer").Parse(footer)
    t.New("thanks").Parse(thanks)
    ordersToThank := []orderData{
        {"Sleve McDichael", 17104, "2018-10-10"},
        {"Bobson Dugnutt", 17106, "2018-10-12"},
    } for _, data := range ordersToThank {
        t.ExecuteTemplate(os.Stdout, "thanks", data)
    }
}
```

事实证明，文档中的“模板”一词既指`*template.Template`的原始实例，也指与所述原始实例相关联的其他命名模板*。正确的说法可能是“一个`*template.Template`可以代表一个单独的命名模板，或者一个命名模板的集合”，解决方案是初始化你的第一个模板，然后在上面调用`New()`。请注意，文档中明确说明了这一点:*

> 每个模板由创建时指定的字符串命名。此外，每个模板都与零个或多个其他模板相关联，它可以通过名称来调用这些模板；这种关联是可传递的，并形成模板的名称空间。
> 
> 模板可以使用模板调用来实例化另一个相关联的模板；请参见上面对“模板”操作的解释。该名称必须是与包含调用的模板相关联的模板的名称。

然而，这是一个非常简洁的解释，需要一些上下文、细节和示例代码，以便对不熟悉该库的人有意义。

## 那么我们如何用这个来渲染 HTML 呢？

事实上，我们已经解决了我们的业务问题，但是如上面的代码所示，所有的模板数据都是硬编码的。我们当然不希望营销人员为了制作新消息而手工编辑我们的源代码！幸运的是，模板库提供了一个非常方便的方法，通过一个方法调用就可以从单个文件中加载许多模板！

让我们转换一下思路，假设 WidgetCo 公司的网站急需更新，因为它自 1997 年以来看起来就没变过。由于我们的老板非常喜欢邮件合并程序，我们提议使用同样的技术来更新网站，她很高兴地同意了。

我们将从索引页面和“关于我们”页面开始，但首先我们必须定义一些模板。

首先，我们创建一个文件夹，其结构最终将如下所示:

```
widgetco_site
├─ main.go
└─ templates
   ├─ about.html
   ├─ footer.html
   ├─ header.html
   └─ index.html
```

接下来，我们定义我们的文件。

header.html:

```
<!DOCTYPE html>
<head>
  <title>{{ .Title }}</title>
  <style>
    body {
      font-family: monospace;
      background-color: #BBC;
      color: #334;
    }
    #content {
      margin: 30px auto 30px auto;
    }
    #footer {
      font-size: 0.8em;
    }
  </style>
</head>
<body>
  <h1>Welcome to {{ .CompanyName }}!</h1>
    <nav>
      <a href="/">Home</a>
      <a href="/about">About</a>
    </nav>
  <div id="content">
```

页脚. html:

```
 </div>
  <div id="footer">
    &copy;2018, {{ .CompanyName }}, All Rights Reserved
  </div>
</body>
</html>
```

然后，我们定义几页。

index.html:

```
{{ template "header.html" . }}
<div>
  Here at WidgetCo, we only offer the highest quality widgets!
</div>
{{ template "footer.html" . }}
```

关于. html:

```
{{ template "header.html" . }}
<div>
  WidgetCo was founded in 1987 by Larry W. Cashdollar, a business man with a passion for seersucker suits and the highest quality widgets available anywhere.
</div>
{{ template "footer.html" . }}
```

最后，我们创建一个完整的 web 服务器程序。

主页面:

```
package mainimport (
  "html/template"
  "log"
  "net/http"
  "os"
  "regexp"
)type pageData struct {
  Title       string
  CompanyName string
}var t *template.Template
var routeMatch *regexp.Regexp
var pd pageDatafunc servePage(w http.ResponseWriter, r *http.Request) {
  matches := routeMatch.FindStringSubmatch(r.URL.Path)
  if len(matches) >= 1 {
    page := matches[1] + ".html"
    if t.Lookup(page) != nil {
      w.WriteHeader(200)
      t.ExecuteTemplate(w, page, pd)
      return
    }
  } else if r.URL.Path == "/" {
    w.WriteHeader(200)
    t.ExecuteTemplate(w, "index.html", pd)
    return
  }
  w.WriteHeader(404)
  w.Write([]byte("NOT FOUND"))
}func main() {
  var err error
  t, err = template.ParseGlob("./templates/*")
  if err != nil {
    log.Println("Cannot parse templates:", err)
    os.Exit(-1)
  }
  routeMatch, _ = regexp.Compile(`^\/(\w+)`)
  pd = pageData{
    "WidgetCo Home",
    "WidgetCo International",
  }
  http.HandleFunc("/", servePage)
  log.Fatal(http.ListenAndServe(":8080", nil))
}
```

在 main 函数中，我们使用`template.ParseGlob()`将整个文件夹的模板文件加载到`t`中，得到一个以包含它们的文件命名的模板集合。然后，我们将编译一个简单的正则表达式来匹配传入的 HTTP 请求 URL。我把这两件事都做成全局变量，因为它们只需要在程序启动时做一次，它们需要从我们的 HTTP 处理函数中容易地访问，并且它们在进程的生命周期中不会改变。

接下来，我们定义一些要导出到模板的数据。最后，我们将`servePage`函数绑定到根路由，然后开始监听 HTTP 请求。

在`servePage`函数中，我们使用编译后的正则表达式来匹配传入的请求 URL。请记住，这是一个非常简单的机制——如果路径的第一部分与我们已经加载的文件相匹配，我们就呈现模板。如果不是，看看我们是否在请求`/`，如果是，渲染`index.html`。如果也不是那样，那么返回一个 404。

改进我们计划的想法(将作为练习留给读者完成):

1.  **添加更复杂的路由机制。**创建一个包含预编译正则表达式的`route`结构类型，然后拥有一个从最具体到最不具体匹配的这些路由的排序数组。
2.  **添加服务静态文件的路由。**实现这一点的一种方法是将`http.FileServer`绑定到`/static`路径。
3.  **将包括路由数据在内的相关变量放入配置文件。**诸如 web 服务器运行的端口、模板文件夹、公司名称等变量不应硬编码到程序中。
4.  **添加更吸引人的 404 页面。**
5.  **防止用户访问非消费路线。例如，用户可以输入 URL `/header`，然后只显示标题模板，这显然不是一个完整的 HTML 文档。这可以通过添加更复杂的路由机制很容易地实现。**
6.  **消除在每个页面中添加页眉和页脚模板标签的必要性。**这种模式导致代码混乱，过一会儿就开始感觉像是 [cargo cult 编程](https://en.wikipedia.org/wiki/Cargo_cult_programming)。顺便提一下，在本文的第二部分，我们将讨论一种可能的方法来做到这一点！

[](https://gitconnected.com/learn/golang) [## 学习围棋-最佳围棋教程(2019) | gitconnected

### 23 大围棋教程-免费学习围棋。课程由开发者提交和投票，使您能够找到…

gitconnected.com](https://gitconnected.com/learn/golang)