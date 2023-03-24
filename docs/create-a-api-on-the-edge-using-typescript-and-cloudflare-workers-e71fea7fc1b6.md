# 使用 TypeScript 和 Cloudflare Workers 在边缘创建 API

> 原文：<https://levelup.gitconnected.com/create-a-api-on-the-edge-using-typescript-and-cloudflare-workers-e71fea7fc1b6>

![](img/c5a15c98f64938291963d16dede0a262.png)

照片来自 [Pexels](https://www.pexels.com/photo/opened-white-duct-with-wires-inside-1583656/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Daria Shevtsova](https://www.pexels.com/@daria?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

在我之前的帖子中，我们描述了如何使用 Cloudflare Workers 将静态生成的网站直接部署到边缘。我们还描述了一种更快部署和回滚的方法，该方法使用提交散列来决定我们将向用户提供什么版本的应用程序。

[](/use-cloudflare-javascript-workers-to-deploy-you-static-generated-site-ssg-1c518e078646) [## 使用 Cloudflare JavaScript Workers 部署静态生成的站点(SSG)

### 静态站点生成器正在成为构建和部署不需要服务器的 web 应用程序的事实上的方法…

levelup.gitconnected.com](/use-cloudflare-javascript-workers-to-deploy-you-static-generated-site-ssg-1c518e078646) 

在这篇文章中，我们将继续在此基础上添加我们应用程序的另一个难题，一个 API。我们将在 Cloudflare Workers on the edge 上构建它，并将 Workers KV 用作数据存储。

在上一篇文章中，我们获得了一个名为`wrangler`的 Cloudflare Workers CLI 工具的句柄，让我们从安装和配置它开始。

```
$ npm install -g @cloudflare/wrangler
$ wrangler config
```

## 引导 TypeScript 项目。

因为我们将使用 TypeScript，所以我们需要建立一个构建管道。我个人更喜欢使用汇总([https://rollupjs.org/guide/en/](https://rollupjs.org/guide/en/))和从另一个职位，我们已经有一个模板回购。

[](https://github.com/outsrc/my-module) [## outsrc/我的模块

### 在 GitHub 上创建一个帐户，为 outsrc/my-module 开发做贡献。

github.com](https://github.com/outsrc/my-module) 

使用这个 repo 作为模板，我们可以引导我们的 TypeScript 代码库，但是首先，我们需要在构建过程中修改一些东西。

首先，让我们修改汇总配置文件:

rollup .配置. js

这里最重要的是，我们不需要`sourcemaps`，只需要一个`es`格式的输出文件。

接下来，让我们修改一下我们的`tsconfig.json`文件，也修改一下 TypeScript 编译器选项。

tsconfig.json

这里最重要的是使用`esnext`的`module`和`target`设置，因为 Cloudflare Worker 运行时对大多数现代 JS 特性都有很好的支持。

现在，在代码方面，让我们编写一个初始 Hello Worker(在 TypeScript 上)并配置 wrangler 工具，以将我们的代码部署到 Cloudflare。

src/索引. ts

至此，我们已经准备好构建它了。

```
$ yarn build
```

检查 build 文件夹，我们可以看到我们的 build/index.js。这是我们将使用 wrangler 部署到 Cloudflare 的文件。

## 配置 Cloudflare 的 Wrangler CLI 工具

我们可以使用`wrangler`作为全局安装包，但我们也可以作为开发依赖项添加到项目中。

```
$ yarn add --dev @cloudflare/wrangler
```

我们需要为名为`wrangler.toml`的`wrangler`添加一个配置文件

```
name = "**my-cf-api**"
type = "javascript"
account_id = "**<ACCOUNT-ID>**"
entry-point = "build"
workers_dev = true
```

更改您的 Cloudflare 帐户 ID 的`**ACCOUNT_ID**`(您可以在控制面板上找到它),并执行以下操作:

```
$ wrangler publish
💁  JavaScript project found. Skipping unnecessary build!
✨  Successfully published your script to [https://my-cf-api.outsrc.workers.dev](https://my-cf-api.outsrc.workers.dev)
```

一旦部署了我们的工作人员，我们就可以使用提供的 URL 访问它:

```
$ http [https://my-cf-api.outsrc.workers.dev](https://my-cf-api.outsrc.workers.dev)
HTTP/1.1 200 OK
CF-RAY: 56200b5f38d3f369-ATL
Connection: keep-alive
Content-Length: 18
Content-Type: text/plain;charset=UTF-8
Date: Sat, 08 Feb 2020 19:35:02 GMT
Expect-CT: max-age=604800, report-uri="[https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct](https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct)"
Server: cloudflare
Set-Cookie: __cfduid=d2e97f0dc5b08e83ae7074d5118fdd94b1581190502; expires=Mon, 09-Mar-20 19:35:02 GMT; path=/; domain=.outsrc.workers.dev; HttpOnly; SameSite=Lax
Vary: Accept-Encoding{
    "hello": "worker"
}
```

## 工人预览

持续培养和部署我们的员工可能会很快变成一项单调乏味的任务。

牧马人来准备解决这个问题。我们可以在预览模式下运行。

```
$ wrangler preview
```

这将把我们的工作人员部署到一个开发环境中，并打开一个带有工作台的浏览器，在那里我们可以测试我们的端点。

![](img/f787f5db8a9c472bb9114485fb9b582c.png)

牧马人预览

测试和文档包括:

![](img/2a523fabc59d5a97b63defedd8fde974.png)

测试看起来像一个 HTTP 客户端

![](img/f3b39a5f144dff5cafa01a09824a6047.png)

手头的文件

一个额外的参数`--watch`将使它监视文件的变化，以重新部署和更新我们的测试环境。

```
wrangler preview --watch
💁  JavaScript project found. Skipping unnecessary build!
💁  watching "./"
🌀  Detected changes...
💁  JavaScript project found. Skipping unnecessary build!
👷  Updated preview with changes
```

现在，这本身是不够的，因为我们还需要在每次更改后重新构建我们的 TypeScript 应用程序。

```
$ yarn build --watch
```

在单个 yarn 脚本中组合这两个命令

```
{
  "name": "my-cf-api",
  "version": "1.0.0",
  "description": "",
  "main": "build/index.js",
  "scripts": {
    "build": "rollup -c",
    "lint": "standard",
    "format": "prettier-standard --format",
    "test": "jest --coverage",
    "publish:api": "wrangler publish",
    **"dev": "yarn build --watch & wrangler preview --watch"**
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@types/jest": "^24.0.24",
    "jest": "^24.9.0",
    "rollup": "^1.27.13",
    "rollup-plugin-commonjs": "^10.1.0",
    "rollup-plugin-node-resolve": "^5.2.0",
    "rollup-plugin-peer-deps-external": "^2.2.0",
    "rollup-plugin-typescript2": "^0.25.3",
    "standard": "^14.3.1",
    "standard-prettier": "^1.0.1",
    "ts-jest": "^24.2.0",
    "typescript": "^3.7.3"
  },
  "jest": {
    "preset": "ts-jest",
    "testEnvironment": "node"
  },
  "standard": {
    "ignore": [
      "node_modules/",
      "build/"
    ]
  }
}
```

现在，只需一个命令，我们就可以在观察模式下旋转构建和部署流程。

```
$ yarn dev
```

## 将 API 部署到不同的环境中。

默认情况下，wrangler CLI 工具会将我们的工作人员部署到一个开发环境中，该环境由我们生成的端点发出信号。

```
$ wrangler publish
💁  JavaScript project found. Skipping unnecessary build!
✨  Successfully published your script to [**https://my-cf-api.outsrc.workers.dev**](https://my-cf-api.outsrc.workers.dev)
```

现在，如果我们还想部署到我们的生产环境中，我们需要在`wrangler.toml`文件中添加一个`production`部分。

```
name = "my-cf-api"
type = "javascript"
account_id = "**<ACCOUNT-ID>**"
entry-point = "build"
workers_dev = true

[env.production]
zone_id = "**<ZONE-ID>**"
route = "**api.outsrc.dev/***"
```

对于您添加到帐户的每个域，您可以在 Cloudflare 控制面板上找到`zone_id`属性。

*(注意:* `*api.outsrc.dev*` *子域必须添加到指向不可访问 IP 的域的 DNS 区域，如* `*192.2.0.1*` *)*

`route`参数指示您的工人应该响应哪些路线。

为了立即部署到生产环境，我们做到了:

```
$ wrangler publish --env production
💁  JavaScript project found. Skipping unnecessary build!
✨  Successfully published your script to api.outsrc.dev/*
```

## 使用 KV 商店

KV Store 是一项令人惊叹的技术。用 Cloudflare 自己的话说:

> Workers KV 是一个全球性的低延迟、键值数据存储。它支持异常高的读取量和低延迟，使构建高度动态的 API 和网站成为可能，这些 API 和网站的响应速度与缓存的静态文件一样快。

使用 Worker KV 的第一步是创建一个名称空间，为此我们使用了`wrangler`工具。

```
$ wrangler kv:namespace create "TODOS"               
🌀  Creating namespace with title "my-cf-api-TODOS"
✨  Success: WorkersKvNamespace {
    id: "a9f969275ba24647b3319531531952df",
    title: "my-cf-api-TODOS",
}
✨  Add the following to your wrangler.toml:
kv-namespaces = [ 
  { binding = "TODOS", id = "a9f969275ba24647b3319531531952df" } 
]
```

并修改我们的`wrangler.toml`文件以包含 kv-namespaces 设置。

```
name = "my-cf-api"
type = "javascript"
account_id = "<ACCOUNT-ID>"
entry-point = "build"
workers_dev = true

**kv-namespaces = [
  { binding = "TODOS", id = "a9f969275ba24647b3319531531952df" }
]**

[env.production]
zone_id = "<ZONE-ID>"
route = "api.outsrc.dev/*"
```

现在，由于我们也有一个生产环境，让我们为它创建另一个名称空间。

```
$ wrangler kv:namespace create "TODOS_PRODUCTION"
🌀  Creating namespace with title "my-cf-api-TODOS_PRODUCTION"
✨  Success: WorkersKvNamespace {
    id: "97cdbbe92ebd4e349d73dd129067bb53",
    title: "my-cf-api-TODOS_PRODUCTION",
}
✨  Add the following to your wrangler.toml's "kv-namespaces" array:
{ binding = "TODOS_PRODUCTION", id = "97cdbbe92ebd4e349d73dd129067bb53" }
```

并将 kv-namespaces 设置粘贴到我们的`wrangler.toml`文件的 production 部分。

```
name = "my-cf-api"
type = "javascript"
account_id = "<ACCOUNT-ID>"
entry-point = "build"
workers_dev = true

kv-namespaces = [
  { binding = "TODOS", id = "a9f969275ba24647b3319531531952df" }
]

[env.production]
zone_id = "<ZONE-ID>"
route = "api.outsrc.dev/*"**kv-namespaces = [
  { binding = "TODOS", id = "97cdbbe92ebd4e349d73dd129067bb53" }
]**
```

*(注意，我在两个名称空间* `*TODOS*` *上使用了相同的绑定值，即使一个名称空间被创建为* `*TODOS*` *，另一个名称空间被创建为* `*TODOS_PRODUCTION*` *。)*

绑定将在 worker 上下文中创建一个全局变量`**TODOS**`。所以我们可以直接在代码中使用它。现在，这将导致 TypeScript 抱怨一个没有找到的全局对象`**TODOS**`。

为了避免这个问题，我们需要安装模块`@cloudflare/workers-types`并将主`index.ts`文件顶部的`**TODOS**`对象声明为:

```
import { KVNamespace } from '@cloudflare/workers-types'

declare const TODOS: KVNamespace
```

Todo 应用程序并不完全是最适合员工 KV 存储的应用程序类型。因为写操作是有限的，而读操作更快且不受限制。但是，这是一个简单的概念，通过在我们的基本 Hello Worker 上添加几个函数，现在我们可以向我们的 API 添加几个操作。

src/index.ts(现在带有/todos 端点)

更新、构建和部署后

```
$ curl [https://my-cf-api.outsrc.workers.dev/todos?user=ernesto](https://my-cf-api.outsrc.workers.dev/todos?user=ernesto)
[]
```

为单个用户添加待办事项:

```
$ curl -X POST -d '{"user":"ernesto","todo":"Read that book you bought"}' https://my-cf-api.outsrc.workers.dev/todos
```

正在为用户重新查询待办事项:

```
$ curl [https://my-cf-api.outsrc.workers.dev/todos\?user\=ernesto](https://my-cf-api.outsrc.workers.dev/todos\?user\=ernesto)
["Read that book you bought"]
```

让我们添加更多待办事项:

```
$ curl -X POST -d '{"user":"ernesto","todo":"Buy propane for grill"}' [https://my-cf-api.outsrc.workers.dev/todos](https://my-cf-api.outsrc.workers.dev/todos)
$ curl [https://my-cf-api.outsrc.workers.dev/todos\?user\=ernesto](https://my-cf-api.outsrc.workers.dev/todos\?user\=ernesto)
["Read that book you bought","Buy propane for grill"]
```

同时删除待办事项

```
$ curl -X DELETE -d '{"user":"ernesto","index":0}' [https://my-cf-api.outsrc.workers.dev/todos](https://my-cf-api.outsrc.workers.dev/todos)
$ curl [https://my-cf-api.outsrc.workers.dev/todos\?user\=ernesto](https://my-cf-api.outsrc.workers.dev/todos\?user\=ernesto)
["Buy propane for grill"]
```

我们的 API 正在工作…但是，它安全吗？没有那么多。任何人都可以从任何用户那里添加、查询甚至删除数据。

## 保护 API

有几种方法可以保护 API。我们在这里使用的是 JWT 访问令牌。发送带有不记名令牌的授权头是很常见的。也很容易与认证提供者集成，如 auth 0([https://auth0.com/](https://auth0.com/))或 Okta([https://www.okta.com/](https://www.okta.com/))

为此，我们需要能够从授权头中获取令牌，解码并验证它。感谢开源社区和本·查特兰(【https://github.com/bcnzer】T21)的这个惊人的主旨

[cloudflareworker-verify jwt . js](https://gist.github.com/bcnzer/e6a7265fd368fa22ef960b17b9a76488#file-cloudflareworker-verifyjwt-js)

我只是稍微重构了一下，把它转换成一个我们可以在任何地方重用的函数。

src/jwt.ts

现在，为了保护我们的端点，我们重用了这个函数。

src/索引. ts

这里的重要变化是:

*   我们可以从其他模块导入，本地或通过 node_modules。如果可能的话，Rollup 将从这些模块中移除未使用的代码。
*   我们修改了 main 函数来验证令牌，并将令牌有效负载传递给所有方法函数
*   方法函数现在使用令牌有效负载上的`sub`属性来标识用户。

构建和发布后，我们有:

```
$ http [https://my-cf-api.outsrc.workers.dev/todos](https://my-cf-api.outsrc.workers.dev/todos)
HTTP/1.1 403 Forbidden
CF-RAY: 56213e8bada4f1c6-ATL
Connection: keep-alive
Content-Length: 0
Date: Sat, 08 Feb 2020 23:04:44 GMT
Expect-CT: max-age=604800, report-uri="[https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct](https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct)"
Server: cloudflare
Set-Cookie: __cfduid=db91d31a35776ef201afbea47415e76a11581203084; expires=Mon, 09-Mar-20 23:04:44 GMT; path=/; domain=.outsrc.workers.dev; HttpOnly; SameSite=Lax
Vary: Accept-Encoding
```

我们需要传递一个带有不记名令牌的授权头，该令牌将包含一个标识我们用户的`sub`属性。

*(幸运的是，在另一篇文章中，我有一个 Auth0 的例子。因此很容易提取访问令牌来进行一些测试)*

[](/secure-nextjs-app-users-with-auth0-and-typescript-3b0a6ac3a163) [## 使用 Auth0 和 TypeScript 验证和保护 Next.js 应用程序用户

### React 和 Next.js 应用程序中的用户身份验证

levelup.gitconnected.com](/secure-nextjs-app-users-with-auth0-and-typescript-3b0a6ac3a163) 

```
$ curl https://my-cf-api.outsrc.workers.dev/todos \
-H 'Authorization: Bearer eyJhbGciOi...'
[]
```

添加 todos 变得更加容易，因为我们现在不需要指定用户。

```
$ curl -X POST -d '{"todo":"Clean the backyard"}' [https://my-cf-api.outsrc.workers.dev/todos](https://my-cf-api.outsrc.workers.dev/todos) \
-H 'Authorization: Bearer eyJhbGciOi...'$ curl https://my-cf-api.outsrc.workers.dev/todos \
-H 'Authorization: Bearer eyJhbGciOi...'
["Clean the backyard"]
```

## 克-奥二氏分级量表

> 跨源资源共享是一种机制，它允许从提供第一资源的域之外的另一个域请求网页上的受限资源。一个网页可以自由地嵌入跨源图像、样式表、脚本、iframes 和视频。(维基百科)

不幸的是，当我们的 API 在`api.outsrc.dev/*`路线上部署到生产环境中，并从主 web 域`www.outsrc.dev`访问时，将会遇到 CORS“墙”。这是所有的请求都会被阻止。

打开浏览器 JavaScript 控制台并键入:

```
> fetch('[https://my-cf-api.outsrc.workers.dev/todos'](https://my-cf-api.outsrc.workers.dev/todos'), { headers: { 'Authorization': 'Bearer eyJhbGciOi...'}})
  .then(res => console.log(res))
  .catch(err => console.log(err))Access to fetch at '[https://my-cf-api.outsrc.workers.dev/todos'](https://my-cf-api.outsrc.workers.dev/todos') from origin 'chrome-search://local-ntp' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.
GET [https://my-cf-api.outsrc.workers.dev/todos](https://my-cf-api.outsrc.workers.dev/todos) net::ERR_FAILED
(anonymous)
TypeError: Failed to fetch
```

我们需要打开 CORS 头来允许这种行为，因为我们的 API 是由 JWT 访问令牌保护的。

*(根据您的特定用例，您需要了解如何在您的应用程序上配置 CORS)*

对于我们的演示，我们将开放，允许所有来自任何来源。

src/cors.ts

并且重构了我们的工人 API

src/索引. ts

我们补充道:

*   默认 CORS 标题对象
*   `withStatus`允许传递 HTTP 状态和其他报头并注入默认 CORS 报头的函数
*   检查选项方法

让我们从浏览器中再次尝试我们的获取操作:

```
> fetch(
  '[https://my-cf-api.outsrc.workers.dev/todos'](https://my-cf-api.outsrc.workers.dev/todos'), 
  { headers: { 'Authorization': 'Bearer eyJhbGciOiJSUzI1Ni...'}}
)
  .then(res => res.json())
  .then(res => console.log(res))
  .catch(err => console.log(err))
Promise {<pending>}
["Buy propane for grill"]
```

瞧，成功了！

## 结论

*   Worker KV Store 是一个很好的快速替代方案，可以存储在边缘、最接近用户并且读写比率很高的数据。
*   工具就在那里。我们可以用 TypeScript 和牧马人预览观看模式做热重装。
*   如有必要，用 JWT 访问令牌保护我们的工人 API。
*   CORS 很重要，必须妥善处理。
*   开箱即用也支持多种环境。
*   牧马人 CLI 工具对于管理工人 KV 商店非常有用。

这篇文章的最终代码可以在以下网址找到:

[](https://github.com/outsrc/my-cf-api) [## outsrc/my-cf-api

### 来自 Cloudflare Workers API post 的代码。在 GitHub 上创建一个帐户，为 outsrc/my-cf-api 开发做出贡献。

github.com](https://github.com/outsrc/my-cf-api) 

黑客快乐…