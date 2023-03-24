# 高性能 URL 路由—使用 PHP 和正则表达式(regex)

> 原文：<https://levelup.gitconnected.com/performant-url-routing-with-php-and-regular-expressions-regex-866480493412>

## 用 PHP 和 regex 实现高性能 URL 路由的简单教程。需要 PHP，htaccess 和 regex 的基础知识。

![](img/26224c865472cdb4c7cf5ca9295556c9.png)

开头简单解释了一下:url 路由器是做什么的？url 路由器评估请求的 URL，并根据定义的规范对其进行解释。我使用这个路由器通过 URL 加载一个特定的控制器。一个例子:

```
[http://www.website.com/user/edit/4](http://www.website.de/user/edit/4)
```

这个 url 应该用动作*【编辑】*和参数*【4】*调用控制器*【用户】*。

```
include 'ctrl/user-edit.ctrl.php';
```

另一个例子:

```
http://www.website.com/company/search/autocomplete/german
```

这个 url 应该调用控制器“company ”,动作为“search-autocomplete ”,过滤器为“german”。

```
include 'ctrl/company-search-autocomplete.ctrl.php';
```

在这两个例子中，我仍然有像用户 id 或过滤器这样的参数。这些应该能够传递给 php 脚本，而不是控制器名称的一部分，控制器名称应该来自 url。

# 步骤 htaccess 文件

为了让*“http://www . website . com/”*之后的部分被 PHP 读取，你需要一个 htaccess 文件，向 web 服务器发送重写指令。在这个例子中，这是由 ModRewrite 完成的，这是大多数 Apache 和 nginx 服务器的标准。

```
# ModRewrite activate
RewriteEngine On

# Let files and folder through
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d

# rewrite URL
RewriteRule (.+) index.php?path=$0 [L,QSA]
```

最后一行说的是*“http://www . domain . com/”*之后的部分是如何传递给 PHP 脚本的:作为 get 参数“path”。

# 第二步:路线

为了使路由工作，脚本需要关于存在哪些路由的信息。在这个例子中，我定义了三条路由，并将所有内容放在一个文件*“index . PHP”*中。对于较大的项目，您也可以将路由放在配置文件中。

这些路线是一个三重嵌套的数组。*“path _ pattern”*值是一个正则表达式，表示通常应用于 url 的模式公式。本例中的值*“控制器”*是一个控制器文件的路径。

# 步骤 3:路由器功能

我现在将展示完整的路由器功能，然后一步一步地解释每个代码块的作用。

# 该功能

```
function router($routes)
{
}
```

我把这个函数命名为*“路由器”*。参数是先前创建的数组“routes”。

# 基本变量

```
function router($routes)
{
  $route_match = false;
  $url_path    = 'index';
  $url_params  = array();
}
```

*“route _ match”*是一个布尔变量，目前设置为 false。因此，在执行该函数之前，还没有匹配的路由。最初，变量*“URL _ path”*被设置为值*“index”*。为了存储可能的 URL 参数，我初始化了数组 *"url_params"* 。

# 获取参数路径

```
function router($routes)
{
  $route_match = false;
  $url_path    = 'index';
  $url_params  = array();

  if(isset($_GET['path']))
  {
    $url_path = $_GET['path'];
    if(substr($url_path,-1) == '/')
    {
      $url_path = substr($url_path,0,-1);
    }
  }
}
```

在这里，我检查 GET 变量是否存在。如果是这样，我用 GET 参数*“path”*中的值覆盖*“URL _ path”*。

if 条件 *"if(substr($url_path，-1) == '/')"* 截断字符串 *"url_path"* 的最后一个字符，如果是*"/*。

# 用正则表达式检查路线

```
function router($routes)
{
  $route_match = false;
  $url_path    = 'index';
  $url_params  = array();

  if(isset($_GET['path']))
  {
    $url_path = $_GET['path'];
    if(substr($url_path,-1) == '/')
    {
      $url_path = substr($url_path,0,-1);
    }
  }

  foreach($routes as $route)
  {
    if(preg_match($route['path_pattern'],$url_path,$matches))
    {
      $url_params  = array_merge($url_params,$matches);
      $route_match = true;
      break;
    }
  }
}
```

在 foreach 循环中，我遍历了变量*“routes”*。if-condition*“if(preg _ match(…))”*检查当前循环元素的模式是否与变量*“URL _ path”*匹配，并立即传递带有变量*“matches”*的匹配变量。然后，变量*“url _ params”*包含 URL 中包含的参数。

将变量*“route _ match”*设置为*“true”*，并为 foreach 循环给出一个*“break”*。

# 变量的评估

```
function router($routes)
{
  $route_match = false;
  $url_path    = 'index';
  $url_params  = array();

  if(isset($_GET['path']))
  {
    $url_path = $_GET['path'];
    if(substr($url_path,-1) == '/')
    {
      $url_path = substr($url_path,0,-1);
    }
  }

  foreach($routes as $route)
  {
    if(preg_match($route['path_pattern'],$url_path,$matches))
    {
      $url_params  = array_merge($url_params,$matches);
      $route_match = true;
      break;
    }
  }

  if(!$route_match)
  {
    exit('URL path "'.$url_path.'" is not defined.');
  }

  if(file_exists($route['controller']))
  {
    include($route['controller']);
  }
  else
  {
    exit('Controller "'.$route['controller'].'" does not exists.');
  }
}
```

然后，我检查*“route _ match”*是否仍为*“false”*，并显示错误消息“未找到路线”退出。如果找到了路由，我会检查路由器规范中的控制器文件是否存在，并包含它。

# 完整的剧本

用一个简单的*“路由器(＄routes)；”*路由器现在可以调用了。

在 URL*" http://www . website . com/user/edit/4 "*处，加载了控制器*" ctrl/user-edit . ctrl . PHP "*，在关联数组 *"url_params"* 中，现在有了条目 *"user_id"* = > 4，include 可以访问该条目。

*“用户标识”*的值来自模式“/^user\/edit\/(？P\d+)$/。

在我的例子中，我已经展示了基本思想。使用这种方法，您可以创建一个非常灵活的路由器。可以通过各种信息扩展路由阵列，并且可以自由配置从 url 获得的信息的完整处理。

我已经用这个系统实现了几个项目，我对性能非常满意。你觉得剧本和方法怎么样？我期待你的反馈！