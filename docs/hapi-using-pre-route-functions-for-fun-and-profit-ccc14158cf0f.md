# 如何为哈比神路线处理程序预加载对象

> 原文：<https://levelup.gitconnected.com/hapi-using-pre-route-functions-for-fun-and-profit-ccc14158cf0f>

![](img/5702d60e914fe521b203e6415e5bcf13.png)

或者，如何使用哈比神预路由处理程序

## 问题是

随着我在最新的项目中加入更多的方法，我意识到浪费的精力越来越多。

在每个处理项目的端点中，我们必须获取项目。这也意味着每个端点还必须检查用户是否有权访问该项目。当我开始添加属于事物的事物时，我们必须检查拥有的所有权链，这开始变得乏味。

我开始想——Express 有中间件，我想知道哈比神有什么？肯定有什么东西，所以我可以做一次工作，并把它存储在请求对象中。

[到 API 文档！](https://hapi.dev/api/)

![](img/9644faaef3014741c80a4d84fc587b14.png)

## 解决方法

## [验证](https://hapi.dev/api/?v=20.1.2#-routeoptionsvalidateparams)

这些开始看起来很有希望——毕竟，我们正在验证请求参数。

不幸的是，它们没有帮助——验证不能添加到请求上下文中，所以验证函数将获取项目，然后该函数必须再次获取项目。(或者我们开始做一些缓存——可能，但过于复杂。)

## [插件](https://hapi.dev/api/?v=20.1.2#plugins)

接下来，我看了看插件。不过，对我来说，它们并不太合适。

插件是在整个服务器上注册的，而不是一个单独的路由。但是这带来了一个问题——您如何知道哪些请求必须有参数，哪些没有？没有它，您仍然需要检查端点函数，这不是我想要的。

## [预路由功能](https://hapi.dev/api/?v=20.1.2#-routeoptionspre)

这些看起来更有希望。它们在[认证](https://hapi.dev/tutorials/auth/?lang=en_US)之后运行，所以你已经得到了用户凭证。它们可以添加到请求上下文中——它们返回的值进入[到](https://hapi.dev/api/?v=20.1.2#-requestpre) `[request.pre](https://hapi.dev/api/?v=20.1.2#-requestpre)` [对象](https://hapi.dev/api/?v=20.1.2#-requestpre)。您可以将它们添加到单独的路线中。

看来我们有赢家了！

![](img/280971406f593212776a06a43d06b138.png)

# 尝试一下

我们需要一些东西来开始。让我们使用模板和验证来扩展[上的帖子中的人员服务器。](https://www.solarwinter.net/hapi-vision-and-joi/)

我们还将在不使用预路由功能的情况下进行第一次尝试。这让我们检查基本流程是否工作，因为我们以前没有使用过它们，我们可以看到它对代码有什么样的影响。

我们有一个路由，`/people`，用来获取我们存储的所有人的列表。让我们添加一个新的路线来获得个人。会很好的休息。

## 试验

首先——像往常一样——我们添加了一个测试。

```
.   it("can get an individual person", async () => {
        const res = await server.inject({
            method: "get",
            url: "/people/1"
        });
        expect(res.statusCode).to.equal(200);
        expect(res.payload).to.not.be.null;
    });
```

当然会失败，因为服务器还不知道这条路由。

## 模板

接下来，我们将添加将要使用的模板。我们保持它真正的基础——这不是让东西看起来漂亮，只是测试一个概念。

```
<html>
	<head>
		<title>Purple People Eaters</title>
	</head>
	<body>
        <p><%= person.name %> - <%= person.age %></p>
		<a href="/people">Go back to people</a>
	</body>
</html>
```

## 密码

现在我们开始添加实际的代码。我们需要做的第一件事是扩展路由表:

```
export const peopleRoutes: ServerRoute[] = [
    { method: "GET", path: "/people", handler: showPeople },
    { method: "GET", path: "/people/{personId}", handler: showPerson },
    { method: "GET", path: "/people/add", handler: addPersonGet },
    { method: "POST", path: "/people/add", handler: addPersonPost }  
];
```

然后是处理函数。因为我们在这个项目中不处理认证，所以它已经相当简单了。

```
async function showPerson(request: Request, h: ResponseToolkit): Promise<ResponseObject> {
    const person = people.find(person =>
        person.id == parseInt(request.params.personId)
    );
    return h.view("person", { person: person });
}
```

请注意，我们在这里跳过了错误检查，以便启动并运行一些东西。而且很管用！

```
server handles people - positive tests
    ✓ can see existing people
    ✓ can show 'add person' page
    ✓ can add a person and they show in the list
    ✓ can get an individual person
```

## 使用 pre

第一件事是检查预路由处理程序所需的函数签名。它看起来非常类似于标准的请求处理程序，但是具有不同的返回类型。

这是有意义的——请求处理程序返回 HTTP 响应，而预路由处理程序可能返回对象。

它需要是健壮的——这是检查传入数据正确性的函数——所以我们添加了通常在 HTTP 路由中的所有错误检查。我们的设计是要么返回一个有效的对象，要么抛出一个异常，所以我们把返回类型设为`Person`。

```
async function checkPerson(request: Request, h: ResponseToolkit): Promise<Person> {
    // Did the user actually give us a person ID?
    if (!request.params.personId) {
        throw Boom.badRequest("No personId found");
    }

    try {
        const person = people.find(person => person.id == parseInt(request.params.personId));
        if (!person) {
              throw Boom.notFound("Person not found");
        }
        return person;
    } catch (err) {
        console.error("Error", err, "finding person");
        throw Boom.badImplementation("Error finding person");
    }
}
const checkPersonPre = { method: checkPerson, assign: "person" };
```

我们需要更改路由表来添加新选项:

```
{ method: "GET", path: "/people/{personId}", handler: showPerson, options: { pre: [checkPersonPre] } },
```

然后更新`showPerson`功能:

```
async function showPerson(request: Request, h: ResponseToolkit): Promise<ResponseObject> {
    return h.view("person", { person: request.pre.person });
}
```

甚至在我们的玩具项目中，我们的 HTTP 处理程序现在看起来干净多了。

# 在实际项目中的使用

举一个我正在开发的项目的例子，你可以看到它带来了更大的不同。

在改变之前，每条路线都必须:

*   获取站点，检查是否允许用户引用站点
*   获取事件，检查它是否连接到该站点
*   处理丢失/错误的值

大概是这样的:

```
async function deleteEventPost(request: Request, h: ResponseToolkit): Promise<ResponseObject> {
    try {
        if (!request.params.siteId) {
            throw Boom.badRequest("No site ID");
        }
        if (!request.params.eventId) {
            throw Boom.badRequest("No event ID");
        }

        // We don’t actually want the site or event, we just 
        // want to confirm ownership.
        const site = await getSite(request.auth.credentials.id, request.params.siteId);
        if (!site) {
            throw Boom.notFound();
        }
        const event = await getEvent(site.id, request.params.eventId);
        if (!event) {
            throw Boom.notFound();
        }

        await deleteEvent(event.id);
        return h.redirect(`/sites/${site.id}/events`);
    } catch (err) {
        console.error("Error", err);
        throw Boom.badImplementation("error deleting event");
    }
}
```

在添加了预路由处理程序之后，它精简了很多:

```
async function deleteEventPost(request: Request, h: ResponseToolkit): Promise<ResponseObject> {
    try {
        await deleteEvent(request.pre.event.id);
        return h.redirect(`/sites/${request.pre.site.id}/events`);
    } catch (err) {
        console.error("Error", err);
        throw Boom.badImplementation("error deleting event");
    }
}
```

对几乎每一个功能都重复这一点，你就会明白为什么这是一个成功！

所有工作都在一个地方完成——实际的视图功能可以假设数据在那里并且是有效的，因为如果不是这样，它们就不会运行，并且它们可以继续做它们实际应该做的事情。

# 结束

嗯，就是这样。如果有帮助，请告诉我。像往常一样，帖子中的代码可以在[我的 Github repo](https://github.com/arafel/hapi-route-pre) 中找到。