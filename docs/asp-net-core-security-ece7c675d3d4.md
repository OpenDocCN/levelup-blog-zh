# ASP.NET 核心安全

> 原文：<https://levelup.gitconnected.com/asp-net-core-security-ece7c675d3d4>

![](img/a3ecf6ba5f272789909dc1c4d2b2ba3c.png)

斯科特·韦伯在 [Unsplash](https://unsplash.com/s/photos/security?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

## 如何保护公共 API？

ASP。NET Core 是一个流行的框架。主要优势包括跨平台执行、高性能、内置[依赖注入](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection)和模块化 HTTP 请求管道等特性。

# 挑战

ASP。NET Core 为许多身份验证提供者提供支持，以通过众多身份验证工作流来保护应用程序。然而，在许多情况下，我们必须提供一个依赖于匿名访问的未认证 API 的 web 应用程序/站点。

例如，我们在数据库中有一个产品列表，我们想在网页上显示这些产品。我们可以编写一个 API 来提供产品列表，并让前端(网站)通过 API 检索该列表，并将它们显示在我们的公共产品网页上。

如果不应用安全级别，这样的体系结构可能是一个容易被利用的安全漏洞。

# ASP 中可用的安全控件。网

ASP。NET Core 为常见漏洞提供了解决方案，包括:

*   跨站点脚本
*   SQL 注入，
*   跨站点请求伪造(CSRF)
*   打开重定向

# 更进一步

作为开发人员，我们还应该保护我们的应用程序免受其他常见攻击媒介的影响，包括:

*   分布式拒绝服务
*   拒绝服务
*   批量数据出口
*   探测响应
*   擦

# 使用基于 IP 的请求限制操作过滤器

我们可以限制客户端在指定时间内的请求数量，以防止恶意 bot 攻击。我在 ASP.NET 核心中创建了一个基于 IP 的请求限制操作过滤器。请注意，多个客户端可能位于一个 IP 地址之后，因此我们可能希望满足我们的限制，或者将 IP 地址与其他请求数据相结合，以使请求更加独特。

为了使用过滤器，我们只需要在控制器动作之上添加 ActionAttribute。

```
[HttpGet()]  
[ValidateReferrer]  
[RequestLimit("Test-Action", NoOfRequest = 5, Seconds = 10)]  
public async Task<ActionResult> GetAsync(CancellationToken ct)  
{  
   // code here  
}
```

下面是过滤器的实现，

```
namespace Security.Api.Filters
{
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.AspNetCore.Mvc.Filters;
    using Microsoft.Extensions.Caching.Memory;

    [AttributeUsage(AttributeTargets.Method)]
    public class RequestAttribute :ActionFilterAttribute
    {
        public RequestAttribute(string name)
        {
            Name = name;
        }
        public string Name
        {
            get;
        }
        public intNoOfRequest
        {
            get;
            set;
        } = 1;
        public int Seconds
        {
            get;
            set;
        } = 1;
        private static MemoryCachememoryCache
        {
            get;
        } = new MemoryCache(new MemoryCacheOptions());
        public override void OnActionExecuting(ActionExecutingContext context)
        {
varipAddress = context.HttpContext.Request.HttpContext.Connection.RemoteIpAddress;
varmemoryCacheKey = $ "{Name}-{ipAddress}";
memoryCache.TryGetValue(memoryCacheKey, out intprevReqCount);
            if (prevReqCount>= NoOfRequest)
            {
context.Result = new ContentResult
                {
                    Content = $ "Request is exceeded. Try again in seconds.",
                };
context.HttpContext.Response.StatusCode = (int)HttpStatusCode.TooManyRequests;
            }
            else
            {
varcacheEntryOptions = new MemoryCacheEntryOptions().SetAbsoluteExpiration(TimeSpan.FromSeconds(Seconds));
memoryCache.Set(memoryCacheKey, (prevReqCount + 1), cacheEntryOptions);
            }
        }
    }
}
```

# 添加推荐人检查操作过滤器

为了防止 API 被滥用，并针对跨站点请求伪造(CSRF)攻击提供额外的保护，对发送到服务器的每个 REST API 请求的请求 Referer 头执行安全检查。

这将验证 API 请求来自何处。t 还可以防止来自 POSTMAN、REST Client 等工具的访问。

我们只需要在控制器动作之上添加 ActionAttribute。

```
[HttpGet()]  
[ValidateReferrer]  
public async Task<ActionResult> GetAsync(CancellationToken ct)  
{  
   // code here  
}
```

下面是过滤器的实现。

```
namespace Security.Api.Filters
{
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.AspNetCore.Mvc.Filters;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Linq;
    using System.Net;

    [AttributeUsage(AttributeTargets.Method)]
    public sealed class ValidateAttribute :ActionFilterAttribute
    {
        private IConfiguration _configuration;

        public ValidateAttribute() { }

        public override void OnActionExecuting(ActionExecutingContext context)
        {
            _configuration = (IConfiguration)context.HttpContext.RequestServices.GetService(typeof(IConfiguration));
base.OnActionExecuting(context);
            if (!IsValidRequest(context.HttpContext.Request))
            {
context.Result = new ContentResult
                {
                    Content = $ "Invalid header"
                };
context.HttpContext.Response.StatusCode = (int)HttpStatusCode.ExpectationFailed;
            }
        }

        private bool IsValidRequest(HttpRequest request)
        {
            string referrerURL = "";
            if (request.Headers.ContainsKey("Referer"))
            {
referrerURL = request.Headers["Referer"];
            }
            if (string.IsNullOrWhiteSpace(referrerURL)) return false;

           //Allows to check customer list
varUrls = _configuration.GetSection("CorsOrigin").Get<string[]>()?.Select(url => new Uri(url).Authority).ToList();

            //add current host for swagger calls    
var host = request.Host.Value;
Urls.Add(host);
            bool isValidClient = Urlsl.Contains(new Uri(referrerURL).Authority);
            // comapre with base uri
            return isValidClient;
        }
    }
}
```

# 添加 DoS 攻击中间件

如果我们配置了 autoscale，DoS 攻击会淹没我们的 API，使它们无法响应和/或变得昂贵。有不同的方法可以通过请求节流来避免这个问题。这里有一个选项，使用中间件来限制来自特定客户端 IP 地址的请求数量。

下面是 DosAttackMiddleware.cs 的代码

```
namespace Security.Api.Middlewares
{
    using Microsoft.AspNetCore.Http;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Threading.Tasks;
    using System.Timers;
    public sealed class DosAttackMiddleware
    {

        private static IDictionary _IpAdresses = new Dictionary();
        private static Stack _Banded = new Stack();
        private static Timer _Timer = CreateTimer();
        private static Timer _BannedTimer = CreateBanningTimer();

        private
        const int BANNED_REQUESTS = 10;
        private
        const int REDUCTION_INTERVAL = 1000;
        private
        const int RELEASE_INTERVAL = 3 * 60 * 1000; // 3 minutes    
        private RequestDelegate _next;
        public DosAttackMiddleware(RequestDelegate next)
        {
            _next = next;
        }
        public async Task InvokeAsync(HttpContexthttpContext)
        {
            string ip = httpContext.Connection.RemoteIpAddress.ToString();
            if (_Banned.Contains(ip))
            {
httpContext.Response.StatusCode = (int)HttpStatusCode.Forbidden;
            }
CheckIpAddress(ip);
            await _next(httpContext);
        }

        private static void CheckIpAddress(string ip)
        {
            if (!_IpAdresses.ContainsKey(ip))
            {
                _IpAdresses[ip] = 1;
            }
            else if (_IpAdresses[ip] == BANNED_REQUESTS)
            {
                _Banned.Push(ip);
                _IpAdresses.Remove(ip);
            }
            else
            {
                _IpAdresses[ip]++;
            }
        }

        private static Timer CreateTimer()
        {
            Timer timer = GetTimer(REDUCTION_INTERVAL);
timer.Elapsed += new ElapsedEventHandler(TimerElapsed);
            return timer;
        }

        private static Timer CreateTimer()
        {
            Timer timer = GetTimer(RELEASE_INTERVAL);
timer.Elapsed += delegate {
                if (_Banned.Any()) _Banned.Pop();
            };
            return timer;
        }

        private static Timer GetTimer(int interval)
        {
            Timer timer = new Timer();
timer.Interval = interval;
timer.Start();
            return timer;
        }

        private static TimerElapsed(object sender, ElapsedEventArgs e)
        {
            foreach (string key in _IpAdresses.Keys.ToList())
            {
                _IpAdresses[key]--;
                if (_IpAdresses[key] == 0) _IpAdresses.Remove(key);
            }
        }
    }
}
```

# 结论

未经授权的 API 很容易被滥用。我们应该通过添加额外的代码来防止明确的攻击媒介。希望这篇文章能让这些限制更容易实施，同时让这些攻击者的日子更难过。