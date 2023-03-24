# 在 Golang-net/HTTP/HTTP test 中模拟出站 HTTP 调用

> 原文：<https://levelup.gitconnected.com/mocking-outbound-http-calls-in-golang-net-http-httptest-bc5629cd3c3e>

![](img/eb1bcef7cc73e9cd4a933da2cd47d702.png)

不久前，我写了一篇名为“[用 Golang](/mocking-outbound-http-calls-in-golang-9e5a044c2555) 模拟出站 HTTP 调用”的文章，展示了如何使用 HTTP 接口模拟下游 HTTP 调用。目标是展示如何在不进行任何实际 HTTP 调用的情况下测试任何代码。这种方法非常有效，但是随着我编写越来越多的代码，并接触到不同的模拟方法，我发现是时候用另一种方式来模拟您的调用了。这两种方法都是有效的，都应该被考虑，但是我发现自己经常使用这个例子，因为它太简单了。

# 设置

出于本文的目的，我有意使我的代码尽可能简单。我想把重点放在对电话的嘲讽上，而不是别的。因此，这是一个单一功能的 Golang 项目，它将调用 GitHub 来检索给定用户的所有回购。简单扼要。GitHub API 可以在未经认证的情况下使用，所以这样更好！让我们从`github.go`文件开始:

```
package githubimport (
 "encoding/json"
 "fmt"
 "net/http"
)// GetRepos takes a username and retreives
func GetRepos(username string) ([]map[string]interface{}, error) {

 url := fmt.Sprintf("https://api.github.com/users/%s/repos?sort=created&direction=desc", username)request, err := http.NewRequest(http.MethodGet, url, nil)
 if err != nil {
  return nil, err
 }client := &http.Client{}
 response, err := client.Do(request)
 if err != nil {
  return nil, err
 }defer response.Body.Close()m := []map[string]interface{}{}
 err = json.NewDecoder(response.Body).Decode(&m)
 if err != nil {
  return nil, err
 }return m, nil
}
```

这里没有太多内容，但还是让我们解开这段代码:

*   首先，该函数将 GitHub 用户名作为一个参数，并立即将其放入一个格式化的 URL 中，请求返回按回购的“创建日期”降序排序的结果。
*   接下来，我们有正常的出站 HTTP 调用代码:创建一个请求，创建一个客户机，然后进行调用。
*   最后，我们将响应的主体映射到一个`[]map[string]interface{}`，以便从函数中返回 JSON。对于这个例子来说，这并不是真正需要的，但是它使得编写测试变得更加容易；)

现在我们来看看`github_test.go`文件:

