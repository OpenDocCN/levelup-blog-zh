# 使用 Go 模板时，不要忽略“missingkey”选项

> 原文：<https://levelup.gitconnected.com/when-using-go-templates-dont-neglect-the-missingkey-option-cc28a7e967b3>

![](img/66c09229fcd2f606bfce1f89d67d4176.png)

Go 附带了几个非常强大且简单易用的模板包。这些包使得生成基于模板的文本输出变得异常容易，并与数据结合产生最终输出。下面是对此的一个简单的形象化描述:

![](img/8c4c10728f6225e0cdabcbe1f0f09601.png)

在这个例子中，我们使用 HTML 作为我们的模板，它有一个替换的标记“name”。我们向它传递一些 JSON 数据，输出更多的是替换了标记的 HTML。Go 中的两种风格的模板是“[文本/模板](https://golang.org/pkg/text/template/)和“[html/模板](https://golang.org/pkg/html/template/)”。我们不会深入细节，但要注意的主要事情是，如果你正在生成 html，确保你使用“html/template”包，因为它生成“HTML 输出安全代码注入”。

虽然这很容易实现，但这里有一个“陷阱”会让您非常头疼！如果你没有阅读所有的文档，这很容易被忽略。这一切都源于模板包如何处理数据中丢失的键。如果你有一个模板，期望一个令牌被替换，但是你没有提供那个令牌，结果可能是灾难性的，同时也是卑鄙的。然而，这很容易解决，最好用一些例子来说明。

# 简单的演示

让我们来看一个非常简单的模板演示。你也可以通过 Go Playground [这里](https://play.golang.org/p/OUcwbg8hW4a)运行这段代码。

```
package mainimport (
 "os"
 "fmt"
 "html/template"
 "encoding/json"
)func main() {// Creating a new template

 temp, err := template.New("foo").Parse(`Hello {{.world}}! This is more text at the end.`)
 if err != nil {
  fmt.Println(err)
 }

 // Creating a JSON string and marshaling it to a map

 jsondata := `{ "world": "Earth" }`
 m := map[string]interface{}{}
 err = json.Unmarshal([]byte(jsondata), &m)
 if err != nil {
  fmt.Println(err)
 }

 // Executing the template then checking for errors
 // Stdout will print out the executed template

 err = temp.Execute(os.Stdout, m)
 if err != nil {
  fmt.Println(err)
 }

}
```

这里发生的事情不多，但是让我们来分解一下。首先，我们用想要替换的令牌动态创建一个新模板。接下来，我们创建 JSON 数据并将其编组到一个地图中。方便的是，我们在数据中有需要映射的属性！最后，我们执行模板，将数据传递给它，并打印出结果。在整个过程中，我们检查错误，以防出错。运行这段代码会向我们呈现以下输出:

```
Hello Earth! This is more text at the end.
```

完美！我们的模板和 JSON 数据按预期工作，生活是美好的！现在我们来介绍一个 bug。

# 缺失数据

现在，让我们想象你的商业伙伴喜欢你的模板，一切看起来都很好，但是缺少一个关键的数据。您需要更新模板来显示这些数据。您这样做了，但是可怕的事情发生了，后端团队忘记用新的数据点更新他们的 JSON！让我们看看模板包是如何开箱即用地处理这个问题的。你可以在这里查看这个代码[。](https://play.golang.org/p/uaTai_Nsxrh)

```
package mainimport (
 "os"
 "fmt"
 "html/template"
 "encoding/json"
)func main() {// Creating a new template that will have a missing JSON property

 temp, err := template.New("foo").Parse(`Hello {{.world}}! VERY IMPORTANT DATA: {{.important}} This is more text at the end.`)
 if err != nil {
  fmt.Println(err)
 }

 // Creating a JSON string and marshaling it to a map, there is no "important" property here!!

 jsondata := `{ "world": "Earth" }`
 m := map[string]interface{}{}
 err = json.Unmarshal([]byte(jsondata), &m)
 if err != nil {
  fmt.Println(err)
 }

 // Executing the template then checking for errors
 // Stdout will print out the executed template

 err = temp.Execute(os.Stdout, m)
 if err != nil {
  fmt.Println(err)
 }

}
```

我们已经更新了模板，按照要求添加了新的`important`令牌。然而，我们的 JSON 数据仍然是旧的结构，没有新的重要数据。运行这段代码会产生以下输出:

```
Hello Earth! VERY IMPORTANT DATA:  This is more text at the end.
```

现在你看着这个，心想，“当然，那是不会出现的！”。你这么想是对的。在这个简单的演示中，这个错误非常突出。有人会检查单行输出，注意到重要的数据不存在，我们会解决这个问题并继续前进。

然而，这并不是一个真实的例子。在现实世界中，你的模板可能有几页长，有 CSS 和图片。它可能会被转换成 PDF 格式并发送给客户，或者加载到文档管理系统中。它可能包含数百个至关重要的数据点，数量如此之多，以至于很容易漏掉一个字段。你需要一种方法来提前知道出了什么问题。

# 修复

![](img/ffc957603863389f35445df1a3d9b238.png)

幸运的是，通过将“missingkey”选项传递给模板，您可以很容易地捕捉到这些错误。让我们用这个选项来更新代码，你也可以在这里看到。

```
package mainimport (
 "os"
 "fmt"
 "html/template"
 "encoding/json"
)func main() {// Using the Option object here to throw an error if a key is missing in the data

 temp, err := template.New("foo").Option("missingkey=error").Parse(`Hello {{.world}}! VERY IMPORTANT DATA: {{.important}} This is more text at the end.`)
 if err != nil {
  fmt.Println(err)
 }

 // Creating the JSON string and marshaling it to a map

 jsondata := `{ "world": "Earth" }`
 m := map[string]interface{}{}
 err = json.Unmarshal([]byte(jsondata), &m)
 if err != nil {
  fmt.Println(err)
 }

 // Executing the template then checking for errors
 // this will error as the template is looking for {{.important}} but its missing in the JSON
 // Stdout will still print out as the writer has data up to the error

 err = temp.Execute(os.Stdout, m)
 if err != nil {
  fmt.Println(err)
 }

}
```

更新的代码包括`Option("missingkey=error"`选项。现在，当我们运行代码时，我们得到以下输出:

```
Hello Earth! VERY IMPORTANT DATA: template: foo:1:41: executing "foo" at <.important>: map has no entry for key "important"
```

这样好多了！现在，当我们的模板和数据键不匹配时，我们会得到一个正确的错误消息。有了这段代码，我们现在可以在出错后退出，让下游的消费者知道出错了，我们不会为他们生成文档。即使开箱即用的错误提供了您需要的所有信息，很明显我们缺少数据！

点击，你可以了解更多关于你可以使用[的选项。可用的“missingkey”选项包括默认、无效、零和错误。您可以在操场上尝试所有这些，您会发现，只有“错误”会导致不同的输出。所有其他选项只是给出缺少密钥的输出。](https://golang.org/pkg/html/template/#Template.Option)

# 包扎

如何使用模板包完全取决于你自己。在某些情况下，您可能并不真正关心某个键是否丢失。然而，即使在这种情况下，我仍然主张使用“missingkey”选项。我宁愿知道我的模板正在期待它没有得到的东西，并记录下来以备后用。如果您的用例要求使用模板中的每一个键，那么这是满足您的模板需求的必备选项。您最不想做的事情就是在几天或几周后惊讶地发现，您已经使用丢失数据的模板生成了文档！

[![](img/3515ab52cb6fb5e74c27c7a2e06d3811.png)](https://ko-fi.com/O5O63ENS7)