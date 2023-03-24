# 在发展中永远摆脱“原产地被 CORS 封锁”的问题

> 原文：<https://levelup.gitconnected.com/getting-rid-of-origin-has-been-blocked-by-cors-policy-forever-969e161421ca>

摆脱 CORS 噩梦的语言独立方法，并一路学习 nginx！

![](img/9c29915f1fb8a803470048b554c8e3c2.png)

来自 [pixbay](https://pixabay.com/) 的 Credit [stocksnap](https://pixabay.com/users/stocksnap-894430/)

昨天我做了一个噩梦。我试图在前端修复一个高优先级的 bug。我很快就能修好它。我对自己真的很满意。我只是想快速地将前端连接到开发服务器，并测试它是固定的。我的应用程序在`[localhost:8080](http://localhost:8080/)`运行，开发服务器在某个`10.10.1.123:80`(嘿，你们这些黑客，不要一看到我编造的 IP 就开始做一些可疑的事情！).我开始了一切，迫不及待地想看到它的工作。但后来我看到了著名的 CORS 问题信息。

> 对位于'[http://](http://localhost:8090/handshake)10 . 10 . 1 . 123/some path ' from Origin '[http://localhost:8080](http://localhost:8080/)'的 XMLHttpRequest 的访问已被 CORS 策略阻止:对预检请求的响应未通过访问控制检查:请求的资源上不存在“Access-Control-Allow-Origin”标头。

气得我都醒了！还有另一个场景，你在本地的`8090`端口上运行你的后端，并尝试在本地的前端端口`8080`上使用它。你会做同样的噩梦。我认为几乎所有的开发人员在他们开发生涯的某个阶段都面临过这个令人难以置信的问题。我这里有一个不依赖于任何特定语言的永久解决方案。

![](img/26d5c3df2a325465171916433190f32d.png)

信用:memegenerator.net

# 步骤 1:安装 nginx

对于 mac 用户

`brew install nginx`

以下是一些有用的命令

```
brew services start nginx 
brew services status nginx
brew services stop nginx 
brew services restart nginx
```

对于 ubuntu 用户

`sudo apt-get install nginx`

一些有用的命令

```
sudo systemctl start nginx 
sudo systemctl status nginx 
sudo systemctl stop nginx 
sudo systemctl restart nginx
```

对于那些不使用 wsl2 或其他 linux 发行版的 windows 用户，我对你有信心，你可以做一些谷歌搜索并自己安装它💪

# 第二步:配置

在 mac 上，配置位于:
`/usr/local/etc/nginx/nginx.conf`

在 ubuntu 上是

`/etc/nginx/nginx.conf`

现在，用以下内容替换 conf 的服务器部分

```
server {
    listen       9090;
    server_name  localhost; #This is the main application (UI) location / {
        root   html;
        proxy_pass [http://localhost:8080/](http://localhost:8080/);
        proxy_set_header                X-Real-IP       $remote_addr;
        proxy_set_header                X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header                Cookie $http_cookie;
        proxy_pass_request_headers      on;
    }#This is the recipes microservice backend application in your  local machine. replace the proxy pass with your application url location /recipes {
        proxy_pass [http://localhost:8090/](http://localhost:8090/);
        proxy_set_header                X-Real-IP       $remote_addr;
        proxy_set_header                X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header                Cookie $http_cookie;
        proxy_pass_request_headers      on;
    } #This is the authentication backend application but in your dev machine. replace the proxy pass with your application url location /auth {
        proxy_pass [http://10.13.1.213:8081/](http://10.13.1.213:8081/);
        proxy_set_header                X-Real-IP       $remote_addr;
        proxy_set_header                X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header                Cookie $http_cookie;
        proxy_pass_request_headers      on;
    }

}
```

您应该根据您的后端 API 前缀，用您的 URL 和位置路径替换代理通行证。现在，访问前端应用程序的基本 url 将是`http://localhost:9090`。

**在您的前端应用程序中，像这样更改 API URL:**

`[http://localhost:8090/](http://localhost:8090)auth`应替换为`[http://localhost:](http://localhost:8090)9090/auth`

`[http://localhost:](http://localhost:9090)8090/recipe`应替换为`[http://localhost:](http://localhost:8091)9090/recipe`

**我来举例说明一下配置**。
Nginx 将在 9090 端口上运行。当你点击`[http://localhost:9090](http://localhost:9090)/recipes`时，它会将任何带有该配方前缀的请求重定向到`[http://localhost:8090/](http://localhost:8090/)recipes`。以下是重定向示例:

`[http://localhost:9090](http://localhost:9090)/recipes/getAllRecices` → `[http://localhost:8090/recipes/getAllRecipes](http://localhost:8090/recipes/getAllRecipes)`

`[http://localhost:9090](http://localhost:9090)/recipes/delete/id` → `[http://localhost:8090/recipes/delete/id](http://localhost:8090/recipes/delete/id)`

`[http://localhost:9090](http://localhost:9090)/auth/login` →
`[http://10.13.1.213:8081/auth/login](http://10.13.1.213:8081/auth/login)`

`[http://localhost:9090](http://localhost:9090)/auth/register` → `[http://10.13.1.213:8081/auth/register](http://10.13.1.213:8081/auth/register)`

它会转发所有 cookies 和好东西的请求。
为什么有效？因为现在你的整个申请都在同一个域名下，`[localhost](http://localhost/):9090.`所以所有的 CORS 问题都将不复存在！！

# 限制

正如你所看到的，你必须有一个对所有 API 都一样的路径前缀。否则，您必须为每个 URL 编写一个新的服务器配置。让我们通过例子来学习:

我们所有的请求都与`auth`匹配，并被转发到后端。它之所以有效，是因为我们在每个 API 的认证后端都有`auth`前缀。如果你这样设计你的 API，没有任何前缀，会发生什么？
`[http://10.13.1.213:8081/login](http://10.13.1.213:8081/login)`
`[http://10.13.1.213:8081/register](http://10.13.1.213:8081/register)`(当然是糟糕的设计)

这种路由将不起作用。因为它会将请求转发给不存在的`[http://10.13.1.213:8081/auth/login](http://10.13.1.213:8081/auth/login)`。

绕过这一点的简单方法是:

```
location /register {
    proxy_pass [http://10.13.1.213:8081/](http://10.13.1.213:8081/);
    proxy_set_header                X-Real-IP       $remote_addr;
    proxy_set_header                X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header                Cookie $http_cookie;
    proxy_pass_request_headers      on;
}location /login {
    proxy_pass [http://10.13.1.213:8081/](http://10.13.1.213:8081/);
    proxy_set_header                X-Real-IP       $remote_addr;
    proxy_set_header                X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header                Cookie $http_cookie;
    proxy_pass_request_headers      on;
}
```

但是工作量太大了，对吧？

我希望你对 CORS 和 nginx 有了更多的了解。起初，这可能看起来有点麻烦，但是你只需要在你的机器上安装一次 nginx，你就再也不用搜索特定语言的 CORS 补丁了。如果你做得足够好，你甚至可以在生产中绕过任何 CORS 问题，而且你现在已经对 nginx 了解很多了😉

我其他的一些文章

[https://level up . git connected . com/why-every-software-engineer-should-use-vim-b9fb 97 e 69d 97](/why-every-software-engineer-should-use-vim-b9fb97e69d97)

[https://level up . git connected . com/beginner-guide-to-started-with-vim-not-blow-your-head-off-30313088 a9f 3](/beginner-guide-to-started-with-vim-without-blowing-your-head-off-30313088a9f3)