# 模仿 Golang-interfaces 中的出站 HTTP 调用

> 原文：<https://levelup.gitconnected.com/mocking-outbound-http-calls-in-golang-9e5a044c2555>

![](img/07c7cee521ea37ec9c7db2b7bd8d7a0b.png)

现在的工程师有这么多很酷的 API 可以使用。你可以通过谷歌搜索找到满足特定需求的 API。我们有 [Twilio](https://www.twilio.com/) 用于发送电子邮件/传真/短信，[丛林奇兵](https://therundown.io/api)用于实时体育比分和赔率，或者 [Airbnb](https://www.airbnb.com/partner) 用于管理你的房源。您也可以前往 [RapidAPI](https://rapidapi.com/collection/recommended-apis) 网站，发现数百种 API 以满足任何需求。

在 Golang 中进行出站 HTTP 调用非常容易，但是当您测试使用第三方 API 的代码时，您最不想做的事情就是为此进行真正的调用。原因有很多:您的 CI/CD 管道可能不允许出站调用，调用可能需要太长时间来响应，您可能需要为调用 API 付费，等等。在这种情况下，嘲笑是很有意义的。幸运的是，嘲笑这些电话几乎和打电话一样简单。在这篇文章中，我们将看看一个模仿出站 HTTP 调用的简单模式。

> **注**:这篇帖子很大程度上是受 [Sophie DeBenedetto](https://www.linkedin.com/in/sophiedebenedetto/) 写的一篇的启发，你可以在这里查看:[https://www . the greatcodeadventure . com/mocking-http-requests-in-golang/](https://www.thegreatcodeadventure.com/mocking-http-requests-in-golang/)。我真的很喜欢她的解决方案，你会发现这里是她的代码略有修改的版本。

# 设置

出于本文的目的，我有意使我的代码尽可能简单。我想把重点放在对电话的嘲讽上，而不是别的。因此，这是一个单一功能的 Golang 项目，它将调用 GitHub 来检索给定用户的所有回购。简单扼要。GitHub API 可以在未经认证的情况下使用，所以这样更好！让我们从`github.go`文件开始:

```
package githubimport (
 "encoding/json"
 "fmt"
 "net/http"
)// GetRepos takes a username and retreives
func GetRepos(username string) ([]map[string]interface{}, error) {

 url := fmt.Sprintf("https://api.github.com/users/%s/repos?sort=created&direction=desc", username) request, err := http.NewRequest(http.MethodGet, url, nil)
 if err != nil {
  return nil, err
 } client := &http.Client{}
 response, err := client.Do(request)
 if err != nil {
  return nil, err
 }defer response.Body.Close() m := []map[string]interface{}{}
 err = json.NewDecoder(response.Body).Decode(&m)
 if err != nil {
  return nil, err
 }return m, nil
}
```

这里没有太多内容，但还是让我们解开这段代码:

*   首先，该函数将 GitHub 用户名作为一个参数，并立即将其放入一个格式化的 URL 中，请求返回按回购的“创建日期”降序排序的结果。
*   接下来，我们有正常的出站 HTTP 调用代码:创建一个请求，创建一个客户机，然后进行调用。
*   最后，我们将响应的主体映射到一个`[]map[string]interface{}`来从函数返回 JSON。对于这个例子来说，这并不是真正需要的，但是它使得编写测试变得更加容易；)

现在我们来看一下`github_test.go`文件:

```
package githubimport (
 "testing"
)func TestGitHubCallSuccess(t *testing.T) {
 result, err := GetRepos("atkinsonbg")
 if err != nil {
  t.Error("TestGitHubCallSuccess failed.")
  return
 } if len(result) == 0 {
  t.Error("TestGitHubCallSuccess failed, array was empty.")
  return
 } if result[0]["full_name"] != "atkinsonbg/unittest-outbound-http-calls-golang" {
  t.Error("TestGitHubCallSuccess failed, array was not sorted correctly.")
  return
 }
}func TestGitHubCallFail(t *testing.T) {
 _, err := GetRepos("atkinsonbgthisusershouldnotexist")
 if err == nil {
  t.Error("TestGitHubCallFail failed.")
  return
 }
}
```

这里没有太多要测试的，但是我们也将打开这个:

*   TestGitHubCallSuccess 调用我们的`GetRepos`函数，传递一个有效的用户名。它首先检查 JSON 数组中是否有内容，然后获取第一个结果以确保我们期望的名称存在。
*   TestGitHubCallFail 还调用我们的`GetRepos`函数，传入一个非常无效的用户名。它确保函数抛出适当的错误。

现在我们可以运行一个`go test -v ./... -coverpkg ./... -coverprofile cover.out`命令，看到我们得到了 85.7%的覆盖率！不算太寒酸！！

```
Users-Air:unittest-outbound-http-calls-golang user$ go test -v ./... -coverpkg ./... -coverprofile cover.out
=== RUN   TestGitHubCallSuccess
--- PASS: TestGitHubCallSuccess (0.35s)
=== RUN   TestGitHubCallFail
--- PASS: TestGitHubCallFail (0.04s)
PASS
coverage: 85.7% of statements in ./...
ok      _/Users/user/Documents/GitHub/atkinsonbg/unittest-outbound-http-calls-golang    0.411s  coverage: 85.7% of statements in ./...
```

当然，这个测试有一个明显的问题。我们在测试中实际调用了 GitHub。这是站不住脚的。现在让我们继续，模拟这个调用，同时仍然实现我们的高代码覆盖率！

# 嘲笑它直到你成功

一般来说，在 Go 中，如果你想模仿某个东西，你可以创建一个接口。反之亦然，如果你有一个接口，你可以很容易地嘲笑它。*(抱歉打哑谜，很快就清楚了。)* Go 关于接口的官方文档可以在这里[找到](https://golang.org/doc/effective_go.html#interfaces)，它提供了一个很好的解释:

> Go 中的接口提供了一种指定对象行为的方式:如果某个东西可以做*这个*，那么它就可以用*这里*。

[Nathan LeClaire](https://www.linkedin.com/in/nathanleclaire/) 在 Golang 中写了一篇关于接口和有效测试的[好文章，他总结为:](https://nathanleclaire.com/blog/2015/10/10/interfaces-and-composition-for-effective-unit-testing-in-golang/)

> 接口允许您定义一组方法，一个类型(通常是`struct`)必须定义这些方法才能被认为是接口的实现。
> 
> 当任何给定的类型实现了该接口的所有方法时，Go 编译器自动知道它被允许作为该类型使用。

现在最棒的部分是，我们**不必实现接口/结构上的所有**方法来实现它(*然而，如果你想使用它，你必须实现它*)。在我们的代码中，我们确实需要模仿 Go 的 HTTP 客户端，但是我们确实不需要模仿所有的方法。我们只使用 Do 方法，所以这是我们唯一关心的方法。如果我们看一下 [http 包](https://golang.org/pkg/net/http/)的官方文档，我们可以看到[客户机结构](https://golang.org/pkg/net/http/#pkg-index)是如何定义的。它由六个函数组成:CloseIdleConnections、Do、Get、Head、Post 和 PostForm。在我们的代码中，我们使用的是 Do 函数，所以这是我们唯一关心的模拟函数。

[Do 功能](https://golang.org/pkg/net/http/#Client.Do)定义为:

```
func (c *[Client](https://golang.org/pkg/net/http/#Client)) Do(req *[Request](https://golang.org/pkg/net/http/#Request)) (*[Response](https://golang.org/pkg/net/http/#Response), [error](https://golang.org/pkg/builtin/#error))
```

因此，当需要模拟我们的 HTTP 调用时，这是我们必须匹配的签名。现在我们有了这些信息，我们可以开始重新编写代码来支持嘲讽。下面是`github.go`文件现在的样子:

```
package githubimport (
 "encoding/json"
 "fmt"
 "net/http"
)// HTTPClient interface
type HTTPClient interface {
 Do(req *http.Request) (*http.Response, error)
}var (
 Client HTTPClient
)func init() {
 Client = &http.Client{}
}// GetRepos takes a username and retreives
func GetRepos(username string) ([]map[string]interface{}, error) {

 url := fmt.Sprintf("https://api.github.com/users/%s/repos?sort=created&direction=desc", username) request, err := http.NewRequest(http.MethodGet, url, nil)
 if err != nil {
  return nil, err
 } response, err := Client.Do(request)
 if err != nil {
  return nil, err
 } defer response.Body.Close() m := []map[string]interface{}{}
 err = json.NewDecoder(response.Body).Decode(&m)
 if err != nil {
  return nil, err
 }return m, nil
}
```

我们将深入探讨四个显著的差异:

*   首先，我们定义了一个名为`HTTPClient`的新接口，它有一个名为`Do`的方法，恰好匹配 Go http 包 Do 的签名模式。
*   接下来，我们创建一个名为`Client`的包级变量，它是`HTTPClient`的一种。这是我们将用来进行所有出站 HTTP 调用的变量。因为这是一个包级别的声明，并且我们的测试在同一个包中，他们将能够在以后将这个变量设置为一个模拟的客户端。
*   在我们的包初始化函数中，我们将变量`Client`设置为 Go 的 http 包客户端的一个实例。
*   最后，在我们的代码中，当我们进行`Do`调用时，我们使用我们的`Client`变量:`reponse,err := Client.Do(request)`

我们现在有了要模拟的主要代码设置。我们可以再次运行我们的测试，应该没有什么变化。输出将是完全相同的，包括代码覆盖率。这里最大的不同是，我们现在可以模拟我们的 HTTP 调用了！

# 实现模拟调用

现在我们已经有了主代码设置，我们可以修改我们的测试来注入一个模拟的`Do`调用。让我们看看`github_test.go`文件中的代码:

```
package githubimport (
 "bytes"
 "errors"
 "io/ioutil"
 "net/http"
 "testing"
)// Custom type that allows setting the func that our Mock Do func will run instead
type MockDoType func(req *http.Request) (*http.Response, error)// MockClient is the mock client
type MockClient struct {
 MockDo MockDoType
}// Overriding what the Do function should "do" in our MockClient
func (m *MockClient) Do(req *http.Request) (*http.Response, error) {
 return m.MockDo(req)
}func TestGitHubCallSuccess(t *testing.T) { // build our response JSON
 jsonResponse := `[{
   "full_name": "mock-repo"
  }]` // create a new reader with that JSON
 r := ioutil.NopCloser(bytes.NewReader([]byte(jsonResponse))) Client = &MockClient{
  MockDo: func(*http.Request) (*http.Response, error) {
   return &http.Response{
    StatusCode: 200,
    Body:       r,
   }, nil
  },
 } result, err := GetRepos("atkinsonbg")
 if err != nil {
  t.Error("TestGitHubCallSuccess failed.")
  return
 } if len(result) == 0 {
  t.Error("TestGitHubCallSuccess failed, array was empty.")
  return
 } if result[0]["full_name"] != "mock-repo" {
  t.Error("TestGitHubCallSuccess failed, array was not sorted correctly.")
  return
 }
}func TestGitHubCallFail(t *testing.T) { // create a client that throws and returns an error
 Client = &MockClient{
  MockDo: func(*http.Request) (*http.Response, error) {
   return &http.Response{
    StatusCode: 404,
    Body:       nil,
   }, errors.New("Mock Error")
  },
 } _, err := GetRepos("atkinsonbgthisusershouldnotexist")
 if err == nil {
  t.Error("TestGitHubCallFail failed.")
  return
 }
}
```

现在这里还有更多的东西，但是这是一个很好的可重复模式，提供了大量的灵活性。让我们来分解一下:

*   首先，我们创建一个名为`MockDoType`的新`type`，它与 Go http 包的`Do`函数具有相同的签名。
*   接下来，我们创建一个名为`MockClient`的`struct`，它有一个名为`MockDo`的属性，这是我们新创建的`MockDoType`。
*   最后，我们将一个`Do`方法挂在我们的`MockClient`结构上，该结构也具有与 http 包的`Do`函数相同的签名。这里的主要区别是，它所做的只是转过身，将`request`交给`MockDoType`运行。

现在，在我们的每个测试中，我们可以编写这样的代码:

```
 // build our response JSON
 jsonResponse := `[{
   "full_name": "mock-repo"
  }]` // create a new reader with that JSON
 r := ioutil.NopCloser(bytes.NewReader([]byte(jsonResponse))) Client = &MockClient{
  MockDo: func(*http.Request) (*http.Response, error) {
   return &http.Response{
    StatusCode: 200,
    Body:       r,
   }, nil
  },
 }
```

让我们也打开包装:

*   首先，我们创建一个 JSON 响应，我们希望从对 GitHub 的实际调用中得到这个响应。在这种情况下，我们期望一个对象数组，但我并不在乎这里有完整的对象，只要足够测试就行。
*   接下来，我们用 JSON 创建一个响应体，它将从调用中返回。一个 HTTP 响应有一个类型为`io.Reader`的主体，幸运的是，Go 提供了[no closer](https://golang.org/pkg/io/ioutil/#NopCloser)函数，它返回其中的一个，非常适合测试。
*   最后，我们用我们的`MockClient`的一个实例设置包级别`Client`变量(在`github.go`文件中声明)。我们用支持测试所需的任何响应来初始化这个客户机！在这个例子中，我们现在有一个包含 test JSON 的`io.Reader`,所以我们将它传递给我们的`MockDo`属性。因为`MockDo`是一个`MockDoType`，它基本上是`Do`函数的一个实现，我们可以在这里设置 HTTP 响应的所有属性！

这个乍一看有点混乱。最终，我们在这里试图做的是模拟我们从`Do`呼叫中得到的响应。我们不在乎嘲笑请求，只在乎回应。我们将我们的`HTTPClient`定义为一个接口，带有一个`Do`属性，我们不能在测试中设置它。因此我们的`MockDoType`是我们可以设置的，只是通过我们的`MockClient` `Do`方法来调用。

这种模式在测试失败调用时非常出色:

```
func TestGitHubCallFail(t *testing.T) { // create a client that throws and returns an error
 Client = &MockClient{
  MockDo: func(*http.Request) (*http.Response, error) {
   return &http.Response{
    StatusCode: 404,
    Body:       nil,
   }, errors.New("Mock Error")
  },
 } _, err := GetRepos("atkinsonbgthisusershouldnotexist")
 if err == nil {
  t.Error("TestGitHubCallFail failed.")
  return
 }
}
```

该代码将在调用`Client.Do`后立即触发`github.go`文件中的错误检查。再次运行我们的测试，我们得到相同的输出:

```
go test -v ./... -coverpkg ./... -coverprofile cover.out
=== RUN   TestGitHubCallSuccess
--- PASS: TestGitHubCallSuccess (0.00s)
=== RUN   TestGitHubCallFail
--- PASS: TestGitHubCallFail (0.00s)
PASS
coverage: 85.7% of statements in ./...
ok      _/Users/user/Documents/GitHub/atkinsonbg/unittest-outbound-http-calls-golang    0.013s  coverage: 85.7% of statements in ./...
```

嗯，几乎相同的输出，这些测试运行得快得多！我们仍然有 85.7%的覆盖率，这太好了！现在我们已经有了模拟，我们可以测试以前很难测试的代码，GitHub 响应到 JSON 的映射:

```
 response, err := Client.Do(request)
 if err != nil {
  return nil, err
 } defer response.Body.Close() m := []map[string]interface{}{}
 err = json.NewDecoder(response.Body).Decode(&m)
 if err != nil {
  return nil, err
 }
```

在我们的模拟之前，我们调用的是实际的 GitHub API，它非常稳定，不会经常失败。因此，解码对 JSON 的响应几乎总是成功的。我们不能在这里正确地测试负面的情况，在这里坏的数据回来了。有了我们的模拟，我们现在可以编写一个这样的测试:

```
func TestGitHubCallBadJsonFail(t *testing.T) { // build our response JSON
 jsonResponse := `{
  "full_name": "mock-repo"
 }` // create a new reader with that JSON
 r := ioutil.NopCloser(bytes.NewReader([]byte(jsonResponse))) // create a client that throws and returns an error
 Client = &MockClient{
  MockDo: func(*http.Request) (*http.Response, error) {
   return &http.Response{
    StatusCode: 200,
    Body:       r,
   }, nil
  },
 } _, err := GetRepos("atkinsonbg")
 if err == nil {
  t.Error("TestGitHubCallBadJsonFail failed.")
  return
 }
}
```

在这个测试中，我们将我们的 JSON 响应指定为一个 JSON 对象，而不是一个数组，数组应该被返回。现在，当我们运行这个测试时，JSON 映射代码将失败，并应该返回一个错误。现在运行我们的测试，我们可以看到我们的覆盖率上升到 92.9%！

```
go test -v ./... -coverpkg ./... -coverprofile cover.out
=== RUN   TestGitHubCallSuccess
--- PASS: TestGitHubCallSuccess (0.00s)
=== RUN   TestGitHubCallFail
--- PASS: TestGitHubCallFail (0.00s)
=== RUN   TestGitHubCallBadJsonFail
--- PASS: TestGitHubCallBadJsonFail (0.00s)
PASS
coverage: 92.9% of statements in ./...
ok      _/Users/user/Documents/GitHub/atkinsonbg/unittest-outbound-http-calls-golang    0.014s  coverage: 92.9% of statements in ./...
```

# 包扎

在这篇文章中，我们了解了如何在 Go 测试中轻松模拟出站 HTTP 调用。希望您会发现这种模式也很容易在您的代码中实现。这不仅提供了一种方便的方法来模拟您的调用，还提供了一种灵活的方法来基于每个测试修改这些模拟。再次感谢[索菲·德贝尼代托](https://www.thegreatcodeadventure.com/mocking-http-requests-in-golang/)的精彩帖子，这篇帖子很大程度上基于。尽情享受吧！

## 资源

*   [GitHub 回购](https://github.com/atkinsonbg/unittest-outbound-http-calls-golang)
*   [模仿 Golang 中的 HTTP 请求](https://www.thegreatcodeadventure.com/mocking-http-requests-in-golang/)

[![](img/3515ab52cb6fb5e74c27c7a2e06d3811.png)](https://ko-fi.com/O5O63ENS7)