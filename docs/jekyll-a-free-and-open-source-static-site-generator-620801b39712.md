# Jekyll——一个免费的开源静态站点生成器

> 原文：<https://levelup.gitconnected.com/jekyll-a-free-and-open-source-static-site-generator-620801b39712>

## Jekyll 是一个简单且可扩展的静态站点生成器。它可以用来建立具有丰富且易于使用的导航的网站。

![](img/5e0ea3e37b767f57d6afd57face2d278.png)

Jekyll 是 Github 用 Ruby 语言构建的，你可以免费使用 Github 页面来托管你的静态网站，并使用 repo 中的 CNAME 文件轻松地将其与你的自定义域名链接起来。

# 装置

使用 Jekyll，您可以通过几个命令在几秒钟内启动并运行 Jekyll。一旦有了安装了 Ruby 和 gem 的开发机器，就可以运行下面的命令:

```
gem install jekyll bundler
```

这将使用 gem 安装 jekyll 和 bundler 包。

```
jekyll new new-site
```

生成的网站使用了一个叫做 Minima 的极简主题，这对作者来说是一个很好的主题。

```
cd new-site
```

然后，您可以在网站文件夹中导航。

```
jekyll serve
```

接下来，您可以简单地导航到 localhost:4000 来查看静态网站的启动和运行。

```
[http://localhost:4000](http://localhost:4000)
```

# 哲基尔的利弊

## 赞成的意见

*   它是免费和开源的
*   您可以将主题构建为宝石，并通过 RubyGems 分发它们
*   简单易用
*   多亏了 Jekyll importers，你可以很容易地从流行的平台(如 WordPress)上迁移你的内容
*   强大的 Github 页面支持
*   带有默认和体面的最小化的开箱即用的主题

## 骗局

*   随着网站内容的增长，构建过程会明显变慢(这是 Jekyll 的主要弱点)
*   Gem 依赖可能会引入不兼容性
*   许多插件变得过时
*   增量构建仍然是实验性的
*   Github Pages 支持 Jekyll 开箱即用，但只能使用一组 Github 安全插件

```
Jekyll is by far the most popular static generator since it was built and supported by GitHub and used for the popular service GitHub Pages which are used for free to host static pages for personal or project websites.
```