```
package githubimport (
 "testing"
)func TestGitHubCallSuccess(t *testing.T) {
 result, err := GetRepos("atkinsonbg")
 if err != nil {
  t.Error("TestGitHubCallSuccess failed.")
  return
 }if len(result) == 0 {
  t.Error("TestGitHubCallSuccess failed, array was empty.")
  return
 }if result[0]["full_name"] != "atkinsonbg/unittest-outbound-http-calls-golang" {
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
*   TestGitHubCallFail 也调用我们的`GetRepos`函数，传入一个非常无效的用户名。它确保函数抛出适当的错误。

现在我们可以运行一个`go test -v`命令，看到我们得到了 85.7%的覆盖率！不算太寒酸！！

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

一般来说，在 Go 中，如果你想模仿某个东西，你可以创建一个接口。反之亦然，如果你有一个接口，你可以很容易地嘲笑它。*(抱歉打哑谜，很快就清楚了。)* Go 关于接口的官方文档可以在这里找到[，它提供了一个很好的解释:](https://golang.org/doc/effective_go.html#interfaces)

> Go 中的接口提供了一种指定对象行为的方式:如果某个东西可以做*这个*，那么它就可以用*这里*。

[Nathan LeClaire](https://www.linkedin.com/in/nathanleclaire/) 在 Golang 中写了一篇关于接口和有效测试的[好文章，他总结道:](https://nathanleclaire.com/blog/2015/10/10/interfaces-and-composition-for-effective-unit-testing-in-golang/)

> 接口让你定义一组方法，一个类型(通常是`*struct*`)必须定义这些方法才能被认为是接口的实现。
> 
> 当任何给定的类型实现了该接口的所有方法时，Go 编译器自动知道它被允许作为该类型使用。

这基本上就是我们在之前的文章中所做的，我们在 HTTP 客户端接口上实现了`Do`函数，这允许我们在稍后的测试中模拟它。现在让我们看看如何用另一种方法来做这件事。

# 构建结构

首先，我们将重构我们的`github.go`文件并引入一个结构:

```
type GitHubManager struct {
    BaseUrl string
    Client http.Client
}
```

这是一个非常简单的直接结构，简单地命名为`GitHubManager`，它有两个字段:

*   **客户端**:这基本上可以让我们大量清理代码。它还提供了一种方法来注入一个定制的 HTTP 客户端，就像一个定制的传输，等等。
*   **BaseUrl** :这个字段提供了一种方法来覆盖调用 GitHub 所使用的基本 Url。在我们之前的代码中，这个 URL 是`[https://api.github.com](https://api.github.com,)` [，](https://api.github.com,)然而，为了支持嘲讽，我们希望能够控制这个，你将在后面的帖子中看到。

接下来，我们针对这些变化更新代码:

```
func (ghm *GitHubManager) GetRepos(username string) ([]map[string]interface{}, error) {
   url := fmt.Sprintf("%s/users/%s/repos?sort=created&direction=desc", ghm.BaseUrl, username)

   request, err := http.NewRequest(http.*MethodGet*, url, nil)
   if err != nil {
      return nil, err
   }

   response, err := ghm.Client.Do(request)
   if err != nil {
      return nil, err
   }

   defer response.Body.Close()

   m := []map[string]interface{}{}
   err = json.NewDecoder(response.Body).Decode(&m)
   if err != nil {
      return nil, err
   }

   return m, nil
}
```

首先，我们需要更新我们的`url`变量，以利用新的 struct 字段:

```
url := fmt.Sprintf("%s/users/%s/repos?sort=created&direction=desc", ghm.BaseUrl, username)
```

接下来，我们可以访问该结构的`http.Client`,向 GitHub 发出请求:

```
response, err := ghm.Client.Do(request)
```

这些变化使我们的代码更加灵活，但在使用这个结构/方法时，确实引入了少量的前期配置。这没什么大不了的，对于更复杂的交互来说，这是一个很好的模式。现在让我们看看这是如何使测试变得如此容易的。

# 使用 httptest 进行测试。新闻服务器

Go 提供了一个名为`[httptest](https://pkg.go.dev/net/http/httptest)`的非常好的包，在他们的文档中被描述为“*包 httptest 提供了用于 HTTP 测试的实用程序*”。非常简单的解释，但真的是这样。在这个包中有`Server`，它被描述为“*服务器是一个 HTTP 服务器，监听本地环回接口上系统选择的端口，用于端到端 HTTP 测试*。你没看错，这提供了一种启动真正的本地服务器来响应真正的 HTTP 请求的方法，特别是来自测试的请求。

让我们来看一个使用`Server`的简单测试，然后分解它:

```
package githubimport (
 "net/http"
 "net/http/httptest"
 "testing""github.com/stretchr/testify/assert"
)func TestGitHubCallSuccess(t *testing.T) {// build our response JSON
 jsonResponse := `[{
   "full_name": "mock-repo"
  }]`// create a new server with that JSON
 server := httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
  w.WriteHeader(200)
  _, _ = w.Write([]byte(jsonResponse))
 }))
 defer server.Close()ghm := GitHubManager{
  BaseUrl: server.URL,
 }result, err := ghm.GetRepos("atkinsonbg")
 if err != nil {
  t.Error("TestGitHubCallSuccess failed.")
  return
 }assert.True(t, len(result) > 0)
assert.Equal(t, result[0]["full_name"], "mock-repo")
}
```

首先，我们从 github.com/stretchr/testify/assert导入所有需要的包:`net/http,` `net/http/httptest,` `testing,`和`assert`。Assert 只是提供了一些很好的函数来执行测试断言，如果您以前没有使用过它，绝对推荐您去看看。

接下来是我们的测试函数，`TestGitHubCallSuccess`，它将使用一个测试服务器来模拟我们的 GitHub 调用，让我们来分析一下它在做什么:

*   首先，我们创建一个模拟 json 响应，希望我们的代码能够处理它。现在，我们的代码除了将 JSON 发送回我们之外没有做更多的事情，但是如果您的代码确实对 JSON 有效负载做了一些事情，那么您可以在这里制作您喜欢的任何东西，它将从测试服务器返回。
*   接下来，我们使用`httptest.NewServer().`创建一个新的测试服务器，这上面的签名有点多，因为`NewServer`带有一个`http.HandlerFunc`，它带有一个接受`http.ResponseWriter`的`func`和一个指向`http.Request.`的指针。好消息是，这几乎是样板文件，任何时候你需要一个新的服务器，这就是签名。
*   在`func`中，我们可以访问`http.ResponseWriter,`和`w`变量，这是所有魔法发生的地方。我们从设置`w.WriteHeader(200)`开始，它设置了我们想要返回的状态代码。如果你想要一个 200，你不必指定这个。然而，它提供了对提供 201、400、404、500、503 等等的强大控制。你甚至可以使用`w.Header().Add("key", “value").`来指定自定义标题
*   接下来，我们用`_,_ = w.Write([]byte(jsonResponse)).`提供响应的主体。这非常简单，我们需要以字节数组的形式向编写器提供主体，我们简单地忽略返回变量，因为这是我们测试中的受控环境，我们不需要它们。
*   最后我们调用一个`defer server.Close()`来清理资源。

这就是设置测试服务器并提供模拟响应所要做的全部工作。现在让我们看看你如何使用它。

由于我们创建了一个采用`BaseUrl`参数的结构，我们可以简单地像这样使用服务器:

```
ghm := GitHubManager{
   BaseUrl: server.URL,
}

result, err := ghm.GetRepos("atkinsonbg")
```

如你所见，我们简单地将`server.URL`传递给我们的结构，当我们调用`ghm.GetRepos()`时，测试服务器将被调用，而不是实际的 GitHub API URL。最后，我们根据传入的模拟 JSON 检查结果:

```
assert.True(t, len(result) > 0)
assert.Equal(t, result[0]["full_name"], "mock-repo")
```

如果我们对我们的回购运行`go test -v`,我们应该看到我们所有的测试都通过了:

```
(base) ➜  unittest-outbound-http-calls-golang-2 git:(main) ✗ go test -v
=== RUN   TestGitHubCallSuccess
--- PASS: TestGitHubCallSuccess (0.00s)
=== RUN   TestGitHubCallFail
--- PASS: TestGitHubCallFail (0.00s)
PASS
ok      github.com/atkinsonbg/unittest-outbound-http-calls-golang-2     0.367s
```

注意我在 GitHub repo 中有一个额外的测试来测试调用失败；)

# 包扎

在这篇文章中，我们看到了如何使用`net/http/httptest`包轻松模拟出站 HTTP 调用进行测试。这个包非常简单，同时又非常强大。我们仅仅触及了它的表面，它已经提供了大量的价值。因此，如果您发现自己需要测试下游 HTTP 调用，不用再找了，这个包可以解决您的问题。

## 资源

*   [GitHub 回购](https://github.com/atkinsonbg/unittest-outbound-http-calls-golang-2)
*   [net/http/httptest](https://pkg.go.dev/net/http/httptest)
*   [断言](https://github.com/stretchr/testify)
*   [回复作者](https://pkg.go.dev/net/http#ResponseWriter)