# 抓取亚马逊教程——抓取亚马逊畅销书数据的快速示例

> 原文：<https://levelup.gitconnected.com/scraping-amazon-tutorial-a-quick-example-to-scrape-amazon-bestseller-data-d9e014653b79>

## 轻松获得您最喜欢的 30 本书

![](img/9075bc489ac0193fa98b86faed353932.png)

在 Canva 上设计

阅读是你在生活中学习许多技能的燃料。读最好的书会让你收获最大。

畅销书排行榜是了解某个主题值得阅读和购买的最佳途径之一。

您可能曾经复制和粘贴图书数据，如标题、作者、读者评分和排名。从网上复制数据并粘贴到电子表格中。

复制和粘贴既麻烦又容易出错，而且耗时。

或者你可能需要它来决定买哪本书。这其实是我的案子。

我喜欢读关于我喜欢的主题的最好的书。所以我制作了这个脚本来快速方便地获取 CSV 文件中的最佳书籍，我想与你分享。

这个快速教程将帮助你开始。您将学习如何从 Amazon 畅销书页面抓取数据并将其导出到电子表格中。你可以将同样的策略应用于任何畅销书页面。

根据自己的利益使用代码(许可为 Creative Commons Zero)。

让我们开始吧。

# 要求

你只需要安装两个库:`BeautifulSoup`和`Pandas`。我假设你已经安装了 Python3 和 pip。

如果您还没有，请在您的终端上运行以下命令:

```
**$** pip install beautifulsoup4 pandas
```

然后创建一个新的 Python 脚本，将两者和标准库一起导入:`urllib`:

```
from urllib.request import urlopen, Request
from bs4 import BeautifulSoup
import pandas as pd
```

在这里，您使用:

*   `urlopen`打开页面。
*   `Request`创建一个请求对象与服务器通信。
*   `BeautifulSoup`解析 HTML。
*   `pandas`将数据导出到 CSV 文件。

# 请求页面

您需要请求页面。首先，将 URL 传递给`urlopen`函数:

```
# Page for best sellers in writing (Authorship subcategory)
url = 'https://www.amazon.com/gp/bestsellers/books/11892'
request = Request(url, headers={'User-agent': 'Mozilla/5.0'})
html = urlopen(request)
```

注意:需要使用`User-agent`头来防止 Amazon 阻止您的请求。如果没有通过，就会得到这个错误:`urllib.error.HTTPError: HTTP Error 503: Service Unavailable`。

# 解析 HTML

现在，`html`变量包含了页面的 HTML。`urlopen`不理解 HTML，只是返回。

所以你需要用`BeautifulSoup`解析它。这样做将允许您根据解析的 HTML 的结构抓取数据。

```
soup = BeautifulSoup(html, 'html.parser')
```

接下来，您需要在浏览器中转到页面，查看 HTML 的结构。通过在浏览器中单击 CTRL+SHIFT+C(在 macOS 上为 CMD+SHIFT+C)来检查 HTML。当您单击想要了解其 HTML 结构的感兴趣的项目时，您将在 elements 选项卡中看到 HTML 标记。

# 抓取亚马逊畅销书页面

你想抓住你可以循环的畅销商品列表。

在浏览器中检查一个项目的 HTML 后，您会发现每个项目都有一个带有`id`属性`gridItemRoot`的`div`标签。使用`find_all()`方法获取所有带有该 id 的`div`标签。这就是我们想要循环以获取每本书信息的内容。

```
books = soup.find_all('div', **id**="gridItemRoot")
```

旁注:亚马逊应该有比这更好的方法来构建他们的 HTML。这个`id`属性并不像它应该的那样是唯一的。应该是一个`class`属性。

不管怎样，我们继续吧。

现在，你循环阅读每本书。一次只关注一个项目，从 HTML 结构中获取你想要的信息。

```
**for** book **in** books:
    rank = book.find('span', class_="zg-bdg-text").get_text().replace('#', '')
    **print**(rank)
    title = book.find(
        'div',
        class_="_p13n-zg-list-grid-desktop_truncationStyles_p13n-sc-css-line-clamp-1__1Fn1y"
    ).get_text(strip=True)
    **print**(f"Title: {title}")
```

在这个例子中，您用 HTML 标签`span`和类`zg-bdg-text`获得等级。然后，对等级结果做一点修改(例如`#1`)并替换`#`以省略它。

与获取排名结果类似，您可以获取带有 HTML 标签`div`的标题以及相关的 long 类。

然后你会得到作者和评级信息:

```
**for** book **in** books:
    ...
    author = book.find('div', class_="a-row a-size-small").get_text(strip=True)
    **print**(f"Author: {author}")
    r = book.find('div', class_="a-icon-row").find('a', class_="a-link-normal")
    rating = r.get_text(strip=True).replace(' out of 5 stars', '') **if** r **else** None
```

你只想得到收视率。所以你对结果字符串做了一点修改。

# 导出到 CSV

要将数据导出到 CSV 文件，需要在 for 循环之前有空列表，并将数据追加到循环中的每个列表。

直到创建熊猫数据帧并将其导出为 CSV 文件:

```
pd.DataFrame({
    'Rank': ranks,
    'Title': titles,
    'Author': authors,
    'Rating': ratings
}).to_csv('best_seller.csv', index=False)
```

# 最后的想法

这是一个快速的教程，让你开始收集一般的数据。我们已经看到了如何从 Amazon 畅销书页面获取数据，解析 HTML，并将结果导出到 CSV 文件。

注意:这段代码有一个限制，因为我们使用了仅仅是一个 HTML 解析器的`BeautifulSoup`。你可能会注意到，该页面包含 50 本畅销书，而我们只有 30 本。

那是因为那个页面上有 Javascript 代码。它会禁用 30 本书后页面上的其余内容，直到我们向下滚动。

如果你想得到 50 本书的完整列表，你需要使用另一个图书馆。考虑使用硒或羊瘙痒。

但是这是一个快速入门教程。30 本书是个好数字。

**想用 Python 写出干净的代码？** [**这里下载清洁工 Python**](https://ezzeddin.gumroad.com/l/cleaner-python)**。它提供了 5 种方法来编写高效的 Python 代码。**