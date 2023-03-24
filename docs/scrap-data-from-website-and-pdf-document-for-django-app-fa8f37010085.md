# 用 Django 应用程序从网站和 PDF 文档中抓取数据

> 原文：<https://levelup.gitconnected.com/scrap-data-from-website-and-pdf-document-for-django-app-fa8f37010085>

我们的 Django web 应用程序现在需要数据——我们将使用 python 从网站和 PDF 文档中获取数据。在本教程中，我们将介绍 BeautifulSoup 的基本 web 抓取和 PyPDF2 的 PDF 抓取。我们将使用这两种技术来获取数据，并将其添加到我们的数据库中，以便 Django 应用程序能够在前端提供数据。

作为一个新的应用程序，当我们上线并部署我们的应用程序时，我们首先需要实时数据。在用户可以开始注册个人资料之前，他们必须看到一个具有读取数据的工作应用程序。真实可靠的数据，提升你的诚信，并吸引人们访问你的网站。

真实的工作数据也会提高你的搜索能力——搜索引擎会有更多的数据，因此你的网站可能会有更多的关键词排名。

我们还将使用这些数据在我们的社交媒体页面上发布，进一步扩大我们的覆盖范围和社区。

![](img/e47951497536d646291a9226b4dd6127.png)

从网站和 PDF 中删除数据

# 从 PDF 文档中删除数据

我们将使用 Python 库 PyPDF2 来废弃 PDF 文档，但是首先我们必须从互联网上下载文件。为此，我们需要一个下载 url。以下是从 PDF 文档中删除数据的步骤:

*   查找下载网址——浏览网站
*   下载文档
*   阅读文件
*   在您需要匹配 Django 模型的数据结构中解析文档
*   将数据保存到数据库

## 关闭网站

