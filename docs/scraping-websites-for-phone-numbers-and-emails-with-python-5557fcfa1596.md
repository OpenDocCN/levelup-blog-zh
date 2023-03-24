# 用 Python 抓取网站上的电话号码和电子邮件

> 原文：<https://levelup.gitconnected.com/scraping-websites-for-phone-numbers-and-emails-with-python-5557fcfa1596>

## 数据收集

## [轻量级工具](https://github.com/enjoys-sashimi/contact-scraper)用于抓取、验证电子邮件和电话号码。

![](img/47642d6b4e20b141c1b9408137b1d45d.png)

照片由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

让我们直接开始吧。

首先确定你有 Python 3，如果没有，[安装 Python 3](https://www.python.org/downloads/) 。

与所有 python 项目一样，最好在虚拟环境中运行它们。如果您已经知道如何设置虚拟环境，请随意跳过前面的步骤。

1.  导航到您希望此项目驻留的父文件夹。

> c:\项目

2.打开 cmd 提示符并执行此脚本来创建一个新的虚拟环境。

```
py -m venv contact-scraper-venv
```

*   ***注意:*** 如果您在 Linux 或 MacOS 上，该命令会有所不同，请参见关于[创建虚拟环境](https://docs.python.org/3/library/venv.html)的文档。

3.如果您遵循我的命名约定，请导航到脚本目录，该目录位于:

```
C:\Projects\contact-scraper-venv\Scripts
```

4.一旦进入正确的目录，安装[接触刮刀](https://github.com/enjoys-sashimi/contact-scraper)。回购上有安装说明，但我会在这里添加。逐一执行以下命令。

```
git clone git@github.com:enjoys-sashimi/contact-scraper.git
cd contact-scraper
pip install -r requirements.txt
```

恭喜你。您已经设置好了，现在让我们修改当前目录中的 example.py 来进行抓取。

本节将抓取一个域列表，并将结果 JSON 对象写入 output.json。

```
from contactscraper.controller import Controller

instance = Controller(starting_urls=['https://www.python.org/'], 
                       scrape_numbers=True,
                       scrape_emails=True,
                       region="US",
                       max_results=2)

instance.scrape()
```

控制器接受几个参数，让我们看看它们各自的功能。

*   starting_urls:是你想开始递归抓取的 URL 列表。蜘蛛将被部署在每个网址上，它不会偏离到任何不包含根网址的链接。例如，传入`['https://www.python.org/privacy/']`将允许抓取任何根域为`python.org`的 URL。
*   scrape_numbers & scrape_email:布尔型，分别描述您是否想要抓取电子邮件或电话号码。
*   地区:用于验证电话号码的地区，大多数北美号码使用美国地区，但是，我们支持[所有这些两位数字的地区代码](https://github.com/daviddrysdale/python-phonenumbers/tree/dev/python/phonenumbers/shortdata)。
*   max_results:一个整数，表示在关闭之前应该存储的唯一结果的最大数量。唯一结果的特征在于包含一个或多个电子邮件或一个或多个电话号码的唯一 URL。

完成修改后，继续运行 example.py:

```
C:\Projects\contact-scraper-venv\Scripts> example.py
```

很好，现在您已经找到了一个联系信息网站，让我们打印出存储在`output.json`中的结果:

```
import json

with open('output.json', 'r') as raw_output:
    data = raw_output.read()
    output = json.loads(data)

print(json.dumps(output, indent=2))
```

如果您按照本指南进行测试，您应该会看到以下结果:

```
[
  {
    "url": "https://www.python.org/privacy/",
    "emails": [
      "psf@python.org"
    ],
    "numbers": []
  },
  {
    "url": "https://status.python.org/",
    "emails": [],
    "numbers": [
      "+16505551234"
    ]
  }
 ]
```

如果你想投稿、提出改进建议或报告错误，你可以在 [github repo](https://github.com/enjoys-sashimi/contact-scraper) 上这样做。