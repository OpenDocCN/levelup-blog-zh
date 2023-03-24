# 如何用 Scrapy 抓取网页

> 原文：<https://levelup.gitconnected.com/how-to-crawl-a-web-page-with-scrapy-3b9d12797813>

![](img/5cb5881188cd6c6eb68e27fa78854203.png)

由 [Arian Darvishi](https://unsplash.com/@arianismmm?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Scrapy 是最流行和最强大的 Pythons 刮库之一；它采用一种“包含电池”的方法来进行刮擦，这意味着它处理所有刮擦器都需要的许多常见功能，因此开发人员不必每次都重新发明轮子。它使刮削成为一个快速而有趣的过程。Scrapy 和大多数 Python 包一样，位于 PyPI(也称为 pip)上。PyPI，Python 包索引，是一个社区拥有的所有已发布 Python 软件的存储库。如果您安装了 Python，就像本教程的先决条件中概述的那样，那么您的机器上已经安装了 pip，因此您可以使用以下命令安装 Scrapy:

`pip install scrapy`

# 编码:

我们将创建一个蜘蛛类，并将`scrapy`传递给它，然后我们将为 name 和 start_url 提供两个属性

`name` —蜘蛛的名字

`start_url` —蜘蛛将抓取的 URL 列表，我们将从一个 URL 开始

让我们一行一行地分解代码。首先，我们已经导入了`scrapy`模块，因此我们可以使用它的类和包。接下来，我们获取 Scrapy 提供的 Spider 类，并在它的基础上创建一个子类，名为 BrickSetSpider。把子类想象成它的父类的更专门化的形式。Spider 子类有一些方法和行为，这些方法和行为定义了如何跟踪 URL 并从它找到的页面中提取数据，但是它不知道去哪里寻找或者寻找什么数据。通过对它进行子类化，我们可以给它这些信息。然后我们给这个蜘蛛起名叫`brickset_spider`。

最后，我们提供的目标网站的网址是 2016 年数据[http://brickset.com/sets/year-2016](http://brickset.com/sets/year-2016)，如果你在浏览器中打开这个网址，它将重定向到向你显示许多乐高套装的网页。现在，您可以使用命令提示符来执行代码。但是，`scrapy`有自己的命令行运行界面`scrapy runspider scraper.py`

## 输出:

```
2016-09-22 23:37:45 [scrapy] INFO: Scrapy 1.1.2 started (bot: scrapybot)
2016-09-22 23:37:45 [scrapy] INFO: Overridden settings: {}
2016-09-22 23:37:45 [scrapy] INFO: Enabled extensions:
['scrapy.extensions.logstats.LogStats',
 'scrapy.extensions.telnet.TelnetConsole',
 'scrapy.extensions.corestats.CoreStats']
2016-09-22 23:37:45 [scrapy] INFO: Enabled downloader middlewares:
['scrapy.downloadermiddlewares.httpauth.HttpAuthMiddleware',
 ...
 'scrapy.downloadermiddlewares.stats.DownloaderStats']
2016-09-22 23:37:45 [scrapy] INFO: Enabled spider middlewares:
['scrapy.spidermiddlewares.httperror.HttpErrorMiddleware',
 ...
 'scrapy.spidermiddlewares.depth.DepthMiddleware']
2016-09-22 23:37:45 [scrapy] INFO: Enabled item pipelines:
[]
2016-09-22 23:37:45 [scrapy] INFO: Spider opened
2016-09-22 23:37:45 [scrapy] INFO: Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
2016-09-22 23:37:45 [scrapy] DEBUG: Telnet console listening on 127.0.0.1:6023
2016-09-22 23:37:47 [scrapy] DEBUG: Crawled (200) <GET http://brickset.com/sets/year-2016> (referer: None)
2016-09-22 23:37:47 [scrapy] INFO: Closing spider (finished)
2016-09-22 23:37:47 [scrapy] INFO: Dumping Scrapy stats:
{'downloader/request_bytes': 224,
 'downloader/request_count': 1,
 ...
 'scheduler/enqueued/memory': 1,
 'start_time': datetime.datetime(2016, 9, 23, 6, 37, 45, 995167)}
2016-09-22 23:37:47 [scrapy] INFO: Spider closed (finished)
```

输出太长。让我们来分析一下这个蜘蛛的工作原理。

scraper 初始化并加载了处理从 URL 读取数据所需的附加组件和扩展。

2:是我们给了`start_url`的 URL，抓取了网站的`HTML`代码。

3:接下来，他们将 HTML 代码传递给蜘蛛默认解析器，它什么也不做，因为我们从未为此编写过自己的解析器。

# **提取数据:**

好的，我们刚刚创建了一个非常基本的程序`Scrapy`，它只是提取页面的`HTML`代码，什么也不做。它不做任何爬行或抓取，所以我们将扩展代码并添加抓取功能。一个基本的网页有这样的结构。

1:出现在每个网页上的标题。

2:不同的 HTML 标签和元素以及我们需要搜索和提取的其他内容。

3:然后是集合本身，显示在看起来像表格或有序列表的地方。每组都有相似的格式:

```
<body>
  <section class="setlist">
    <article class='set'>
      <a href="https://images.brickset.com/sets/large/10251-1.jpg?201510121127" 
      class="highslide plain mainimg" onclick="return hs.expand(this)"><img 
      src="https://images.brickset.com/sets/small/10251-1.jpg?201510121127" title="10251-1: 
      Brick Bank" onError="this.src='/assets/images/spacer.png'" /></a>
      <div class="highslide-caption">
        <h1>Brick Bank</h1><div class='tags floatleft'><a href='/sets/10251-1/Brick- 
        Bank'>10251-1</a> <a href='/sets/theme-Creator-Expert'>Creator Expert</a> <a 
        class='subtheme' href='/sets/theme-Creator-Expert/subtheme-Modular- 
        Buildings'>Modular Buildings</a> <a class='year' href='/sets/theme-Creator- 
        Expert/year-2016'>2016</a> </div><div class='floatright'>&copy;2016 LEGO 
        Group</div>
          <div class="pn">
            <a href="#" onclick="return hs.previous(this)" title="Previous (left arrow 
            key)">&#171; Previous</a>
            <a href="#" onclick="return hs.next(this)" title="Next (right arrow key)">Next 
            &#187;</a>
          </div>
      </div>

...

    </article>
  </section>
</body>
```

这是我们将传递给 scraper 并从中提取信息的`HTML`代码的外观。`scrapy`使用选择器抓取数据，选择器是我们用来在网页上寻找一个或多个元素的模式，`Scrapy`支持`CSS selectors`或`XPATH selectors`。在这篇教程文章中，我们将使用 CSS 选择器，因为使用 CSS 选择器比`XPATH`更容易定位元素。如果你看了一下上面的 HTML 代码`<article class=’set’>`，保存每个集合数据的文章在设置了类的文章标签中，所以我们可以简单地将我们的选择器设置为. set。

代码将获取带有集合的类，并在它们上面循环以提取数据，现在让我们编写代码来提取数据。如果你再看一遍源代码，你会注意到乐高套装的每个标题都在`h1 tag`里面。

```
<h1>Brick Bank</h1><div class='tags floatleft'><a href='/sets/10251-1/Brick-Bank'>10251-1</a>
```

你会注意到代码中的两件事，首先我们用`append ::text`来命名我们的选择器。这是一个 CSS 伪选择器，它获取标签内部的文本，而不是标签本身。然后我们在由`brickset.css(NAME_SELECTOR)`返回的对象上调用`extract_first()the` 方法，因为我们只想要匹配选择器的第一个元素。这给了我们一个字符串，而不是一个元素列表，现在保存文件并再次运行 scraper，这一次您将在命令提示符下看到集合名称。

## 输出:

```
...
[scrapy] DEBUG: Scraped from <200 http://brickset.com/sets/year-2016>
{'name': 'Brick Bank'}
[scrapy] DEBUG: Scraped from <200 http://brickset.com/sets/year-2016>
{'name': 'Volkswagen Beetle'}
[scrapy] DEBUG: Scraped from <200 http://brickset.com/sets/year-2016>
{'name': 'Big Ben'}
[scrapy] DEBUG: Scraped from <200 http://brickset.com/sets/year-2016>
{'name': 'Winter Holiday Train'}
...
```

让我们扩展我们的刮刀刮图像，碎片，和微型数字，或一套来的微型数字。再次查看 HTML 代码，您会看到图像在`<a> tag`中。棋子放在`<dd> tag`里，微型人物放在`<dt> tag`里。现在，为了获取数据，我们将使用 Xpath，因为用 CSS 选择器从标签中获取数据非常复杂。

输出:

```
2016-09-22 23:52:37 [scrapy] DEBUG: Scraped from <200 http://brickset.com/sets/year-2016>
{'minifigs': '5', 'pieces': '2380', 'name': 'Brick Bank', 'image': 'http://images.brickset.com/sets/small/10251-1.jpg?201510121127'}
2016-09-22 23:52:37 [scrapy] DEBUG: Scraped from <200 http://brickset.com/sets/year-2016>
{'minifigs': None, 'pieces': '1167', 'name': 'Volkswagen Beetle', 'image': 'http://images.brickset.com/sets/small/10252-1.jpg?201606140214'}
2016-09-22 23:52:37 [scrapy] DEBUG: Scraped from <200 http://brickset.com/sets/year-2016>
{'minifigs': None, 'pieces': '4163', 'name': 'Big Ben', 'image': 'http://images.brickset.com/sets/small/10253-1.jpg?201605190256'}
2016-09-22 23:52:37 [scrapy] DEBUG: Scraped from <200 http://brickset.com/sets/year-2016>
{'minifigs': None, 'pieces': None, 'name': 'Winter Holiday Train', 'image': 'http://images.brickset.com/sets/small/10254-1.jpg?201608110306'}
2016-09-22 23:52:37 [scrapy] DEBUG: Scraped from <200 http://brickset.com/sets/year-2016>
{'minifigs': None, 'pieces': None, 'name': 'XL Creative Brick Box', 'image': '/assets/images/misc/blankbox.gif'}
2016-09-22 23:52:37 [scrapy] DEBUG: Scraped from <200 http://brickset.com/sets/year-2016>
{'minifigs': None, 'pieces': '583', 'name': 'Creative Building Set', 'image': 'http://images.brickset.com/sets/small/10702-1.jpg?201511230710'}
```

嗯，我们定义了一个图像，片段，微型选择器，并把每个的 XPath 存储到其中，在接下来的行中，我们使用 XPath 函数。extract_first 函数从标签中删除我们的数据。最后，我们完成了页面抓取，我们的新挑战是让我们的抓取器将页面移动到网站上的另一个页面。看看下面的源代码。

```
<ul class="pagelength">

  ...

  <li class="next">
    <a href="http://brickset.com/sets/year-2017/page-2">&#8250;</a>
  </li>
  <li class="last">
    <a href="http://brickset.com/sets/year-2016/page-32">&#187;</a>
  </li>
</ul>
```

正如你所看到的，李瑟娥标签有一个名为 next 的类，它有一个包含页面 URL 的标签，我们只需要从那个标签中删除那些 URL。

首先，我们为“下一页”链接定义一个选择器，提取第一个匹配，并检查它是否存在。`scrapy.Request`是我们返回的一个值，表示“嘿，抓取这个页面”，而`callback=self.parse`表示“一旦你从这个页面获得了 HTML，就把它传递回这个方法，这样我们就可以解析它，提取数据，并找到下一个页面。T

他的意思是，一旦我们转到下一页，我们将在那里寻找下一页的链接，在那一页上，我们将寻找下一页的链接，以此类推，直到我们找不到下一页的链接。这是网络抓取的关键部分:找到并跟踪链接。

在这个例子中，它是非常线性的；一个页面有一个到下一个页面的链接，直到我们点击最后一个页面，但是你可以跟随链接到标签，或其他搜索结果，或任何其他你喜欢的 URL。现在，如果你保存你的代码并再次运行蜘蛛程序，你会发现它不仅仅是在遍历第一页集合时停止。它继续在 23 页上检查所有 779 个匹配项！从大的方面来看，这并不是一大块数据，但是现在您知道了自动寻找新页面的过程。

# 结论:

我们学习了如何用 python 创建一个`scrapy`铲运机机器人。我们学习了 scraper 如何工作的基础知识，如何使用 CSS 选择器和 Xpath 选择器，如何从标签中获取每个数据，等等。但是事情并没有就此结束。在刮痧世界里，还有很多东西有待发现。希望这篇文章对你以后有所帮助。请随意分享您对此的回应。