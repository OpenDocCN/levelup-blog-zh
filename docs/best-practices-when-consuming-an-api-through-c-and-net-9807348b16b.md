# 通过 C#和使用 API 的最佳实践。网

> 原文：<https://levelup.gitconnected.com/best-practices-when-consuming-an-api-through-c-and-net-9807348b16b>

![](img/66fb9d15467be9d0499d4f7b5d34b48e.png)

让我们来讨论一下使用第三方 API 的最佳实践——甚至是使用您自己的 API 的最佳实践！

# 抢一个官方图书馆

如果你幸运的话，将会有一个官方的客户端库作为你正在使用的 API 的 Nuget 包。如果有，这通常是最好的路线，因为它会节省你很多工作。你只需安装软件包，就可以开始了。您可以通过 Nuget 跟踪库的任何更新。

大多数真正流行的 API 都有官方的。NET 库，但并不总是这样。有时你会得到一个没有针对性的 API。NET 开发人员，或者他们正在与微软竞争，或者类似性质的东西，然后可能很难找到。直接网支持。这完全取决于开发者社区。

# 不要抢非官方图书馆

如果没有一个官方的客户端库，那么对于那些声称是为你想要使用的 API 开发的随机库，要非常小心。这些通常是实验，辅导项目，未完成的，或不受支持的。你可能会受到诱惑，认为使用别人开始的东西会节省你的时间，但最终你可能会浪费时间去解释它是如何工作的，并找到他们没有解决的问题。

你是个很棒的程序员。你应该自己写 API 客户端库！

编写自己的 API 客户端让您受益于了解代码，并且只包含您真正感兴趣或计划使用的 API 部分。当没有官方库时，你花在编写自己的代码上的时间通常是一项不错的投资。

# 创建 API 客户端的步骤

拉起第三方 API 文档，让我们开始吧！

# DTO 班级

我总是从将我需要的 API 部分映射到本地数据传输对象类开始。dto 被用作保存数据并将其传递到应用程序其他区域的容器。DTO 类不包含任何逻辑，只包含表示数据的属性。例如:

```
public class ExampleDTO
{
	public int Id { get; set; }
	public string FirstName { get; set; }
	public string LastName { get; set; }
	public string DisplayName { get; set; }
}
```

创建属性与 API 文档中列出的对象的属性相匹配的类。您在这里所做的是将外部 API 建模为强类型对象，您可以在自己的本地代码中使用这些对象。

然而，有一点需要注意，API 几乎只返回 JSON。JSON 属性命名约定与 C#的不同之处在于它们通常是 camelcase。C#中的属性“ExampleProperty”在 JSON 中可能会被表示为“exampleProperty”。即使 API 作者遵循惯例也是如此。你可能会发现他们使用下划线，比如“example_property”。

将从 API 获得的 JSON 反序列化到本地 dto 可能需要 DataContract 的帮助。DataContract 确切地告诉我们的类外部数据的内容。例如:

```
using System.Runtime.Serialization;[DataContract]
public class ExampleDTO
{
	[DataMember(Name = "id")]
	public int Id { get; set; }
	[DataMember(Name = "first_name")]
	public string FirstName { get; set; }
	[DataMember(Name = "last_name")]
	public string LastName { get; set; }
	[DataMember(Name = "display_name")]
	public string DisplayName { get; set; }
}
```

这里的[DataMember]注释将每个外部属性名转换为一个本地属性。如果你给本地属性取和 API 一样的名字，你不会得到任何编译错误或者让 Intellisense 生气…

```
public string first_name { get; set; }
```

然而，那是糟糕的 C#。努力使用[DataContract]和[DataMember]注释使数据代表 C#标准。

除了将从 API 返回的内容映射到本地类之外，您还需要为它们期望您作为请求数据发送的所有内容创建 DTO 类。这是一条双行道。这些都是用同样的方法做的。

```
using System.Runtime.Serialization;[DataContract]
public class ExampleRequest
{
	[DataMember(Name = "id")]
	public int Id { get; set; }
}
```

现在，您的属性“id”被正确地格式化为“Id”，这是他们所期望的。

# 创建可重用的 RestClient

在您完全建模了 API(或者只是您计划使用的部分)之后，下一步是编写一个客户端，它将完成您的应用程序和 API 之间的通信工作。客户端发送请求并接收数据作为回报。

