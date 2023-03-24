# 与 Nginx 共享 Rails 资产的三种方法

> 原文：<https://levelup.gitconnected.com/three-methods-to-share-rails-assets-with-nginx-f39c90bb7d68>

## 通过使用反向代理(如 Nginx)来服务资产，提高 Rails 应用程序的性能

![](img/911b9d624992d9996f88fc4e4d93caf3.png)

伊莱恩·卡萨普在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

性能是每个 web 应用程序的关键问题。虽然应用程序的性能可以通过优化 web 应用程序本身的算法和过程来大幅提高，但也可以通过优化托管堆栈中的其他层来提高，如后期持久性或前端层。

影响 web 应用程序速度和性能的一个方面是实现的内容交付策略。举例来说，web 应用程序内容可以分为两类:

*   静态内容:该内容按原样提供，不需要应用程序进行任何处理。这种内容的例子有样式表、图像和 javascript 脚本。
*   动态内容:在将响应发送给客户机之前，需要计算或处理这些内容。涉及查询数据库以收集数据并将其显示给最终用户的这种内容请求的例子。

由于静态内容不需要由应用程序处理，所以它也可以由其他应用程序提供，例如反向代理应用程序。将静态内容服务从应用程序本身转移到反向代理的主要优点是

*   如果使用反向代理，提供静态内容会更快。首先，不需要向应用程序发送另一个请求，其次，反向代理在提供静态内容方面要快得多，并且提供了更多的技术来增强提供内容的性能，例如压缩内容。
*   由于不是每个请求都会被转发到 web 应用程序，应用程序的负载将会减少，因此应用程序将能够处理更多的并发请求而不会崩溃。

在这篇文章中，我将介绍三种方法，通过 Nginx 反向代理共享 Docker 容器中托管的 Rails 应用程序的静态内容。

## **第一种方法:在本地预编译资产**

这种方法背后的思想是在本地(在执行部署命令的主机上)预编译 Rails 应用程序的资产，然后将资产复制到 Nginx 和 Rails 应用程序的 Docker 映像中。使用这种方法部署应用程序将包括以下步骤

*   查看该项目的源代码
*   预编译 Rails 应用程序。这一步将在 ***公共*** 文件夹中生成资产文件。
*   为 Nginx 和应用程序构建 docker 映像。
*   执行应用程序的部署。

下面的代码片段展示了 docker-compose 如何实现上述步骤。

您可以简单地通过克隆 GitHub repo 后面的[并执行下面的命令来测试上述方法。](https://github.com/wshihadeh/three_methods_to_share_assets)

```
$> git clone [git@github.com](mailto:git@github.com):wshihadeh/three_methods_to_share_assets.git
$> cd precompile_on_the_host
$> make config # to do the local Precompile
$> make deploy # to depoy the services
$> make stop # to stop the services
```

您还需要将下面一行添加到您的`/etc/hosts`文件中，以便能够在`http://blog.local.me`访问站点。

```
127.0.0.1 blog.local.me
```

## **第二种方法:在运行时预编译资产**

这种方法会将 Rails 应用程序的资产预编译延迟到应用程序的运行时。以下几点总结了实施该方法所需的步骤

*   更新 Rails 应用程序的 docket 入口点，以便在启动 Rails 服务器之前预编译资产。
*   构建 Docker 图像。
*   将 Rails 应用程序和 Nginx 配置为对静态资产使用相同的卷。
*   部署服务。

下面的代码片段展示了如何使用 docker-compose 实现上述步骤。一旦应用程序容器启动，它将生成资产并将它们存储在资产卷中，由于 Rails 应用程序和 Nginx 都可以访问同一个卷，因此 Nginx 将可以访问资产并为它们提供服务。

您可以简单地通过克隆 GitHub repo 后面的[并执行下面的命令来测试上述方法。](https://github.com/wshihadeh/three_methods_to_share_assets)

```
$> git clone [git@github.com](mailto:git@github.com):wshihadeh/three_methods_to_share_assets.git
$> cd precompile_at_runtime
$> make deploy # to depoy the services
$> make stop # to stop the services
```

您还需要将下面一行添加到您的`/etc/hosts`文件中，以便能够在`[http://blog.local.me](http://blog.local.me.)` [上访问站点。](http://blog.local.me.)

```
127.0.0.1 blog.local.me
```

## **第三种方法:在构建映像时预编译资产**

该方法将在 docker 构建阶段执行预编译过程，作为在 docker 文件中为 Rails 应用程序定义的步骤之一，而不是在主机上或在运行时在 docker 容器内预编译资产。下面是实现该方法所需的步骤

*   安装 ruby gems 后，更新 Rails 应用程序 Dockerfile 以预编译资产。
*   将 Nginx Docker 文件更新为多级 Docker 文件，从 Rails 应用程序 Docker 映像复制资产文件。
*   部署服务。

下面的代码片段展示了 Nginx 和 Rails 应用程序 docker 图像的一个例子。

您可以简单地通过克隆 GitHub repo 后面的[并执行下面的命令来测试上述方法。](https://github.com/wshihadeh/three_methods_to_share_assets)

```
$> git clone [git@github.com](mailto:git@github.com):wshihadeh/three_methods_to_share_assets.git
$> cd precompile_during_docker_build
$> make deploy # to depoy the services
$> make stop # to stop the services
```

您还需要在`/etc/hosts`文件中添加以下一行，以便能够访问`[http://blog.local.me](http://blog.local.me.)` [上的网站。](http://blog.local.me.)

```
127.0.0.1 blog.local.me
```

**最终想法&其他方案**

以上三种方法描述了如何与逆向共享 Rails 应用程序资产，以及如何配置 Nginx 来服务这些资产。然而，这并不是唯一一个可以提高资产服务绩效的想法。您可以考虑的其他方案简述如下

*   使用 rails 应用程序服务资产，并配置 Nginx 缓存来自 Rails 应用程序的资产。

[](https://medium.com/@wshihadeh/cache-and-serve-rails-static-assets-with-nginx-reverse-proxy-dfcd49319547) [## 使用 Nginx 反向代理缓存和服务 Rails 静态资产

### 通过服务和缓存 web 请求静态内容，提高 Rails web 应用程序的性能和速度

medium.com](https://medium.com/@wshihadeh/cache-and-serve-rails-static-assets-with-nginx-reverse-proxy-dfcd49319547) 

*   使用[内容交付网络](https://en.wikipedia.org/wiki/Content_delivery_network)来服务 Rails 应用程序的资产。关于 CDN 的更多信息可以在[这里](https://guides.rubyonrails.org/asset_pipeline.html)找到。

## **结论**

使用反向代理服务资产或静态内容可以提高 web 应用程序的性能，因为这将减少后端应用程序的负载，并且可以更快地服务静态内容。另一方面，还有其他有助于提高静态资产服务性能的方案，如使用 cdn。