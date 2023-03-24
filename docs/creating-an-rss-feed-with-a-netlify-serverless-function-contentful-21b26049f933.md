# 用 Netlify 无服务器函数+ Contentful 创建 RSS 提要

> 原文：<https://levelup.gitconnected.com/creating-an-rss-feed-with-a-netlify-serverless-function-contentful-21b26049f933>

![](img/f0f4493a58cf1a2cdffa0f817719b055.png)

打造你的天线。

早在 2020 年 10 月，Brightcove [推出了我们的新品牌](https://www.brightcove.com/en/blog/brightcove-video-means-business)——同时推出了新的[www.brightcove.com](https://www.brightcove.com/en)——采用 [Contentful](https://contentful.com) 和 [Netlify](https://www.netlify.com) 的盖茨比风格。一月份，我们把我们的英文博客搬了过来，所以 inso 不得不寻找一个 feed 视图的替代品(最初是通过 Node/sail js/Pug 提供的)。谢天谢地，这是 Netlify 的函数和 Contentful 的 JavaScript SDK 的轻而易举的事情。

RSS 提要本质上是一个很长的 XML 文件，包含读者获取的内容。这个函数只需要做三件事:

1.  获取内容
2.  汇编和格式化提要
3.  返回格式化的提要

首先，在部署到 Netlify 的 Gatsby 项目中，创建一个/functions/文件夹(如果还没有的话)。在该文件夹中，创建一个名为 rss-feed.js 的文件。

在 rss-feed.js 中，首先导入内容丰富的 javascript 内容交付 API，并使用您的凭证配置客户端:

```
const contentful = require(“contentful”);
const client = contentful.createClient({
  space: "SPACE_ID",
  accessToken: process.env.GATSBY_CONTENTFUL_ACCESS_TOKEN
});
```

您可以使用与您的应用程序的其余部分相同的 env 变量来访问 Contentful—rad。

接下来，我们可以设置实际响应请求的处理程序。

```
exports.handler = async (event, context) => {
  // get posts from contentful and do stuff
  return {
    statusCode: 200,
    body: "MY FEED WILL GO HERE"
  };
};
```

Netlify 函数需要返回一个状态和主体。在我们的例子中，主体将是带有大量数据的格式化的 XML。但是首先，让我们添加对 Contentful 的调用。

```
const posts = await client.getEntries({
    content_type: "post",
    locale: "en-US",
    order: "-fields.publishedAt"
 });
```

这是对上面配置的内容丰富的客户端的异步调用。我们的内容类型被定义为“post ”,我们正在寻找英文的帖子，所以我们将通过“en-US”地区。为了确保他们首先返回最新的帖子，我们添加了“-fields.publishedAt”的订单查询(您可以根据自己的内容模型使用任何想要的参数)。

既然我们得到了帖子，是时候构建提要了。下面的两个方法可以做到这一点:为整个提要设置开始和结束标记，以及每个条目的特定于项目的格式。感谢模板文字让这个*超级*变得简单。

```
const sanitize = (string) => {
  const newString = string.replace(/\&/g, "&amp;");
  return newString;
};
const dateString = (dstring) => {
  const target = new Date(dstring);
  return target.toUTCString();
};const feedWrapper = (contents) => {
  return `<?xml version="1.0" encoding="utf-8" ?>
    <rss version="2.0" xml:base="[https://site.com/en/blog/feed](https://site.com/en/blog/feed)" xmlns:dc="[http://purl.org/dc/elements/1.1/](http://purl.org/dc/elements/1.1/)">
    <channel>
        <title>My Blog - The Leading Blog</title>
        <link>[https://site.com/en/blog](https://site.com/en/blog)</link>
        <description>Superior Posts</description>
        <language>en</language>
        ${contents}
    </channel>
    </rss>
    `;
};const stringify = (obj) => {
  const template = (entry, id) => `
        <item>
            <title>${sanitize(entry.title) || ""}</title>
            <link>[https://site.com/en/blog/${entry.slug](https://site.com/en/blog/${entry.slug) || ""}</link>
            <category>[https://site.com/en/blog/${](https://www.brightcove.com/en/blog/${)
              entry.category.fields.slug
            }</category>
            <pubDate>${dateString(entry.publishedAt)}</pubDate>
            <description>${sanitize(entry.body)}</description>
            <dc:creator>${entry.author.fields.name}</dc:creator>
            <guid>[https://site.com/en/blog/${entry.slug](https://site.com/en/blog/${entry.slug)}</guid>
        </item>
    `;
  if (obj && obj.fields && Object.keys(obj.fields).length > 0) {
    return template(obj.fields, obj.sys.id);
  } else {
    return "";
  }
};
```

好了，让我们来谈谈其中的一些。sanitize 和 dateString 方法只是清理从 Contentful 返回的内容；不好的 HTML 字符(特别是“&”)会让 feed 读者生气，所以 santize 替换了它们。为了将一个以毫秒为单位的时间戳转换成清晰的日期/时间，dateString 方法返回一个 UTC 字符串，这也是为了使<pubdate>值有效。</pubdate>

feedWrapper 方法包含开始和结束 feed 标签，并接受一个*内容*参数——该参数将是由 *stringify* 方法生成的条目列表。让我们建立这个列表:

```
const buildFeed = (posts) => {
  let entries = "";
  posts.items.forEach((item, index) => {
    if (item.fields && Object.keys(item.fields).length > 0) {
      entries += stringify(item);
    }
  });
  return feedWrapper(entries);
};
```

我们已经设置了一个空的*条目*字符串，它被追加到我们从 contentful 响应传递的方法的 post 项的 forEach 循环中。我们检查 post 对象是否有包含我们需要的内容的字段，并为每个字段生成一个模板字符串。然后，该方法移交已构建的<项目列表> …..< /item >字符串关闭到 feedWrapper 方法，并返回编译后的 XML 提要。

综合起来，它看起来像这样:

```
const contentful = require("contentful");
const client = contentful.createClient({
  space: "SPACE_ID",
  accessToken: process.env.GATSBY_CONTENTFUL_ACCESS_TOKEN
});const feedWrapper = (contents) => {
  return `<?xml version="1.0" encoding="utf-8" ?>
    <rss version="2.0" xml:base="[https://site.com/en/blog/feed](https://site.com/en/blog/feed)" xmlns:dc="[http://purl.org/dc/elements/1.1/](http://purl.org/dc/elements/1.1/)">
    <channel>
        <title>My Blog - The Leading Blog</title>
        <link>[https://site.com/en/blog](https://site.com/en/blog)</link>
        <description>Superior Posts</description>
        <language>en</language>
        ${contents}
    </channel>
    </rss>
    `;
};
const sanitize = (string) => {
  const newString = string.replace(/\&/g, "&amp;");
  return newString;
};
const dateString = (dstring) => {
  const target = new Date(dstring);
  return target.toUTCString();
};
const stringify = (obj) => {
  const template = (entry, id) => `
        <item>
            <title>${sanitize(entry.title) || ""}</title>
            <link>[https://site.com/en/blog/${entry.slug](https://site.com/en/blog/${entry.slug) || ""}</link>
            <category>[https://site.com/en/blog/${](https://www.brightcove.com/en/blog/${)
              entry.category.fields.slug
            }</category>
            <pubDate>${dateString(entry.publishedAt)}</pubDate>
            <description>${sanitize(entry.body || "")}</description>
            <dc:creator>${entry.author.fields.name}</dc:creator>
            <guid>[https://site.com/en/blog/${entry.slug](https://site.com/en/blog/${entry.slug)}</guid>
        </item>
    `;
  if (obj && obj.fields && Object.keys(obj.fields).length > 0) {
    return template(obj.fields, obj.sys.id);
  } else {
    return "";
  }
};
const buildFeed = (posts) => {
  let entries = "";
  posts.items.forEach((item, index) => {
    if (item.fields && Object.keys(item.fields).length > 0) {
      entries += stringify(item);
    }
  });
  return feedWrapper(entries);
};exports.handler = async (event, context) => {
  // get posts from contentful
  const posts = await client.getEntries({
    content_type: "post",
    locale: "en-US",
    order: "-fields.publishedAt"
  });
  const formatted = posts ? buildFeed(posts) : "";
  return {
    statusCode: 200,
    body: formatted
  };
};
```

该函数等待 posts，通过遍历条目构建提要，然后将条目粘贴到 XML 包装器中，并将其作为响应体返回。现在，要在您的 netlify 应用程序中创建一个路由，您可以创建一个 Netlify 重定向。在我们的例子中，我们已经大量使用了`/static/_redirects`文件，所以我们只需要添加新的一行:

```
/en/blog/feed/all   /.netlify/functions/rss-feed  200!
```

现在，我们可以将用户指向`en/blog/feed/all`的 URL，它将执行函数来获取帖子并返回 XML:[https://www.brightcove.com/en/blog/feed/all](https://www.brightcove.com/en/blog/feed/all)

这是一个相当快速的端口，我相信还有更多的可以添加到 feed(媒体资产等)，但它让我们的 feed 启动并运行，几乎没有任何问题。我已经删除了一些我们必须用来清理响应数据的额外库，但值得注意的是，如果您从 CMS 获得 Markdown，应该对其进行解析和清理——我发现 [markdown-it](https://www.npmjs.com/package/markdown-it) 和 [string](https://www.npmjs.com/package/string) 有助于将数据规范化为 RSS 解析器更喜欢的内容。

如果我应该在这里添加更多内容，请大声说出！感谢阅读！