幸运的是，您真的只需要编写一个 RestClient，并且不需要做太多改变就可以使用不同的 API。在 [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) 中，调用 API 的方法只有这么多。通常它是一个 GET 或 POST 调用(虽然偶尔也有一个 PUT 调用来传输文件)。如果它是一个 GET 调用，那么您可能需要将查询字符串参数与 URL 放在一起。如果它是 POST 调用，您将向它发送 JSON。您可以将所有这些简化成一个可重用的 RestClient 类。

下面是一个客户端示例:

```
using ExampleAPIClient.Models;
using ExampleAPIClient.Utilities;
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;namespace ExampleAPIClient.Client
{
    public class RestClient : HttpClient
    {
        private string _baseUri = "https://external-api.com"; private TokenResponse _token; public RestClient()
        {
            InitializeTlsProtocol();
        } public async Task<T> GetAsync<T>(string url, Dictionary<string, string> query)
        {
            await SetTokenAsync(); DefaultRequestHeaders.Clear();
            DefaultRequestHeaders.CacheControl = new CacheControlHeaderValue() { NoCache = true };
            DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", _token.AccessToken); using (HttpResponseMessage response = await GetAsync(Utils.AddQueryString(_baseUri + url, query)))
            {
                if (response.IsSuccessStatusCode)
                {
                    return await response.Content.ReadAsAsync<T>();
                } throw new Exception(response.ReasonPhrase);
            }
        } public async Task<T> GetAsync<T>(string url)
        {
            await SetTokenAsync(); DefaultRequestHeaders.Clear();
            DefaultRequestHeaders.CacheControl = new CacheControlHeaderValue() { NoCache = true };
            DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", _token.AccessToken); using (HttpResponseMessage response = await GetAsync(_baseUri + url))
            {
                if (response.IsSuccessStatusCode)
                {
                    return await response.Content.ReadAsAsync<T>();
                } throw new Exception(response.ReasonPhrase);
            }
        } public async Task<T> PostAsync<T>(string url, string data)
        {
            await SetTokenAsync(); DefaultRequestHeaders.Clear();
            DefaultRequestHeaders.CacheControl = new CacheControlHeaderValue() { NoCache = true };
            DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", _token.AccessToken); var content = new StringContent(data, Encoding.UTF8, "application/json"); using (HttpResponseMessage response = await PostAsync(_baseUri + url, content))
            {
                if (response.IsSuccessStatusCode)
                {
                    return await response.Content.ReadAsAsync<T>();
                } throw new Exception(response.ReasonPhrase);
            }
        } public async Task<T> PutAsync<T>(string url, FileStream fs)
        {
            await SetTokenAsync(); DefaultRequestHeaders.Clear();
            DefaultRequestHeaders.CacheControl = new CacheControlHeaderValue() { NoCache = true };
            DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", _token.AccessToken); using (HttpResponseMessage response = await PutAsync(_baseUri + url, new StreamContent(fs)))
            {
                if (response.IsSuccessStatusCode)
                {
                    return await response.Content.ReadAsAsync<T>();
                } throw new Exception(response.ReasonPhrase);
            }
        } private void InitializeTlsProtocol()
        {
            ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        } private async Task SetTokenAsync()
        {
            if (_token == null || _token.Expiration > DateTime.Now)
            {
                DefaultRequestHeaders.Clear();
                DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", "SOME CREDENTIAL SCHEME"); var content = new StringContent("grant_type=client_credentials", Encoding.UTF8, "application/x-www-form-urlencoded"); using (HttpResponseMessage response = await PostAsync(_baseUri + "/some-path-to-get/token", content))
                {
                    if (response.IsSuccessStatusCode)
                    {
                        _token = await response.Content.ReadAsAsync<TokenResponse>();
                    }
                    else
                    {
                        throw new Exception(response.ReasonPhrase);
                    }
                }
            }
        }
    }
}
```

# RestClient 的剖析

## 任务

虽然这不是绝对必要的，但最佳实践是让所有调用都成为异步任务。您不知道请求需要多长时间才能完成，并且您不希望阻塞的调用占用您的用户界面。

## 证明

在调用 API 时，通常会涉及到某种身份验证和授权。有标准的认证方案，比如 OAuth2，但是单个 API 的认证也可以完全定制。大多数都涉及某种令牌或其他数据，您必须将它们作为查询字符串参数或头部添加到调用中。它们通常也会过期。您的代码应该持有这个令牌，并检查是否过期，只有在它过期时才调用获取新令牌。

## HTTP 方法

