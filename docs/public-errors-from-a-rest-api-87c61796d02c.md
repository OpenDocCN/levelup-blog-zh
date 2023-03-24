# 来自 REST API 的公共错误

> 原文：<https://levelup.gitconnected.com/public-errors-from-a-rest-api-87c61796d02c>

## 在 GO 中实现公共错误契约

![](img/d9be880ae83e1008b063183f85e24d03.png)

# 介绍

今天我想分享一个关于如何实现应该公开的错误的想法。你知道，基于这些，各种客户端可以向用户显示特定的消息，从后备服务请求附加信息，等等。

在与第三方集成打交道的这些年里，我注意到的是，API 调用中返回的错误并不清晰，并且不能确保在相同的 API 版本中存在错误契约。
我最近遇到的一个例子是，当我在构建支付解决方案时，我想知道客户余额何时为空，以便显示正确的消息并向用户提供充值服务。我从第三方提供商那里得到的答案是……嗯，大概是这样

> *我们将返回空余额的内部错误，但请放心，您可以调用另外 3 或 4 个端点并自己计算余额。*

![](img/219a074a4034b85de487e6f3688c9885.png)

当我问他们，当帐户被封锁时，他们会返回什么错误，他们似乎很困惑，几天后才回答这个问题。

summa summaryum——他们的实现不允许知道各种场景中存在什么样的错误，他们需要分析流程以了解错误可能发生在哪里以及将返回什么样的错误代码。最糟糕的是，缺乏抽象，这意味着错误代码*帐户被阻止*可能会变成*帐户被阻止*，而之前没有任何更新。

