# 用熊猫生成动态网站地图🐼

> 原文：<https://levelup.gitconnected.com/dynamic-sitemap-generation-with-pandas-c78899600796>

## 确保你的网站是完全优化的——生成一个网站地图！

![](img/116c3cafd89190f0431017cff7b27974.png)

照片由 [Pexels](https://www.pexels.com/photo/australia-map-68704/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Catarina Sousa](https://www.pexels.com/@catarina-sousa-9147?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

想象你在一栋建筑里，每个房间都有不同种类的物品展示给你的访客。你如何让来访者知道该参观哪个房间？是的，你需要一张地图。这也适用于你的网站，为了让搜索引擎如 Google 和 Bing 知道你网站的内容，你需要一个好的 XML 站点地图。

XML 站点地图是一个文件，作为你的网站的路线图，引导谷歌到你所有重要的页面。[1]因此，作为一个数据驱动的营销人员，它是一个重要的搜索引擎优化(SEO)工具，允许您将您的网站页面列入主要的搜索引擎。

## 网站地图的基本结构

根据 Google [2]的说法，一个非常基本的包含单个 URL 位置的 XML 站点地图如下:

```
<?xml version="1.0" encoding="UTF-8"?>
<urlset >
  <url>
    <loc>http://www.example.com/foo.html</loc>
    <lastmod>2018-06-04</lastmod>
  </url>
</urlset>
```

站点地图协议格式由 XML 标记组成。站点地图中的所有数据值必须是[实体转义的](https://www.sitemaps.org/protocol.html#escaping)。文件本身必须是 UTF-8 编码的。

网站地图必须:

*   以开始标签`<[urlset](https://www.sitemaps.org/protocol.html#urlsetdef)>`开始，以结束标签`</urlset>`结束。
*   在`<urlset>`标签中指定名称空间(协议标准)。
*   为每个 URL 包含一个`<[url](https://www.sitemaps.org/protocol.html#urldef)>`条目，作为父 XML 标记。
*   为每个`<url>`父标签包含一个`<[loc](https://www.sitemaps.org/protocol.html#locdef)>`子条目。

所有其他标签都是可选的。对这些可选标签的支持可能因搜索引擎而异。此外，网站地图中的所有 URL 必须来自一个主机，比如 www.example.com 或 store.example.com。[3]

## 静态网站地图的问题

最近我一直在做我的一个项目——ai pharm。XYZ 。它显示每日更新的制药数据。数据不断更新，每天都会添加新页面。如果没有新的网站地图，谷歌需要几个月的时间来发现新页面。解决问题的方法是——动态网站地图。

## 为什么是熊猫🐼？

静态网站地图的问题引导我寻找动态生成网站地图的解决方案，我找到了最简单的解决方案:**熊猫！**

有了最新版本( *Pandas 1.4 及以上*)，可以很容易地从数据框中生成网站地图。使用熊猫的其他优势包括:

*   轻松操作页面
*   Python 友好的
*   需要几行代码

让我们现在开始编码💪

在下一节中，我将演示如何从类似于被查询的项目数据的虚拟数据中生成一个站点地图。你可以在这里获得 JSON 和这篇文章的最终代码[在这里](https://github.com/manfye/medium-publication/tree/main/Dynamic%20Sitemap%20Generation)。

## 需要的包:

默认情况下，大多数 python 用户已经在他们的环境中安装了 pandas。在开始编码之前检查熊猫的版本是很重要的。

```
pd.__version__
```

> *确保你的熊猫版本高于 1.4.0*

如果低于 1.4.0，请升级您的熊猫:

```
pip install --upgrade pandas==1.4.1
```

导入必要的包并开始编码:

```
import pandas as pd
import numpy as np
import requests
from datetime import datetime
import urllib.parse
import renow = datetime.now()
```

接下来，我们需要获得动态项目的完整列表，并将其加载到数据框中，请注意，代码可能会根据您的后端数据而有所不同:

```
r = requests.get("[https://reqres.in/api/products](https://reqres.in/api/products)")
j = r.json()["data"]
df = pd.DataFrame.from_dict(j)
```

## 生成动态项目列表

在这里，我使用 URL[https://example.xyz/](https://aipharm.xyz/)作为我的 URL 前缀。我想做一个[https://example.xyz/](https://aipharm.xyz/)产品/ < id > / <产品名称>的图案

由于产品每月更新一次，所以我将优先级设为 0.6，并将 fred 设为每月

```
**def** returnURL(name,id,type):
    pattern **=** re**.**compile(r"[^\w\s]")
    url_name **=** pattern**.**sub("", name)
*#     print(url_name)*
    url_name **=** url_name**.**lower()**.**replace(" ","-")
    url **=** "https://example.xyz/"**+**type**+**"/"**+**str(id)**+**"/"**+**urllib**.**parse**.**quote(url_name)
    **return** url

df["loc"] **=** df**.**apply(**lambda** x: returnURL(x["name"],x["id"],"products"),axis**=**1)
df["lastmod"] **=** now**.**strftime("%Y-%m-%d")
df["changefreq"] **=** "monthly"
df["priority"] **=** 0.6df **=** df**.**reindex(columns**=**["loc","lastmod","changefreq","priority"])
```

创建表格后，记得重新索引表格，只获取站点地图所需的 4 列。

## 生成静态页面

创建一个包含 4 列“loc”、“lastmod”、“changefreq”、“priority”的数据框，然后将所有数据追加到数据框中

```
df_main **=** pd**.**DataFrame(columns**=**["loc","lastmod","changefreq","priority"], data**=**[])df_main **=** df_main**.**append(pd**.**DataFrame(columns**=**["loc","lastmod","changefreq","priority"], data**=**[["https://example.xyz",now**.**strftime("%Y-%m-%d"),"daily",1.0]]))

array_list **=** ["page1","page2","page3"]
**for** i **in** array_list:
    df_main **=** df_main**.**append(pd**.**DataFrame(columns**=**["loc","lastmod","changefreq","priority"], data**=**[["https://example.xyz/"**+**i,now**.**strftime("%Y-%m-%d"),"daily",1.0]]))
```

## 合并两个列表

一旦你有了这两个列表，将它们合并成为最终的表 df_final。记得删除索引以适应所需的格式。

```
df_final **=** df_main**.**append(df)
df_final **=** df_final**.**reset_index(drop**=True**)
```

## 出版🎉

整篇文章的精髓就在这里！只需使用 dataframe.to_xml 方法将数据框导出为站点地图。使用下面的设置来遵守站点地图协议:

```
df_final.to_xml("sitemap.xml" ,
                index=False,
                root_name='urlset',
                row_name='url',
                namespaces= {"": "[http://www.sitemaps.org/schemas/sitemap/0.9](http://www.sitemaps.org/schemas/sitemap/0.9)"})
```

*注意:确保在第一次生成站点地图时，使用站点地图验证器* [*在线*](https://www.xml-sitemaps.com/validate-xml-sitemap.html) 来验证您的站点地图

## 奖励:Github 上传

要上传到 GitHub，您可以使用 pygithub 包，只需使用下面的代码:

```
from github import Github# using an access token
g = Github("XXXXXXXX")
repo = g.get_repo("xxxx/medium_article")
with open('sitemap.xml', 'r') as file:
    content = file.read()

contents = repo.get_contents("public/sitemap.xml")
repo.update_file("public/sitemap.xml", "update sitemap", content, contents.sha, branch="main")
```

这段代码将把网站地图上传到你想要的 Github repo 中，新的网站地图将在网站重建时提供给 google。

> 完整代码[此处](https://github.com/manfye/medium-publication/tree/main/Dynamic%20Sitemap%20Generation)

# *最终想法*

作为一名 react 前端开发人员和数据爱好者，我发现使用 pandas 来生成站点地图更简单。首先，它让我有一个独立的管道来生成站点地图，我能够控制何时需要生成站点地图，我不需要故意推送一些不必要的东西，我能够设置 cron 任务来生成站点地图。

其次，与其他在线可用的方法相比，这种方法更容易设置，例如在构建时生成的 javascript 方法，或者其他需要多个步骤的 python 方法。它让我可以完全控制站点地图中要生成和包含的内容，并且能够在将来生成更复杂的站点地图。

最后，我想感谢你阅读我的文章

如果你对数据科学感兴趣并有所反应，你可以阅读我下面的另一篇文章:

[](https://towardsdatascience.com/custom-object-detection-using-react-with-tensorflow-js-7f79adee9cd4) [## 使用 React 和 Tensorflow.js 进行自定义对象检测

### 让我们用 Azure 自定义视觉在 30 分钟内制作一个实时自定义对象检测器

towardsdatascience.com](https://towardsdatascience.com/custom-object-detection-using-react-with-tensorflow-js-7f79adee9cd4) 

如果你想成为中等会员，请考虑通过这个[链接](https://manfyegoh.medium.com/membership)支持我💌💖

## 参考资料:

[1] Yoast SEO

[2][https://developers . Google . com/search/docs/advanced/sitemaps/build-sitemap](https://developers.google.com/search/docs/advanced/sitemaps/build-sitemap)

[https://www.sitemaps.org/protocol.html](https://www.sitemaps.org/protocol.html)