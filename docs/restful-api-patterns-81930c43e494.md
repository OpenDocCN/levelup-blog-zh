# RESTful API 模式

> 原文：<https://levelup.gitconnected.com/restful-api-patterns-81930c43e494>

## 有很多方法可以编写一个兼容 REST 架构风格的 API，我在这里将一些解决方案进行了分组。

REST 架构风格有定义良好的约束，可以帮助开发者编写可伸缩的 Web 服务接口，但是 API 并不容易编写。这就是为什么我在这里列出了一些技巧和解决方案。

1.  资源和基本操作
2.  列表和分页
3.  多对多关系
4.  字段过滤
5.  长期运行的操作
6.  并发处理
7.  版本控制
8.  将资源组合成复合材料
9.  多语言字段

# 1.资源和基本操作

API 是关于资源和交互的。资源可以拥有允许客户端与之交互的全部或部分基本操作。客户端可以*创建、替换、更新、删除*和*检索资源。所有这些操作都有一个定义良好的 HTTP 动词映射。*

让我们看一个例子。给定资源*用户，*客户端可以通过以下端点与之交互:

```
*// create the resource*
**POST /users***// retrieve the details of the resource identified with the given id*
**GET /users/:id***// partially update the content identified with the given id*
**PATCH /users/:id***// replace the content identified with the given id*
**PUT /users/:id***// delete the content identified with the given id*
**DELETE /users/:id**
```

为了**创建**新用户，客户端提交以下请求:

```
POST /users HTTP/1.1
Host: example.com
Content-Type: application/json{
  "name": "bob",
  "age": 76
}
```

为了**检索**用值 8646291 标识的资源，客户端提交以下请求:

```
GET /users/*8646291* HTTP/1.1
Host: example.com
```

为了**部分更新**用值 8646291 标识的资源，客户端提交以下请求:

```
PATCH /users/*8646291* HTTP/1.1
Host: example.com
Content-Type: application/json{
  "name": "bob-update"
}
```

补丁请求部分更新，所以在上面的例子中，属性`age`保持不变，只有*名称*被更新。

