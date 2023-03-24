# Cloudflare Workers 和无服务器 Redis 的边缘速率限制

> 原文：<https://levelup.gitconnected.com/rate-limiting-at-edge-with-cloudflare-workers-and-serverless-redis-74c0468a1873>

![](img/a1e4f45fe1079062547ab4e4a1468f29.png)

在本教程中，我们将展示如何使用 Cloudflare Workers 和 Upstash Redis 对您的应用程序进行速率限制。我们将使用[速率限制 SDK](https://github.com/upstash/ratelimit) ，它将数据保存在 Upstash Redis 中。

# Redis 设置

使用 [Upstash 控制台](https://console.upstash.com)或 [Upstash CLI](https://github.com/upstash/cli) 创建一个或多个 Redis 数据库。不要选择全局，而是在不同的区域创建多个数据库，在那里你的站点会有流量。(为什么？我们将稍后解释)在接下来的步骤中，您将需要 REST URL 和令牌。

# 项目设置

我们将使用牧马人 2 进行部署，因此安装(或升级)[牧马人 2](https://developers.cloudflare.com/workers/wrangler/) 。

为您的项目创建一个文件夹并运行`wrangler init`。选择`ts`:

```
⛅️ wrangler 2.0.7 (update available 2.0.26) ------------------------------------------------------ Using npm as package manager. 
✨ Created wrangler.toml No package.json found.
 Would you like to create one? (y/n) 
✨ Created package.json Would you like to use TypeScript? (y/n) 
✨ Created tsconfig.json Would you like to create a Worker at src/index.ts? (y/n) 
✨ Created src/index.ts
```

# 代码

更新工人功能如下:

替换上面 Redis 实例的 REST_URL 和标记。您可以根据您的流量在不同的区域添加更多的 Redis 数据库(或设置一个)。

# 测试和部署

您可以使用`wrangler dev`在本地测试该功能。

使用`wrangler publish`将您的功能部署到 Cloudflare

函数的端点将被打印出来。例如[https://cloud flare-workers-rate-limiting . upsdev . workers . dev/](https://cloudflare-workers-rate-limiting.upsdev.workers.dev/)当你连续刷新页面 5 次，应该会看到你是速率受限的。

好了，教程结束了(很容易吧？)，现在让我们进入一些细节。

# 对每个请求进行远程调用的成本不是很高吗？

如果我们不使用短暂的缓存，它可能是。[临时缓存](https://github.com/upstash/ratelimit#ephemeral-cache)是在处理程序之外定义的 map 对象。当无服务器功能激活(热)时，速率限制 SDK 使用它来缓存标识符。在处理程序之外定义它很重要，因此它将是一个全局变量。Cloudflare Workers 不保证不同的调用会使用同一个变量，但在流量高峰时缓存它还是很有帮助的。

# 为什么不是全球数据库？

Upstash Global databases 将 Redis 数据复制到多个地区。它非常适合许多边缘使用案例。但是对于速率限制，我们不需要将一个区域中的数据复制到所有其他区域。速率限制会话具有本地作用域。我们仍然可以使用一个全球数据库，但这将是不必要的成本，因为每次更新都将被复制到世界各地。这就是为什么我们选择创建多个区域性数据库，正如限速 SDK 推荐的那样。

# IP 作为标识符

我们已经使用用户的 IP 作为速率限制功能的标识符。这意味着我们限制单个用户的速率。如果您的请求中有更好的标识符(用户名、电子邮件等)，请使用它。如果您想限制总流量，可以设置一个常量字符串。或者您可以使用地区或国家( [request.cf.country](https://developers.cloudflare.com/workers/examples/country-code-redirect/) )作为标识符来限制每个地理区域的流量。

# 我可以使用自托管 Redis 吗？

Cloudflare Workers 在 V8 隔离上运行，不允许 TCP 连接。所以你不能访问自托管的 Redis、Redislabs 或 Elasticache，除非你使用 REST 代理。upshredis 有一个内置的 REST API。

# 整合到您现有的网站

您可以对现有的 web 应用程序/网站进行分级限制，而无需触及它们的代码。您只需要在 Cloudflare 中创建一个站点，并相应地为您的域设置名称服务器。因此，Cloudflare 将能够拦截对您网站的请求，并运行 Workers 功能。另外，取消对 XX 行的注释，如果请求没有速率限制，那么 Workers 函数会将请求转发到您的网站。

*原载于 2022 年 8 月 22 日*[*https://dev . to*](https://dev.to/noahfschr/rate-limiting-at-edge-with-cloudflare-workers-and-serverless-redis-4opd)*。*

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)