虽然 POST 请求作为单个字符串传递给 JSON，但是 GET 请求通常需要多个查询字符串参数一起传递。在这里，我们通过将单个参数作为字典传递给方法来简化这一过程。我们还有一个实用程序，可以将字典翻译成查询字符串。看起来是这样的:

```
using System;
using System.Collections.Generic;
using System.Net;
using System.Text;namespace ExampleAPIClient.Utilities
{
    public partial class Utils
    {
        public static string AddQueryString(string uri, IDictionary<string, string> queryString)
        {
            if (uri == null)
            {
                throw new ArgumentNullException(nameof(uri));
            } if (queryString == null)
            {
                throw new ArgumentNullException(nameof(queryString));
            } return AddQueryString(uri, (IEnumerable<KeyValuePair<string, string>>)queryString);
        } private static string AddQueryString(
            string uri,
            IEnumerable<KeyValuePair<string, string>> queryString)
        {
            if (uri == null)
            {
                throw new ArgumentNullException(nameof(uri));
            } if (queryString == null)
            {
                throw new ArgumentNullException(nameof(queryString));
            } var anchorIndex = uri.IndexOf('#');
            var uriToBeAppended = uri;
            var anchorText = ""; if (anchorIndex != -1)
            {
                anchorText = uri.Substring(anchorIndex);
                uriToBeAppended = uri.Substring(0, anchorIndex);
            } var queryIndex = uriToBeAppended.IndexOf('?');
            var hasQuery = queryIndex != -1; var sb = new StringBuilder();
            sb.Append(uriToBeAppended);
            foreach (var parameter in queryString)
            {
                sb.Append(hasQuery ? '&' : '?');
                sb.Append(WebUtility.UrlEncode(parameter.Key));
                sb.Append('=');
                sb.Append(WebUtility.UrlEncode(parameter.Value));
                hasQuery = true;
            } sb.Append(anchorText);
            return sb.ToString();
        }
    }
}
```

大概就是这么多了。现在，您已经准备好开始使用客户端打电话了！

# 在代码中使用 RestClient

您可以在控制器、服务或架构中任何有意义的地方使用 RestClient。一般结构是:

```
public async Task<ExampleDTO> ExampleMethod(ExampleRequest request)
{
	// Serialize the request into JSON.
	var data = JsonConvert.SerializeObject(request); // Post the data and then deserialize the response into ExampleDTO.
	return await _client.PostAsyc<ExampleDTO>("some-end-point-url", data);
}
```

这几行代码将成为所有 API 调用的要点。您已经完成了所有繁重的工作，现在您可以从坚实的基础架构中获益。

# 一些额外的事情要考虑

## 您对外部 API 没有任何控制权，因此错误处理比您用自己的代码编程更重要。

你永远不应该假设一个 API 会正常工作，甚至在你需要的时候是可用的。诊断一个问题可能具有挑战性。有时返回的错误代码是有帮助的，其他时候它们只是增加了混乱。

## 大多数 API 在某种程度上都有速率限制。

这意味着你只能在特定的时间段内(每天、每小时等)接到一定数量的电话。也许这就是你花钱所能得到的全部，或者也许他们减少了通话量以限制服务器的压力。即使通话没有费率限制，你也可能要为计量使用付费，这意味着你打的电话越多，花费就越多。

在与第三方系统集成时，最好考虑如何减少这种交互。你真的需要在每次用户请求你的页面时都给他们打电话吗？你能缓存数据并减少刷新频率吗？

*示例场景:*您的任务是在页面上显示一个 twitter feed。每次显示该页面时，你都可以获取最新的推文。然而，如果页面在一天内被访问 1000 次，那就是 1000 次调用。如果你知道你的营销部门实际上一天只发布四条推文，那么更有效的方法是缓存内容，并且每两个小时调用一次 API，只有在缓存过期的时候，这样每天调用 4 次而不是 1000 次。这可能意味着你有一个稍微过时的推文列表，但它更有效率和成本效益。

# 本次讨论的一些关键要点

*   使用第三方 API 时，尽量找一个定期维护的官方库。
*   如果没有官方库，不要害怕自己写代码。
*   您应该按照文档将 API 建模成可以在本地使用的类。
*   您的 RestClient 将成为一个有价值的基础设施，您只需编写一次，就可以反复使用。
*   当您无法控制第三方服务时，错误处理是必须的。
*   练习缓存，尽可能减少 API 消耗。

这绝不是使用 API 的所有最佳实践，但希望它能帮助您入门。编码快乐！