当无法使用 PATCH 时，请改用 POST 动词。不要超载放。HTTP 定义PUT 是为了完全更新或替换一个资源[ [RFC7231](https://tools.ietf.org/html/rfc7231#section-4.3.4) ] *。*

为了用值 8646291 替换用户的内容，客户端提交以下请求:

```
PUT /users/*8646291* HTTP/1.1
Host: example.com
Content-Type: application/json{
  "age": 54
}
```

在 PUT 请求之后，由给定 id 标识的用户将丢失属性`name`，因为 PUT 请求用请求中提供的主体替换了用户信息。

最后，为了**删除**用值 8646291 标识的资源，客户端提交以下请求:

```
DELETE /users/*8646291* HTTP/1.1
Host: example.com
```

# 2.列表和分页

客户机可以用 GET 动词检索多个元素，并用查询参数过滤它们。结果必须分页，最常用的分页是*基于光标的分页*和*基于偏移量的分页*。

每个分页都有一个输入参数，它限制了页面将包含的项数，它是作为查询参数提供的，姑且称之为`limit`。

## 基于光标的分页

也许你还见过名为*基于键集的分页，*当你有一个非常大的数据集时，它是最有效的分页方式，因为在大多数实现下，它比*基于偏移量的分页*执行得更好。

当客户端需要一个集合时，服务器用条目进行回复，每个条目中的*提供一个光标*，在服务器响应时，该光标落在它所包含的条目上。这里举个例子:

```
**# REQUEST**
GET /users?limit=100 HTTP/1.1
Host: example.com**# RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8{
  "items": [
    {
      "id": 123,
      "meta":{
        **"cursor": "js3Hsji3nj"**
      }
    },
    // other items ...
    {
      "id": 426,
      "meta":{
        **"cursor": "ke3Gdk1xyi"**
      }
    }
  ]
}
```

正如你所看到的**每个**项目都有一个属性`meta.cursor`，这个光标是一个随机的字符串，它标记了项目列表中的一个特定项目，可以用来检索下一个或上一个元素，只要它作为`after`和/或`before`参数的值。

当提供了`after`时，返回的项目必须将紧跟在光标之后的项目作为其第一个项目。如果游标之后没有任何项，则返回的集合必须为空。当提供了`before`时，返回的集合的最后一项必须是紧接在光标前面的项。客户端提交以下请求以检索下一个、前一个或范围元素之间的内容:

```
**# RETRIEVE NEXT ELEMENTS**
GET /users?after=ke3Gdk1xyi&limit=100 HTTP/1.1
Host: example.com**# RETRIEVE PREVIOUS ELEMENTS**
GET /users?before=js3Hsji3nj&limit=100 HTTP/1.1
Host: example.com**# RETRIEVE ELEMENTS BETWEEN TWO ELEMENT**
GET /users?after=ke3Gdk1xyi&before=js3Hsji3nj&limit=100 HTTP/1.1
Host: example.com
```

## 基于偏移量的分页

*基于偏移量的分页*允许客户端跳转到某个页面，但大多数时候对于非常大的数据集性能较差，它比其他分页更广为人知。

客户端提交以下请求来检索集合:

```
**# REQUEST**
GET /users?limit=100 HTTP/1.1
Host: example.com**# RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8{
  "items": [
    // the results
  ]
}
```

要检索下一页，客户端必须增加`skip`查询参数:

```
*// retrieve second page*
GET /users?skip=100&limit=100 HTTP/1.1
Host: example.com*// retrieve third page*
GET /users?skip=200&limit=100 HTTP/1.1
Host: example.com
```

## 页面参考

您可以使用页面引用系统简化分页任务，提供一个指向页面的不透明指针，或者换句话说，一个标记特定页面的光标。

页面引用或页面光标通常对页面位置进行编码(加密)，即第一个或最后一个页面元素的标识符、分页方向以及所应用的查询过滤器，以安全地重新创建集合。

让我们看一个显示页面引用的例子，一个客户端提交一个请求来检索第一个页面:

```
**# REQUEST**
GET /users?limit=100 HTTP/1.1
Host: example.com**# RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8{
  "items": [
    // the results
  ],
  "paging": { 
    "prev": "kewIJbwDS2Bsja...", 
    "next": "dFRdkdek2KLmcd..."
  }
}
```

`paging.next`是客户端应该用来接收下一批响应的*页面引用*，而`paging.prev`是客户端应该用来收集前一批响应的*页面引用*:

```
GET /users?page_ref=dFRdkdek2KLmcd&limit=100 HTTP/1.1
Host: example.com
```

在第一次请求之后，*限制*参数是 URL 中除了 *page_ref* 之外的唯一参数，这是因为在请求之间修改*限制*是安全的。可能破坏请求的参数，比如排序顺序和过滤器，被直接嵌入到 *page_ref* 中，或者以某种方式存储起来。尝试添加或修改过滤器或订单参数将导致请求失败。如果需要不同的顺序或过滤，则必须从第一页重新开始。还需要注意的是，页面引用通常是临时的，不应该保存起来供以后使用。

# 3.多对多关系

有时两个资源创建一个*多对多*关系，你可以创建一个新的资源来表示这个关系，让我们看一个例子；

给定两种资源；*学生*和*课程*，每个学生可以评一门*课程。我们可以创建一个新的资源，它明确了学生和课程之间的关系，让我们称之为学生-课程-费率，让我们将操作映射到 HTTP 动词；*

```
*// add a student-course rate*
**POST /student-course-rates***// list all student-course rates filtering by student or course*
**GET /student-course-rates***// delete a rate identified by the given id*
**DELETE /student-course-rates/:id**
```

学生可以通过以下方式向课程添加费率:

```
**# REQUEST**
POST /student-course-rates HTTP/1.1
Host: example.com
Content-Type: application/json{
  "studentId": "3298wdi28dh28wid92",
  "courseId": "93710949600282",
  "rate": 10
}**# RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8{
  "id": 1239836164989016,
  "student": {
    "id": "3298wdi28dh28wid92",
    "age": 18
  },
  "course": {
    "id": "93710949600282",
    "description": "..."
  },
  "rate": 10
}
```

检索一门课程所有费率非常简单:

```
**# REQUEST**
GET /student-course-rates?course=93710949600282&limit=10 HTTP/1.1
Host: example.com**# RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8{
  "items": [
    {
       "id": 1239836164989016,
       "student": {
         "id": "i-am-a-student@college.com",
         "age": 18
       },
       "course": {
         "id": "93710949600282",
         "description": "..."
       },
       "rate": 10
    },
    // other results ...
  ]
}
```

如您所见，*学生-课程-费用*资源有一个标识符，属性`id`，您也可以省略它并使用对 *(studentId，courseId)* ，这样可以通过查询参数删除该对；`DELETE /student-course-rates?course=..&student=...`

# 4.字段过滤

有时由于性能原因，客户端需要选择响应中应该包含哪个属性，一个好的方法是需要一个查询参数`fields`，它包含一个逗号分隔的属性列表。

给定资源*用户*:

```
**# REQUEST**
GET /users/12fw342ej1 HTTP/1.1
Host: example.com**#RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{ 
  "id": "12fw342ej1",
  "name": {
    "familyName": "Muro",
    "givenName": "Rupert"
  },
  "age": 67
}
```

客户端可以选择应该通过以下请求返回哪些属性:

```
**# REQUEST**
GET /users/12fw342ej1?fields=name.familyName%2Cage HTTP/1.1
Host: example.com**#RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{ 
  "name": {
    "familyName": "Muro"
  },
  "age": 67
}
```

您还可以将字段的子集映射到预定义的样式，这样客户就可以选择提供查询参数`style`的样式(预定义的字段子集)。

比如说；我们可以将字段`id`、`name.familyName`和`age`映射到样式`compact`，并将`id`、`name.familyName`、`name.givenName`和`age`映射到样式`complete`。

```
**# REQUEST the compact style** 
GET /users/12fw342ej1?style=compact HTTP/1.1
Host: example.com**# RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{ 
  "id": "12fw342ej1",
  "name": {
    "familyName": "Muro"
  },
  "age": 67
}**# REQUEST the complete style**
GET /users/12fw342ej1?style=complete HTTP/1.1
Host: example.com**# RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{ 
  "id": "12fw342ej1",
  "name": {
    "familyName": "Muro",
    "givenName": "Rupert"
  },
  "age": 67
}
```

# 5.长期运行的操作

为了提高可伸缩性和简化部署，web 服务必须保持尽可能低的响应时间，但有时我们需要计算长时间运行的操作，我们如何做到这一点呢？

首先，创建一个代表长时间运行的操作的资源，当客户端向操作资源提交一个 *GET* 请求时，根据操作的当前状态，回复如下:

*   **运行仍在运行**:回复状态码 *200 (Ok)* 和运行状态的表示。
*   **操作以成功**结束:回复状态码 *303(见其他)*和包含 *URI* 的*位置*头到创建的资源。
*   **操作以故障**结束:回复状态码 *200 (Ok)* 和给出故障信息的操作状态表示。

让我们用一个例子来看看解决方案；设计一个从 URI 中提取摘要的 web 服务；我们有两种资源:*汇总*和*提取-任务；*

```
*// retrieve a summary identified by the given id*
**GET /summary/:id**// create a long running task that will extract the summary
**POST /extraction-task**// return information of the task identified with the given id
**GET /extraction-task/:id**
```

客户端可以通过 *POST* 请求创建新的提取任务，服务器将回复状态代码 *202(已接受)*和新资源的表示，以及任何有用的信息，例如客户端可以重试检查操作状态的日期(`checkAfter`属性)。

```
**# REQUEST**
POST /extraction-task HTTP/1.1
Host: example.com
Content-Type: application/json{
  "from": {
    "uri": "https://extract.from.here.com"
  }
}**# RESPONSE**
HTTP/1.1 202 Accepted
Content-Type: application/json;charset=UTF-8
Content-Location: [https://example.com/](http://www.example.org/images/task/1)extraction-task[/3](http://www.example.org/images/task/1)48wd39{
  "id": [3](http://www.example.org/images/task/1)48wd39,
  "state": "pending",
  "checkAfter": "2019-01-10T22:32:12Z",
  "info": {
    "from": {
      "uri": "https://extract.from.here.com"
    }
  }
}
```

然后，客户端可以使用 *GET* 请求来监控状态，如果服务器仍在处理操作，它将回复为:

```
**# REQUEST**
GET /extraction-task/[3](http://www.example.org/images/task/1)48wd39 HTTP/1.1
Host: example.com**# RESPONSE**
HTTP/1.1 202 Accepted
Content-Type: application/json;charset=UTF-8{
  "id": [3](http://www.example.org/images/task/1)48wd39,
  "state": "pending",
  "checkAfter": "2019-01-10T22:32:12Z",
  "info": {
    "from": {
      "uri": "https://extract.from.here.com"
    }
  }
}
```

当服务器成功完成操作时，它将回复一个 [303 ( *参见其他*](https://en.wikipedia.org/wiki/HTTP_303) *)* ，这意味着结果可以在另一个使用 *GET* 方法的 URI 下找到，这并不意味着资源已经
移动到一个新的位置:

```
**# REQUEST**
GET /extraction-task/[3](http://www.example.org/images/task/1)48wd39 HTTP/1.1
Host: example.com**# RESPONSE**
HTTP/1.1 303 See Other
Location: [h](https://www.example.com/extraction-task/348wd39)ttps://example.com/summary/239rfh392
Content-Location: [https://example.com/](http://www.example.org/images/task/1)extraction-task[/3](http://www.example.org/images/task/1)48wd39{
  "id": [3](http://www.example.org/images/task/1)48wd39,
  "state": "completed",
  "info": {
    "from": {
      "uri": "https://extract.from.here.com"
    }
  },
  "finishDate": "2019-01-10T22:35:11Z"
}
```

如果操作失败，服务器将回复为:

```
**# REQUEST**
GET /extraction-task/[3](http://www.example.org/images/task/1)48wd39 HTTP/1.1
Host: example.com**# RESPONSE**
HTTP/1.1 200 OK
Location: [h](https://www.example.com/extraction-task/348wd39)ttps://example.com/summary/239rfh392
Content-Location: [https://example.com/](http://www.example.org/images/task/1)extraction-task[/3](http://www.example.org/images/task/1)48wd39{
  "id": [3](http://www.example.org/images/task/1)48wd39,
  "state": "failed",
  "info": {
    "from": {
      "uri": "https://extract.from.here.com"
    }
  },
  "finishDate": "2019-01-10T22:35:11Z",
  "detail": "The URI doesn't exist (status code 404)."
}
```

如果您更喜欢回调风格的解决方案，只需要在操作创建期间提供一个 URI，当操作结束时，客户端会得到通知；

```
**# REQUEST**
POST /extraction-task HTTP/1.1
Host: example.com
Content-Type: application/json{
  "from": {
    "uri": "https://extract.from.here.com"
  },
  "notifyOn": "https://client.com"
}
```

# 6.并发处理

一个服务器可以同时服务许多客户端，这增加了引发并发问题的机会，例如，两个客户端同时用一个 *PUT* 或 *POST* 请求修改同一个资源。解决方案来自使用条件请求的 RFC([RFC 7232](https://tools.ietf.org/html/rfc7232))。

条件请求要求服务器提供一个或两个条件头；*每个资源的最后修改*和 *ETag* 。要进行条件请求，客户端必须发送一个或两个条件头；*如果-未修改-自*和*如果-匹配*。

让我们看一个例子:目标是向资源*用户提供条件请求。*服务器必须提供两个头 *Last-Modified* 和 *ETag* 中的一个或两个，在我们的例子中，我们将提供两个；

```
**# REQUEST**
GET /users/12fw342ej1 HTTP/1.1
Host: example.com**# RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
ETag: "abcec491d0a4e8ecb8e14ff920622b9c"
Last-Modified: Sun, 05 Jan 2019 14:14:52 GMT{ 
  "id": "12fw342ej1",
  "name": {
    "familyName": "Muro",
    "givenName": "Rupert"
  },
  "age": 67
}
```

为了符合条件请求，客户端必须提供两个标头 *If-Unmodified-Since* 和 *If-Match 中的一个或两个。*如果没有提供，服务器将回复一个 *403(禁止)*，在响应正文中解释原因。

```
**# REQUEST** PUT /users/*8646291* HTTP/1.1
Host: example.com
Content-Type: application/json{
  "age": 54
}**# RESPONSE**
HTTP/1.1 403 Forbidden
Content-Type: application/json;charset=UTF-8{ 
  "code": "120",
  "message": "The conditional headers are required; If-Unmodified-Since and/or If-Match"
}
```

如果客户端在请求中提供了至少一个条件头，服务器必须将它们与 *Last-Modified* 和/或 *ETag* 的当前值进行比较。如果它们匹配，它可以处理更新并返回 *200 (OK)* 或 *204(无内容)*。

```
**# REQUEST** PUT /users/*8646291* HTTP/1.1
Host: example.com
If-Unmodified-Since: Sun, 05 Jan 2019 14:14:52 GMT
If-Match: "abcec491d0a4e8ecb8e14ff920622b9c"
Content-Type: application/json{
  "age": 54
}**# RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
ETag: "1e0e5a0fb102db75aa36d4356936fe4c"
Last-Modified: Sun, 05 Jan 2019 14:15:02 GMT{ 
  "id": "*8646291*",
  "age": 54
}
```

如果没有，服务器必须返回状态码 *412(前提条件失败)*，在响应体中解释原因。

```
**# REQUEST** PUT /users/*8646291* HTTP/1.1
Host: example.com
If-Unmodified-Since: Sun, 05 Jan 2019 13:14:52 GMT
If-Match: "b1b3833b514f4b4a5207b572405e786f"
Content-Type: application/json{
  "age": 54
}**# RESPONSE**
HTTP/1.1 402 Precondition Failed
Content-Type: application/json;charset=UTF-8{ 
  "code": "121",
  "message": "The provided conditional headers doesn't match current values; The request rely on stale informations"
}
```

# 7.版本控制

有时我们需要对 API 进行版本化，提供不同的版本会使理解和维护 API 变得非常复杂，您应该将它作为最后的手段来使用。您可以通过头文件*接受*和*内容类型*使用*媒体类型版本控制*。让我们看一个关于资源*用户的例子；*

```
**# REQUEST VERSION 1**
GET /users/12fw342ej1 HTTP/1.1
Host: example.com
Accept: application/json;version=1**# RESPONSE VERSION 1**
HTTP/1.1 200 OK
Content-Type: application/json;version=1;charset=UTF-8{ 
  "id": "12fw342ej1",
  "familyName": "Muro",
  "givenName": "Rupert"
  "age": 67
}**# REQUEST VERSION 2**
GET /users/12fw342ej1 HTTP/1.1
Host: example.com
Accept: application/json;version=2**# RESPONSE VERSION 2**
HTTP/1.1 200 OK
Content-Type: application/json;version=2;charset=UTF-8{ 
  "id": "12fw342ej1",
  "name": {
    "familyName": "Muro",
    "givenName": "Rupert"
  },
  "age": 67
}
```

# 8.将资源组合成复合材料

有时需要在同一个地方显示许多资源，客户端必须调用几个端点，然后组合它们需要的表示。根据客户端使用模式、性能和延迟要求，我们可以创建一个聚合多个资源的新资源。

我们来做一个例子；我们需要显示一个概括用户财务状况的页面，该页面需要显示以下资源；用户信息、前 10 项投资、最后 10 项银行记录、总余额和信用卡限额。分别要求每种资源会导致性能问题。

```
**# REQUIRE EACH REQUEST**
GET /users/12fw342ej1 HTTP/1.1
Host: example.com
Accept: application/jsonGET /investiments?user=12fw342ej1 HTTP/1.1
Host: example.com
Accept: application/jsonGET /bank-records?user=12fw342ej1 HTTP/1.1
Host: example.com
Accept: application/jsonGET /credit-card?user=12fw342ej1 HTTP/1.1
Host: example.com
Accept: application/jsonGET /bank-account/ew239wqw21ui32une HTTP/1.1
Host: example.com
Accept: application/json
```

为了解决这个问题，创建一个聚合结果的资源，让我们称之为*财务报告*，因为它与一个用户严格相关，所以它可以是一个子资源，唯一可用的动词是*GET*；

```
**# REQUIRE EACH REQUEST**
GET /users/12fw342ej1/financial-report HTTP/1.1
Host: example.com
Accept: application/json**# RESPONSE** HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8{ 
  "userInfo": { .. },
  "lastInvestiments": [...],
  "lastBankRecords": [...],
  "bankAccount": 12321,
  "creditCartLimits": {...}
}
```

# 9.多语言字段

HTTP 为语言内容协商提供了两个头；*接受语言*和*内容语言*。 *Accept-Language* 报头由客户端提供，用于通知服务器首选语言，而 *Content-Language* 报头由服务器在响应中提供。

多语言字段的结构应该包含所有翻译和一个基于 *Accept-Language* 头填充的值，让我们看一个例子，一个客户需要一个资源 *product* ，它具有作为多语言字段的*描述*属性。

```
**# REQUEST**
GET /products/782hb1yufhd8923 HTTP/1.1
Host: example.com
Accept-Language: en,en-US,it
Accept: application/json**# RESPONSE**
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Content-Language: en
Vary: Accept-Language{ 
  "id": "782hb1yufhd8923",
  "description": {
    "localizedValue": "This is a description",
    "translations": [
      {
        "lang": "en", 
        "value": "This is a description"
      },
      {
        "lang": "it", 
        "value": "..."
      }
    ]
   }
}
```

如您所见，字段描述有一个名为 *localizedValue* 的属性，该属性根据客户端提供的 *Accept-Language* 被赋值为正确的翻译。如果缺少标题，请选择默认语言。

要创建产品，不需要属性 *localizedValue* :

```
**# REQUEST**
POST /posts HTTP/1.1
Host: example.com
Content-Type: application/json{ 
  "description": { 
    // *the 'localizedValue' must not be provided*
    "translations": [
      {
        "lang": "en", 
        "value": "This is a description"
      },
      {
        "lang": "it", 
        "value": "..."
      }
    ]
   }
}
```

# 结论

在未来，我将为每个主题创建一篇单独的文章，以深入探索给定的解决方案，并在某些情况下显示替代方案。让我知道你是否发现了一些有用的东西，或者你是否有一些建议来改进上述解决方案。

![](img/b7f30e3dc77a2cd51919320bb921a81e.png)

[洪林](https://unsplash.com/@lh1me?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照