这也是故事之一，但我也面临过很多类似的问题，所以处理必须公开的错误并保持契约稳定似乎是一个严重的问题。或者也许我周围有某种坏运气的光环👺。无论如何，这是如何在围棋中解决这个问题的想法，它是基于大卫·切尼关于处理错误的[旧帖子。](https://dave.cheney.net/2014/12/24/inspecting-errors)

# 处理错误

错误应该在返回给公共客户端之前在顶层处理。之所以要在这里完成，唯一的原因是业务层可以用在不同的地方。例如，相同的业务方法`users.Find()`可以从 UI 网站等外部来源调用，也可以从管理员命令行工具等内部来源调用。这两种方式下，它们的行为应该是相同的，并且在这两种方式下，它们都应该显示一些错误。然而，用户界面和管理员界面上显示的内容可能不同。

UI 只能返回错误代码，但是管理员也可以看到关于错误的更多细节以及如何解决它们的一些见解。

然而，当业务逻辑改变时，它不能影响 UI 或管理员面板。除非这是一个突破性的改变，但这是完全不同的话题...

# 代码示例

为了让事情更清楚，让我们使用一些代码示例。[这里有一个](https://github.com/b-pagis/go-public-errors-example)的例子，由以下内容组成:

*   业务逻辑(`users`目录)
*   假冒存储服务与信息(`repository`目录)
*   带有 REST API 的 HTTP 服务器-由公共客户端使用(`http`目录和`cmd/http-example`目录)
*   最后是代表管理员实用工具(`cmd/cli-example`目录)的 CLI

```
.
├── cmd
│   ├── cli-example
│   │   ├── main.go
│   └── http-example
│       └── main.go
├── curler.sh
├── go.mod
├── http
│   ├── error.go
│   ├── handlers.go
│   └── middleware.go
├── repository
│   ├── current
│   │   ├── error.go
│   │   └── current.go
│   └── legacy
│       ├── error.go
│       └── legacy.go
└── users
    ├── errors.go
    └── users.go
```

# 实现包级错误

如果您研究了项目结构，很可能您已经注意到每一层都有自己的错误实现和自己的错误代码。这种模式提供了更多的灵活性，并且没有引入集中的错误管理机制，所有的错误都在一个地方处理。

我认为，从长远来看，全局错误管理器是邪恶的，因为随着时间的推移，当有人想要替换代码的某些部分时，它只会变得越来越大，那么每次都肯定会触及全局错误处理程序。这增加了全球性破坏的风险。

此外，使用全局错误管理器，业务逻辑在某个时候可能会被放入其中，因为这样做更方便、更简单、更快或有任何其他原因。

当它被分离时，它保持简单、小，并且只与特定的层相关，因此更容易应用改变或者完全替换整个实现。

这里是`user`错误实现。

```
package users

// Error custom error type for the users domains
type Error struct {
	errCode    string
	errMessage string
}

func newError(code string, message string) error {
	return Error{errCode: code, errMessage: message}
}

func (e Error) Error() string {
	return e.errMessage
}

func (e Error) Code() string {
	return e.errCode
}
```

> 当然，这种方法也有一些缺点——代码重复，这会让一些狂热的 DRY 开发者抓狂。此外，在非常大的项目中处理它可能会非常烦人。然而，依我看，前者比后者更有可能发生😄

# 返回错误

因为自定义错误实现符合标准错误的签名。我们只是简单地为我们关心的情况创建新的错误并传递它。

未找到案例的存储库错误示例:

```
return users.User{}, internalError{code: "notFound", msg: "user not found"}
}
```

> *注意:如果需要，也可以包装原始错误。*

# 根据行为采取行动

在业务领域中，我们检查错误行为，而不是实际检查错误类型。因此，如果第一次搜索返回一个错误，我们检查这个错误是否实现了方法`NotFound()`,如果是，并且如果确实没有找到错误，那么我们检查用户是否是遗留存储库。

```
type notFounder interface {
  NotFound() bool
}

if e, ok := err.(notFounder); !ok {
  return User{}, err
} else {
  if !e.NotFound() {
    return User{}, err
  }
}

user, err = u.LegacyDB.Find(searchableUserName)

if err != nil {
  return User{}, err
}
```

如果我们也不能在遗留存储库中找到用户，我们简单地将错误传递给上层。这样，没有实现这种方法的数据库层错误将被传递给调用者。那些实现这种方法的将被用作业务流程的一部分。

# REST API 契约

REST API 是最后一层，信息从这里传递回调用者，所以这可能是我们可以定义和处理公共错误的最好地方。

有几种方法可以实现这一点。例如，我们可以在处理程序中通过定义带有相应 HTTP 状态代码的公共错误列表来实现:

```
type publicErrors map[string]int

publicErrs := publicErrors{
	"notFound":        http.StatusNotFound,
	"sessionNotFound": http.StatusForbidden,
}
```

然后像我们之前在业务级别所做的那样检查行为:

```
if err != nil {
		log.Println(err)
		if publicErrs.isPublic(err) {
			w.Header().Add("Content-Type", "application/problem+json")
			w.WriteHeader(publicErrs.HTTPStatusCode(err))
			w.Write([]byte(`{"code":"` + publicErrs.Code(err) + `", "message":"` + err.Error() + `"}`))

			return
		} else {
			w.Header().Add("Content-Type", "application/problem+json")
			w.WriteHeader(http.StatusInternalServerError)
			w.Write([]byte(`{"code":"internalError"}`))

			return
		}
	}
```

## 使用中间件

对于那些不喜欢冗长工作方式的人来说，还有一种使用中间件的方法，它会以某种常见的方式处理公共错误响应。同样，我们需要用相应的 HTTP 状态定义公共错误列表，并返回包含该列表的新错误。

```
publicErrs := publicErrors{
		"notFound":            http.StatusNotFound,
		"sessionNotFound":     http.StatusForbidden,
		"adminAccessRequired": http.StatusForbidden,
	}

	currentUser, err := getUserSession(currentUserID)

	if err != nil {
		return nil, handlerError{allowedList: publicErrs, originalErr: err}
	}
```

然后我们创建一个中间件，在那里我们检查 error 是否实现了特定的功能，帮助我们确定我们的错误是否是公开的。

```
type handlerErr interface {
	Public() bool
	HTTPStatusCode() int
	Code() string
	Error() string
}

if e, ok := err.(handlerErr); ok && e.Public() {
	w.WriteHeader(e.HTTPStatusCode())
	w.Write([]byte(`{"code":"` + e.Code() + `", "message":"` + err.Error() + `"}`))
	return
}

w.WriteHeader(http.StatusInternalServerError)
w.Write([]byte(`{"code":"internalError"}`))

return
```

这样做可以让我们去掉多个 HTTP writer 函数调用，让处理程序更简洁。然而，如果我们需要在处理程序中使用 cookies、查询参数、头或其他信息，这种方法可能会有点麻烦。

# 响应和输出

在本文的开头，我说过无论在哪里使用，业务实现都应该是相同的，所以如果您看过代码，就会发现我们对 HTTP 和 CLI 的错误处理是不同的。

CLI 返回有关它的更多信息，因为它仅供管理员内部使用。另一方面，HTTP 返回的信息较少。

以下是他们三人的回答，以供比较

## CLI

```
***************
* Scenario #1 *
***************
action: 	 searching for: Maria
outcome:	 failed to get user Maria. Error: need higher access level

***************
* Scenario #2 *
***************
action: 	 searching for: Maria
outcome:	 found user: {ID:1 Name:Maria AccessLevel:1}

action: 	 searching for: Nushi
outcome:	 failed to get user: Nushi. Error: need admin access level

action: 	 searching for: Mohammed
outcome:	 found user: {ID:3 Name:Mohammed AccessLevel:3}

action: 	 searching for: Jose
outcome:	 issue with third party, please contact support@thirdparty

action: 	 searching for: Wei
outcome:	 failed to get user: Wei. Error: user not found
```

## **HTTP**

**带有公共错误和中间件**

```
HTTP Status Code|	Respoonse
403		|  {"code":"sessionNotFound", "message":"session not found or expired"}
500		|  {"code":"internalError"}
404		|  {"code":"notFound", "message":"user not found"}
200		|  {"id":"1","name":"Maria","accessLevel":1}
403		|  {"code":"adminAccessRequired", "message":"need admin access level"}
200		|  {"id":"3","name":"Mohammed","accessLevel":3}
500		|  {"code":"internalError"}
404		|  {"code":"notFound", "message":"user not found"}
```

> *注意:
> HTTP 服务包含三个处理程序:*
> 
> -不使用公共错误的那个
> -使用公共错误且错误在处理程序
> 中处理的那个-有公共错误但错误在中间件中处理的那个
> 
> *此外，与中间件一起使用的处理程序有一个额外的公共错误* `*adminAccessRequired*` *，因此响应与没有中间件的略有不同。*

## 实施确实很重要

让我们先把 CLI 被管理员使用的事实放在一边，这样会暴露更多的信息。 [CLI 实现](https://github.com/b-pagis/go-public-errors-example/blob/main/cmd/cli-example/main.go)没有遵循我们在 [HTTP 处理程序](https://github.com/b-pagis/go-public-errors-example/blob/main/http/handlers.go)中看到的相同模式。现在让我们问自己几个问题:

*   我们能知道 CLI 将返回哪些错误吗？
*   我们需要分析所有代码来了解所有可能的变化吗？
*   我们能否确保错误不会改变，管理员不会感到困惑？

现在让我们问自己，哪种方法更经得起未来的考验，更清晰，并且可以在回答问题/分析上花费更少的时间？

# 结论

这是一篇非常简短的帖子，使用了非常基本的代码示例，没有任何优化。然而，它展示了一个核心思想，即公共错误确实很重要，我们应该在实现任何解决方案之前考虑它们。

![](img/aa10b9aa7f9a3a820be85bc2bbd14d18.png)

如果你喜欢我写的东西，想听更多，你可以[订阅这里](https://medium.com/subscribe/@p.a.g.i.s)。如果你不是灵媒的一员，并且想成为一名灵媒，你可以在这里[做！](https://medium.com/@p.a.g.i.s/membership)

当然，你可以用一杯热的虚拟咖啡招待我，☕ **😋**

[![](img/f70fb2bbb97e021536fd6f765c321c37.png)](https://ko-fi.com/W7W04WZNY)[![](img/fdca75bbe0eee382398964b8e17af001.png)](https://www.buymeacoffee.com/b.pagis)[![](img/96f2533283f86e5b77e1cb7b51b8478e.png)](https://github.com/sponsors/b-pagis?frequency=one-time)

# 链接

[](https://github.com/b-pagis/go-public-errors-example) [## GitHub-b-pagis/go-public-errors-example

### 关于如何处理公共错误的想法。这种或类似实现的主要优点:确保稳定…

github.com](https://github.com/b-pagis/go-public-errors-example)  [## 检查错误

### 返回接口类型错误值的函数的常见约定是，调用者不应该假定…

dave.cheney.net](https://dave.cheney.net/2014/12/24/inspecting-errors)