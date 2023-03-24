# 用 Golang 更好地模拟 HTTP 调用

> 原文：<https://levelup.gitconnected.com/mocking-http-call-in-golang-a-better-way-5cb97e3d5ffe>

![](img/387593e0b9251f027b99e3971ff9d123.png)

*照片由*[*@ Jim _ rear Dan*](https://unsplash.com/@jim_reardan)*在 Unsplash 上*

# 介绍

作为一名软件工程师，你需要每天学习以保持你的知识是最新的。任何方面的改进都会帮助你写出更好的代码。写了越来越多的 Golang 代码后，我意识到我可以改进[这篇博文](https://clavinjune.dev/en/blogs/mocking-http-call-in-golang/)。

正如您在那篇文章中看到的，您需要[模拟 HTTP 客户端](https://clavinjune.dev/en/blogs/mocking-http-call-in-golang/#http-client-mock)来正确模拟 HTTP 调用。此外，您需要更改您的 [API 实现](https://clavinjune.dev/en/blogs/mocking-http-call-in-golang/#api-implementation-struct)来使用 HTTPClient 接口。从长远来看，这是一个相当大的问题，因为你不知道 HTTP 客户端在 Golang 代码库的下一个版本中会得到什么样的改进。如果您模仿 HTTP 客户端，这就是您遇到的问题。相反，您可以改变视角，开始模仿 HTTP 服务器。

在这篇博文中，您将学习如何使用内置测试库模拟 HTTP 服务器。没有必要创建自己的接口，因为它们都是由 Golang 标准库`httptest`提供的。

# 目录结构

```
$ go mod init example
go: creating new go.mod: module example
$ mkdir -p external
$ touch external/{external.go,external_test.go}
$ tree .
.
├── external
│   ├── external.go
│   └── external_test.go
└── go.mod1 directory, 3 files
```

# 实施文件内容

```
// external.go
package externalimport (
    "context"
    "encoding/json"
    "errors"
    "fmt"
    "net/http"
    "time"
)var (
    ErrResponseNotOK error = errors.New("response not ok")
)type (
    Data struct {
        ID   string `json:"id"`
        Name string `json:"name"`
    }External interface {
        FetchData(ctx context.Context, id string) (*Data, error)
    }v1 struct {
        baseURL string
        client  *http.Client
        timeout time.Duration
    }
)func New(baseURL string, client *http.Client, timeout time.Duration) *v1 {
    return &v1{
        baseURL: baseURL,
        client:  client,
        timeout: timeout,
    }
}func (v *v1) FetchData(ctx context.Context, id string) (*Data, error) {
    url := fmt.Sprintf("%s/?id=%s", v.baseURL, id)ctx, cancel := context.WithTimeout(ctx, v.timeout)
    defer cancel()req, err := http.NewRequestWithContext(ctx, http.MethodGet, url, nil)
    if err != nil {
        return nil, err
    }resp, err := v.client.Do(req)
    if err != nil {
        return nil, err
    }
    defer resp.Body.Close()if resp.StatusCode != http.StatusOK {
        return nil, fmt.Errorf("%w. %s", ErrResponseNotOK, http.StatusText(resp.StatusCode))
    }var d *Data
    return d, json.NewDecoder(resp.Body).Decode(&d)
}
```

它与您之前实现的略有不同，但目标仍然是对外部服务进行 HTTP 调用。让我们把重点放在你需要嘲笑的`External interface`上。

# 测试文件内容

```
package external_testimport (
    "example/external"
    "fmt"
    "net/http"
    "net/http/httptest"
    "testing"
    "time"
)var (
    server *httptest.Server
    ext    external.External
)func TestMain(m *testing.M) {
    fmt.Println("mocking server")
    server = httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        // mock here
    }))fmt.Println("mocking external")
    ext = external.New(server.URL, http.DefaultClient, time.Second)fmt.Println("run tests")
    m.Run()
}
```

首先，您需要模拟 HTTP 服务器和`External object`。

正如您在第 24 行看到的:

```
...
ext = external.New(server.URL, http.DefaultClient, time.Second)
...
```

您可以将`server.URL`用作`baseURL`，这样所有对`baseURL`的 HTTP 调用都将由`httptest.Server`处理。这就是你模拟 HTTP 服务器而不是 HTTP 调用的方式。

创建模拟服务器之后，您还需要模拟端点。例如:

```
...func mockFetchDataEndpoint(w http.ResponseWriter, r *http.Request) {
    ids, ok := r.URL.Query()["id"]sc := http.StatusOK
    m := make(map[string]interface{})if !ok || len(ids[0]) == 0 {
        sc = http.StatusBadRequest
    } else {
        m["id"] = "mock"
        m["name"] = "mock"
    }w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(sc)
    json.NewEncoder(w).Encode(m)
}...
```

然后，将端点放入模拟服务器中。

```
...server = httptest.NewServer(http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    switch strings.TrimSpace(r.URL.Path) {
    case "/":
        mockFetchDataEndpoint(w, r)
    default:
        http.NotFoundHandler().ServeHTTP(w, r)
    }
}))...
```

这样模仿 HTTP 服务器的好处是，您可以将所有需要的端点都放在一个服务器上。它将在`m.Run()`之前被创建一次，然后被同一个包中的所有测试使用。

# 创建单元测试

```
...func fatal(t *testing.T, want, got interface{}) {
    t.Helper()
    t.Fatalf(`want: %v, got: %v`, want, got)
}func TestExternal_FetchData(t *testing.T) {
    tt := []struct {
        name     string
        id       string
        wantData *external.Data
        wantErr  error
    }{
        {
            name:     "response not ok",
            id:       "",
            wantData: nil,
            wantErr:  external.ErrResponseNotOK,
        },
        {
            name: "data found",
            id:   "mock",
            wantData: &external.Data{
                ID:   "mock",
                Name: "mock",
            },
            wantErr: nil,
        },
    }for i := range tt {
        tc := tt[i]t.Run(tc.name, func(t *testing.T) {
            t.Parallel()gotData, gotErr := ext.FetchData(context.Background(), tc.id)if !errors.Is(gotErr, tc.wantErr) {
                fatal(t, tc.wantErr, gotErr)
            }if !reflect.DeepEqual(gotData, tc.wantData) {
                fatal(t, tc.wantData, gotData)
            }
        })
    }
}
```

现在您已经模拟了 HTTP 服务器，单元测试本身没有什么特别的。您可以像往常一样开始编写单元测试。例如:

# 结论

通过改变视角，你已经提高了单元测试很多。模拟 HTTP 服务器比模拟 HTTP 调用更具可读性，也更合适。您不需要创建 HTTP 客户端的接口，并开始使用标准的方式通过使用`httptest`来模拟调用。

感谢您的阅读！

*原载于 2021 年 12 月 17 日*[*https://clavinjune . dev*](https://clavinjune.dev/en/blogs/mocking-http-call-in-golang-a-better-way/)*。*