# Nginx 的自动维护模式

> 原文：<https://levelup.gitconnected.com/auto-maintenance-mode-with-nginx-71ef5c896851>

![](img/b9c96283560c9c7c9a0bda91f5a1ea72.png)

当您部署新功能或进行一些维护工作时，或者在停机期间，在您的站点或 web 应用程序中显示维护页面将确保站点的访问者(尤其是非技术用户)确实停机进行适当的维护。显示服务器提供的随机或默认错误页面并不是一个好主意。

你可以根据你网站的主题创建一个简单的`maintenance.html`页面。以下是 Nginx 的配置:

在上面的配置中，当服务器在`root`位置找不到`index.html`页面时会显示`maintenance.html`页面。

例如，当您构建一个`React` web 应用程序时，它也会删除构建文件夹的内容，包括`index.html`。这就是维护页面位于不同路径的原因。在构建过程完成之前，`maintenance.html`将提供给用户。

如果你需要让你的站点离线，那么你可以简单地将`/`的`root`位置改为维护页面位置。保存配置文件后，确保重启`nginx`。