我们将使用 BeautifulSoup 并请求 python 中的库来废弃这个网站:[http://www.dpsa.gov.za/dpsa2g/vacancies.asp](http://www.dpsa.gov.za/dpsa2g/vacancies.asp)

获取我们下载 PDF 数据所需的所有文档链接。

```
import requests
from bs4 import BeautifulSoupheaders =  {'User-agent': 'Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:61.0) Gecko/20100101 Firefox/61.0'}
url = "[http://www.dpsa.gov.za/dpsa2g/vacancies.asp](http://www.dpsa.gov.za/dpsa2g/vacancies.asp)"r = requests.get(url, headers=headers)
c = r.content
soup = BeautifulSoup(c, "html.parser")
tables = soup.find_all(<tag>, {<attribute>: <value>})
```

一旦你得到了汤，你可以调用一个特定的 HTML 标签行，比如“h1”，“table”，“span”。这个公式将返回您要求的所有标签，如果它们存在于页面上的话。为了缩小搜索范围，我们可以传入一个属性参数。这可以是“风格”、“类别”等。例如，您可以说—返回所有“h1”<tag>和“文本-主要”<value>的“类”<attribute>。代码应该是这样的:</attribute></value></tag>

```
tables = soup.find_all(<h1>, {"class": "text-primary"})
```

如果 HTML 中存在表格，该函数将返回符合查询的所有 h1 的列表。如果没有匹配，这将返回一个空列表。

然后，我们运行这段代码来查找我们将在下面的代码中使用的所有链接。

## 下载文档

我们将使用命令行和 **wget** 来下载我们的文档，这是最快的方法，但是程序可以从 Python 内部编写，所以我们可以自动化它。

我们首先为此导入子流程。然后，我们需要设置下载 url 和保存文档的位置文件路径。对所有要下载的文档运行此代码。

```
import subprocessurl = '[http://www.dpsa.gov.za/dpsa2g/documents/vacancies/2021/05/a.pdf'](http://www.dpsa.gov.za/dpsa2g/documents/vacancies/2021/05/a.pdf')
location  = 'filepath-location-on-your-computer-documentName.pdf'def run_command(command):
    p = subprocess.Popen(command, stdout=subprocess.PIPE)
    out, err = p.communicate()
    return outrun_command(["wget", "-O", "{}".format(location), "{}".format(url)])
```

## 阅读文件

我们将使用 PyPDF2，PdfFileReader 来读取上面刚刚保存的文档。我们首先计算页面数量，然后收集每个页面的数据——之后，我们将这些信息提取到 Python 字符串列表中。这个字符串列表是基于文档部分划分的，这将允许我们稍后提取特定的职务信息。

```
from PyPDF2 import PdfFileReader
location  = 'filepath-location-on-your-computer-documentName.pdf'content_list = []
with open(location, 'rb') as f:
    doc = PdfFileReader(f)
    pages = doc.numPagescount = 0
    while count < pages:
        the_page = doc.getPage(count)
        the_text = the_page.extractText()
        a_list = the_text.replace('\n', '').split(' : ')
        for x in a_list:
            content_list.append(x)
        count += 1
```

## 提取数据并进行组织

如果你很好地分割了文档，下一步应该会更容易。我必须提到，这一步是针对文档设计的。本规范不适用于所有 PDF 文档。你必须通读你的文件，在文档中找到特定的元素，你可以用它来锚定你的内容搜索。我选择在分号“:”上分割文档，如上面的代码所示。因此，如果我找到了正确的索引，我就可以从该索引向前计数，以识别作业数据的所有后续部分。

我也很幸运，所有的文档都遵循相同的结构，所以这是可能的。

```
post_string = 'POST 05/'post_list = []
index_list = []final_jobs = []for x in content_list:
    if post_string in x:
        post_list.append(x)for x in post_list:
    the_index = content_list.index(x)
    index_list.append(the_index)for x in index_list:
    obj = {}#Titles
    a = content_list[x+1]
    obj['title'] = ab = content_list[x+2]
    obj['salary'] = bc = content_list[x+3]
    obj['location'] = cd = content_list[x+4]
    obj['requirements'] = de = content_list[x+5]
    obj['duties'] = ef = content_list[x+6]
    obj['enquiries'] = ffinal_jobs.append(obj)
```

作业数据现在将被组织到作业的 final_jobs 列表中。

## 将数据保存到数据库中

![](img/63e1ad00ea8c5f77e70d03ad1fa8c534.png)

这是最后一步——我们稍后将讨论模型文件。

```
#import models
from jobs.models import *
from django.contrib.auth.models import Userthe_user = User.objects.get(email='admin@skolo.online')
the_company = Company.objects.get(uniqueId='**********')
the_category = Category.objects.get(uniqueId='***********')for test_job in final_jobs: Jobs.objects.create(
    title = test_job['title'],
    location = test_job['location'],
    salary = test_job['salary'],
    requirements = test_job['requirements'],
    duties = test_job['duties'],
    date_posted = date.today(),
    enquiries = test_job['enquiries'],
    company = the_company,
    category = the_category,
    owner = the_user,
    )
```

这段代码必须在 Django 环境中运行才能访问模型和数据库。

# Django 工作、公司和类别的模型文件

如上面的代码所示，我们需要(1)工作，(2)公司和(3)类别的模型文件。我们下载的所有作业数据都将根据数据库中的模型进行组织。

jobs 类中的 models.py 文件应该如下所示:

```
from django.db import models
from django.utils import timezone
from django.contrib.auth.models import User
from django.template.defaultfilters import slugify
from uuid import uuid4class Company(models.Model):
    title = models.CharField(max_length=200, null=True, blank=True)
    description = models.TextField(null=True, blank=True)
    uniqueId = models.CharField(null=True, blank=True, max_length=100)
    companyLogo = models.ImageField(default='default.png', upload_to='upload_images')
    slug = models.SlugField(max_length=500, unique=True, blank=True, null=True)
    seoDescription = models.CharField(max_length=500, null=True, blank=True)
    seoKeywords = models.CharField(max_length=500, null=True, blank=True)def __str__(self):
        return '{} - {}'.format(self.title, self.uniqueId)def get_absolute_url(self):
        return reverse('company-detail', kwargs={'slug': self.slug})def save(self, *args, **kwargs):
        if self.uniqueId is None:
            self.uniqueId = str(uuid4()).split('-')[0]
            self.slug = slugify('Company {} {}'.format(self.title, self.uniqueId)) #company-absa-h37dhe3wfself.slug = slugify('Company {} {}'.format(self.title, self.uniqueId))
        self.seoDescription = 'Apply for {} Jobs on careers portal, start your career journey today'.format(self.title)
        self.seoKeywords = '{}, Jobs, Careers Portal, Apply Jobs'.format(self.title)
        super(Company, self).save(*args, **kwargs)class Category(models.Model):
    title = models.CharField(max_length=200, null=True, blank=True)
    description = models.TextField(null=True, blank=True)
    uniqueId = models.CharField(null=True, blank=True, max_length=100)
    categoryImage = models.ImageField(default='category.png', upload_to='upload_images')
    slug = models.SlugField(max_length=500, unique=True, blank=True, null=True)
    seoDescription = models.CharField(max_length=500, null=True, blank=True)
    seoKeywords = models.CharField(max_length=500, null=True, blank=True)def __str__(self):
        return '{} - {}'.format(self.title, self.uniqueId)def get_absolute_url(self):
        return reverse('category-detail', kwargs={'slug': self.slug})def save(self, *args, **kwargs):
        if self.uniqueId is None:
            self.uniqueId = str(uuid4()).split('-')[0]
            self.slug = slugify('Category {} {}'.format(self.title, self.uniqueId))self.slug = slugify('Category {} {}'.format(self.title, self.uniqueId))
        self.seoDescription = 'Apply for {} Jobs online, start your career journey today'.format(self.title)
        self.seoKeywords = '{}, Jobs, Careers Portal, Apply Jobs'.format(self.title)
        super(Category, self).save(*args, **kwargs)class Jobs(models.Model):
    FULL_TIME = 'Full Time'
    PART_TIME = 'Part Time'
    REMOTE = 'Remote'
    NOT_PROVIDED = 'N/A'
    TIER1 = 'Less than 2yrs'
    TIER2 = '2yrs - 5yrs'
    TIER3 = '5yrs - 10yrs'
    TIER4 = '10yrs - 15yrs'
    TIER5 = 'More than 15yrs'TYPE_CHOICES = [
        (FULL_TIME, 'Full Time'),
        (PART_TIME, 'Part Time'),
        (NOT_PROVIDED, 'N/A'),
        (REMOTE, 'Remote'),
    ]
    EXP_CHOICES = [
        (TIER1, 'Less than 2yrs'),
        (TIER2, '2yrs - 5yrs'),
        (TIER3, '5yrs - 10yrs'),
        (TIER4, '10yrs - 15yrs'),
        (TIER5, 'More than 15yrs'),
        (NOT_PROVIDED, 'N/A'),
    ]title = models.CharField(max_length=500, null=True, blank=True)
    company = models.ForeignKey(Company, on_delete=models.CASCADE, null=True, blank=True)
    category = models.ForeignKey(Category, on_delete=models.CASCADE, null=True, blank=True)
    location = models.CharField(max_length=500, null=True, blank=True)
    salary = models.CharField(max_length=500, null=True, blank=True)
    uniqueId = models.CharField(null=True, blank=True, max_length=100)
    type = models.CharField(max_length=100 ,choices=TYPE_CHOICES, default=NOT_PROVIDED)
    experience = models.CharField(max_length=100, choices=EXP_CHOICES, default=NOT_PROVIDED)
    summary = models.TextField(null=True, blank=True)
    description = models.TextField(null=True, blank=True)
    requirements = models.TextField(null=True, blank=True)
    duties = models.TextField(null=True, blank=True)
    enquiries = models.TextField(null=True, blank=True)
    applications = models.TextField(null=True, blank=True)
    note = models.TextField(null=True, blank=True)
    closing_date = models.DateField(blank=True, null=True)
    date_posted = models.DateField(blank=True, null=True)
    contract_type = models.CharField(max_length=500, null=True, blank=True)
    date_created = models.DateTimeField(default=timezone.now)
    owner = models.ForeignKey(User, on_delete=models.CASCADE)
    slug = models.SlugField(max_length=500, unique=True, blank=True, null=True)
    seoDescription = models.CharField(max_length=500, null=True, blank=True)
    seoKeywords = models.CharField(max_length=500, null=True, blank=True)
    urlLink = models.CharField(max_length=500, null=True, blank=True)def __str__(self):
        return '{} - {} - {}'.format(self.company, self.title, self.location)def get_absolute_url(self):
        return reverse('job-detail', kwargs={'slug': self.slug})def save(self, *args, **kwargs):
        #Creating a unique Identifier for the resume(useful for other things in future)
        if self.uniqueId is None:
            self.uniqueId = str(uuid4()).split('-')[0]
            self.slug = slugify('{} {} {}'.format(self.title, self.location, self.uniqueId))self.slug = slugify('{} {} {}'.format(self.title, self.location, self.uniqueId))
        self.seoKeywords = 'Careers Portal, Online job application, full time jobs, part time jobs, get a job, apply for a job, {}'.format(self.title)
        self.seoDescription = '{}'.format('Careers Portal, Job application. Apply for job: {} in {}, online today'.format(self.title, self.location))
        super(Jobs, self).save(*args, **kwargs)
```

模型文件类似于我们之前在[添加带有 slug 字段的 Django 模型并覆盖保存方法](https://skolo-online.medium.com/django-models-with-slugfield-override-model-save-method-and-custom-html-forms-73db161e2fb)时看到的。

然而，因为我们想最大限度地利用 SEO 元素，我们正在创建两个新的领域:(1) SEO 描述和(2) SEO 关键字。这些字段将在模型保存时自动填充——我们将从模型变量中自动创建 SEO 变量。

然后我们可以从 Django HTML 模板中访问这些变量。

# 从 Django Web App 在线视频教程的网站和 PDF 文档中删除数据。

要更详细地理解这些概念，请查看我们的视频教程:

为 Django web 应用程序教程从网站和 PDF 中删除数据