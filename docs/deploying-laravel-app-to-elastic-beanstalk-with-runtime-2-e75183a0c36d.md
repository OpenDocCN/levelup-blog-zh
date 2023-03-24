# 使用 Runtime 2 将 Laravel 应用程序部署到 Elastic Beanstalk

> 原文：<https://levelup.gitconnected.com/deploying-laravel-app-to-elastic-beanstalk-with-runtime-2-e75183a0c36d>

![](img/90f6c9b0200c95c2090249f587ff7117.png)

使用运行时 2 (PHP 7.4)将 Laravel 部署到 Elastic Beanstalk

如果您最近试图使用 nginx + PHP 7.4 将 Laravel 应用程序部署到 elastic beanstalk，那么您必须知道，每当您试图访问您的任何应用程序路由时，它都会抛出 404 错误。

![](img/a4ce77403d37555c367ad8006a3b3c7e.png)

发生这种情况是因为最新的 elastic beanstalk 运行时使用 nginx 作为缺省的 web 服务器来代替 Apache，Apache 是在最老的版本中使用的。

许多用户只是决定放弃 php 7.4，回到 php 7.2，旧的运行时和问题得到解决。你不需要这么做。

# 我该如何解决这个问题？

您所要做的就是创建这个文件

```
.platform/nginx/conf.d/elasticbeanstalk/
```

并在其中粘贴常规的 try_files 指令。

```
location / {
    try_files $uri $uri/ /index.php?$query_string;
    gzip_static on;
}
```

然后使用常规的 **eb deploy** 或使用 zip 文件提交/部署它。这将自动使您的路线再次正常工作。

当然，您可以在这个文件中添加任何其他指令。只是要小心弹性豆茎支撑的是什么。