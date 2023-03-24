# Swift |进行 API 调用并获取 JSON

> 原文：<https://levelup.gitconnected.com/swift-making-an-api-call-and-fetching-json-acd364c77a71>

![](img/199e216ee5bf72e3d8c2aa65e5eb7784.png)

由[谷仓图片](https://unsplash.com/@barnimages?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/http?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# API 和 REST(RESTful)API

## **API**

也称为应用程序编程接口，是软件中使用的一组协议和功能，用于帮助与其他软件进行交互。

## **RESTful API**

简称为**Re**presentational**S**state**T**transfer API，是一个基于客户端服务器的 API，使用 URIs、HTTP 协议和 JSON 从 web 访问数据。

Restful APIs 通过 HTTP 动词(GET、POST、PUT、DELETE、PATCH)执行 CRUD，通过 XML 或 JSON 文件交换数据。

```
POST :Request a URI with POST creates a resource GET: Retrieves the resource and gets detailed information about the document. PUT: Replaces all current representation. Causes change in state(Updates information) -> always call POST to view changes DELETE: Deletes information from server.
```

例子

考虑一个管理学生资源的 API。

```
{
  "id": 1,
  "firstName": "Tyrion",
  "lastName": "Lannister",
  "classes": [
    {"id": 1, "name": "History of Westeros"}, 
    {"id": 2, "name": "Brewing"}, 
    {"id": 3, "name": "High Valyrian 101"}
  ]
}
```

*   [POST]/学生:创建新学生
*   [GET]/学生:调用整个学生列表
*   [GET]/学生:叫第一个学生
*   [PUT]/students/1:编辑关于学生 1 的信息
*   [DELETE]/students/1:删除学生 1

# 正在获取 JSON

我将使用 [JSONPlaceholder API](https://jsonplaceholder.typicode.com) ，这是一个用于原型和测试数据的免费 REST API。他们提供的 API 之一是 [posts API](https://jsonplaceholder.typicode.com/posts) ，看起来像这样。

```
{
    "userId": 1,
    "id": 1,
    "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
    "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto" }
```

创建一个名为 Post 的模型，它符合**可编码协议。**Codable**协议用于编码(从 Swift 到 other(JSON))和解码(从 other(JSON)到 Swift)我们从 API 获得的数据。你可以选择你想从 API 中获取的数据，我已经决定获取所有的数据，包括用户 Id、id、标题和主体。**

**苹果创建了一个名为 **URLSession** 的会话来帮助 IOS 程序与服务器通信。它支持 HTTP 协议，这是我们访问 RESTful APIs 时所需要的。**

```
**{****"userId": 1,
"id": 1,
"title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
"body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"****}**
```

# **解码 JSON**

**上面的代码只是使用苹果的 URL 会话从一个 API 获取数据。让我们试着解码数据。我们需要解码数据，因为它是以 JSON 格式编写的，我们使用的是 Swift。为此，我们将使用 **JSONDecoder()** ，这是一个从 JSON 对象中解码数据的对象。对于这个例子，我将使用 [full post API。](https://jsonplaceholder.typicode.com/posts)**

**在这里，我收集了每一个实例，对其进行解码，并打印出帖子的标题。**

```
**sunt aut facere repellat provident occaecati excepturi optio reprehenderit
qui est esse
ea molestias quasi exercitationem repellat qui ipsa sit aut
eum et est occaecati
nesciunt quas odio
......**
```

**这就是我对这个会议的全部内容，我希望它真的有所帮助！获取和解码 JSON 数据可能会令人困惑，但是如果你练习的话，它真的很有帮助！**

**如果你在 yu24c@mtholyoke.edu 有任何问题，请告诉我。感谢你阅读这篇文章！**