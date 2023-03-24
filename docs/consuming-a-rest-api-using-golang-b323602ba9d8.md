# 使用 Golang 使用 REST API

> 原文：<https://levelup.gitconnected.com/consuming-a-rest-api-using-golang-b323602ba9d8>

## 概观

这篇文章将讨论如何使用 Go 来使用 REST API。我们将介绍设置 go 项目的步骤、使用的 Go 模块以及如何运行代码。

![](img/4693bc2a8f8cb54f0d73b140682d0863.png)

定制一只地鼠@[https://gopherize.me/](https://gopherize.me/)

## 先决条件

*   有效的 Golang 安装

如果你需要如何安装的指导，这是一个好地方开始[https://golang.org/doc/install](https://golang.org/doc/install)

## 介绍

我想开一个开发者博客已经有一段时间了，但是似乎从来没有时间去做。英格兰的第二次封锁仍然有效，看来现在是个好时机。

作为开发人员，这是一个很受欢迎的任务，所以我认为这是一个很好的开始。在这篇文章中将要用到的 API 是[icanhazdadjock](https://icanhazdadjoke.com/)，你可能从 URL 中猜到当它被调用时会返回一个笑话。首先，让我们创建一个新目录来保存这个项目。

## 项目设置

```
mkdir golang-api
cd golang-api
go mod init github.com/cameronldroberts/golang-api
touch main.go
```

我们现在已经准备好开始编码了！在您最喜欢的编辑器(VSCode for me)中打开`golang-api`项目，然后打开`main.go`，这是代码所在的位置。

## 进口

首先，我们将介绍导入，这也是我们引入其他 Go 模块的方式。在 Go 中，模块是一个或多个包含相关代码的包的集合。为了调用 API，我们将使用以下四个。根据您使用的编辑器(和设置),导入将被自动处理，因此您可能不需要执行以下手动添加导入的步骤。

```
package mainimport (
 "encoding/json"
 "fmt"
 "io/ioutil"
 "net/http"
)
```

## 示例响应

这是我们调用 API 时得到的示例响应。因为我们知道我们将收到的 JSON 响应的结构，所以我们可以将它映射到一个结构中。

```
{
"id": "XgVnOK6USnb",
"joke": "What did the calculator say to the student? You can count on me",
"status": 200
}
```

## 结构体

这个结构相当简单，所以在这个实例中，从 JSON 映射到下面的结构并不需要太多的工作。

```
type Response struct {
 ID     string `json:"id"`
 Joke   string `json:"joke"`
 Status int    `json:"status"`
}
```

如果我们有一个大得多的 JSON 响应，这将是一个非常耗时的过程。不要担心，有一个叫做
[JSON-to-Go](https://mholt.github.io/json-to-go/) 的有用工具可以处理转换。

## 密码

最后是调用 API 和处理响应的函数。当用 Go 开发时，不建议在`main()`中有函数逻辑，因为这是你程序的入口点。出于本文的目的，我们将把代码放在 main 中，但是不建议在现实世界中开发。更多信息请点击[这里](https://golang.org/doc/code.html)

```
func main() {
 fmt.Println("Calling API...")client := &http.Client{}
 req, err := http.NewRequest("GET", "[https://icanhazdadjoke.com/](https://icanhazdadjoke.com/)", nil)
 if err != nil {
  fmt.Print(err.Error())
 }
 req.Header.Add("Accept", "application/json")
 req.Header.Add("Content-Type", "application/json")
 resp, err := client.Do(req)
 if err != nil {
  fmt.Print(err.Error())
 }defer resp.Body.Close()
 bodyBytes, err := ioutil.ReadAll(resp.Body)
 if err != nil {
  fmt.Print(err.Error())
 }var responseObject Response
 json.Unmarshal(bodyBytes, &responseObject)
 fmt.Printf("API Response as struct %+v\n", responseObject)
}
```

*   首先，我们创建一个 http 客户端，并创建我们的 http 请求
*   然后，在发送带有' resp，err := client 的 http 请求之前，我们在请求中添加了几个 http 头。Do(req)`
*   我们读入响应，然后将其解组到我们的响应结构中
*   最后，我们打印出响应

## 运行项目

要运行该项目，请使用以下命令

```
go run main.go
```

这将编译并运行`main.go`文件。如果一切顺利，你应该有一个笑话打印到你的终端。输出应该类似于

```
go run main.goCalling API...API Response as struct {ID:5h399pWLmyd Joke:What did the beaver say to the tree? It's been nice gnawing you. Status:200}
```

**TLDR**

```
package mainimport (
 "encoding/json"
 "fmt"
 "io/ioutil"
 "net/http"
)type Response struct {
 ID     string `json:"id"`
 Joke   string `json:"joke"`
 Status int    `json:"status"`
}func main() {
 fmt.Println("Calling API...")client := &http.Client{}
 req, err := http.NewRequest("GET", "[https://icanhazdadjoke.com/](https://icanhazdadjoke.com/)", nil)
 if err != nil {
  fmt.Print(err.Error())
 }
 req.Header.Add("Accept", "application/json")
 req.Header.Add("Content-Type", "application/json")
 resp, err := client.Do(req)
 if err != nil {
  fmt.Print(err.Error())
 }defer resp.Body.Close()
 bodyBytes, err := ioutil.ReadAll(resp.Body)
 if err != nil {
  fmt.Print(err.Error())
 }var responseObject Response
 json.Unmarshal(bodyBytes, &responseObject)
 fmt.Printf("API Response as struct %+v\n", responseObject)
}
```

这就是第一个帖子的内容！希望对你有所帮助。

【https://www.cameronroberts.dev/ 