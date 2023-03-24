# Golang 中的简单路线处理以及如何避免

> 原文：<https://levelup.gitconnected.com/messy-route-handling-in-golang-and-how-to-avoid-it-736e7d5e216b>

![](img/8fc1a0fcc022a560fe6aa36b134fb4c8.png)

下面的例子是用 Go 编写的，但是同样的原则很容易适用于多种语言。

在处理路线方面，Go 有一个非常简单易懂的方法。开发人员会看到 http 包中的默认处理程序，因此我们可以立即开始响应请求。

```
func (w http.ResponseWriter, r *http.Request) {
    _, err := w.Write(w, []byte("hello there!"))
    if err != nil {
        w.WriteHeader(http.StatusInternalServerError)
    }
}
```

我见过很多这样构建的 API，但它们迟早会遇到同样的问题——不一致、责任纠缠、不当行为和脆弱的代码。

# 使用默认处理程序的有效场景

著名的**中间件**链接模式是处理多种场景的有效方式，因此默认处理程序是该任务的绝佳候选。

```
func (w http.ResponseWriter, r *http.Request, next http.HandlerFunc) {
    if something {
        w.WriteHeader(http.StatusUnauthorized)
        return
    } next(w, r)
}
```

中间件链规则规定，处理程序要么写入响应对象，要么调用链中的下一个对象。因此，写入响应对象是路由处理程序的责任。这是好的，直到它不是。

# **哪里出错了**

使用路由处理程序编写响应是一项额外的责任，会带来以下令人不快的后果。

## 代码复制

在每个路由处理程序中，设置正确的头、编码有效负载和写入响应对象的逻辑都必须重复。

## 不保证一致性

一个 API 应该在它的所有端点上保持一致。用 InternalServerError 响应，有时返回纯文本，有时返回 JSON 对象，有时什么也不返回，这是不自然的。

如果您用 NoContent 状态进行响应，同时在响应体中放置一个 JSON 对象，会怎么样？

虽然使用默认处理程序允许您创建您能想到的任何荒谬的组合，但它注定会变得混乱。

## 关注点分离

一旦路由处理程序处理完结果，它需要选择适当的方式来写入响应。这不是一个好的关注点分离。结果已经准备好被返回，这就足够了。

## 脆弱代码

在实践中，在整个代码库中创建新路线的每个人都必须熟悉必须做的每件事，并且他们应该练习这样做。否则，我们将打破前面的任何一点，这是一个不能总是及时发现的错误。项目越大，就越难发现这样的错误。

# 简单的“修复”

如果我们正在处理代码复制和标准化，自然会考虑使用**助手函数**。因此，我们可能会在代码库中添加一个名为 httputil，的包，并开始描述有效响应的样子。

```
func WriteCreated(w http.ResponseWriter, payload interface{}) {
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusCreated)

    if err := json.NewEncoder(w).Encode(payload); err != nil {
        w.WriteHeader(http.StatusInternalServerError)
    }
}
```

虽然这是一个进步，但问题仍然存在——没有人强迫路由处理程序使用这个或任何其他帮助函数。他们可以干脆决定耍无赖，自己写响应对象。

# 理解真正的问题

如果我们期望从函数中得到一个结果，我们自然会使用返回函数。通过这种方式，我们确切地知道我们将得到什么，并且我们可以确保它总是以我们需要的格式出现。

然而，void 函数没有单一的返回场景。他们没有义务返回任何东西。不能强迫他们做任何与将结果传递回调用者相关的事情。

## 使用 void 函数返回值

根据定义，Go 中的默认路由处理程序是 **void 函数**。因此，他们不受任何约束，也不能期望或要求他们以任何方式行事。他们只是做他们认为最好的事情。

# 如何解决这个问题

路由处理器的职责是接受和验证输入，调用业务逻辑，然后返回正确的结果。

一个路由处理器执行其工作所需要的就是**请求对象**。writer 对象只是一个需要以某种方式管理的额外负担。

可以使用简单的 struct 对象来描述响应。那么，为什么不让路由处理器成为返回这样一个结构的返回函数呢？

## 标准化响应

首先，我们创建一个**响应**包，并在其中定义以下内容:

**response.go**

```
type PayloadType intvar(
    PayloadEmpty PayloadType = iota
    PayloadText
    PayloadJSON
)type HttpResponse struct {
    statusCode int
    payload    interface{}
    payloadType PayloadType
    еrrMessage string
}
```

我们有意将这些字段保持私有，因此只能通过使用一个标准化的响应构造函数来创建对象。

```
func Created(payload interface{}) HttpResponse {
    return HttpResponse{
        statusCode: http.StatusCreated,
        payload: payload,
    }
}// all needed response types...
```

最后一步是实现一个写函数。这将是一个单点故障，因为它将用于编写整个代码库的所有响应。

```
func Write(w http.ResponseWriter, response HttpResponse) error {
    // write the response
}
```

## 符合路线处理程序

现在可以使用我们创建的 **HttpResponse** 结构来描述响应。我们所要做的就是修改路由处理程序，这样它们就可以开始返回一个标准化的值。

```
func(a *api) DoSomething (r *http.Request) response.HttpResponse {
    id := getParam(r, "id")
    if isValidID(id) == false {
        return response.BadRequest(ErrMsgInvalidID)
    } result, err := a.service.DoSomething(id)
    if err != nil {
        msg := "do something was unable to complete"
        return response.InternalServerError(msg)
    } return response.OK(payload)
}
```

现在，我们可以将它包装在一个标准的处理器中，并在任何路由器和中间件中使用它。

```
type RouteHandler func (*http.Request) response.HttpResponsefunc Wrap (handler RouteHandler) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        resp := handler(r)

        if err := response.Write(w, resp); err != nil {
            logger.Error(err)
        }
    }
}
```

# 结论

这篇文章中的观点远非革命性的。然而，它们解决了仍然存在于任何地方的代码库中的问题。我已经看过很多了。

代码库往往会随着时间的推移而增长。从事这项工作的人数也在增加。如果标准化的行为和一致性得不到执行，那么混乱就会慢慢接管。

虽然在开始时加快速度并避免事情过于复杂是很诱人的，但响应是每个 API 的重要部分，我相信它们应该做得正确。

我希望本文中的信息可以帮助人们提出自己的想法，这样我们就可以创建令人愉快的 API。

欢迎评论和提问。