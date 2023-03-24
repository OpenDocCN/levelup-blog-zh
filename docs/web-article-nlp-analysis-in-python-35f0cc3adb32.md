# Python 中的网络文章 NLP 分析

> 原文：<https://levelup.gitconnected.com/web-article-nlp-analysis-in-python-35f0cc3adb32>

## 获取和分析 web 文档的管道

![](img/edd49ce2b997323054ef6d2612e59ef0.png)

[Unsplash 上的图像](https://unsplash.com/photos/n8Qb1ZAkK88)

[](https://jorgepit-14189.medium.com/membership) [## 用我的推荐链接加入媒体-乔治皮皮斯

### 阅读乔治·皮皮斯(以及媒体上成千上万的其他作家)的每一个故事。您的会员费直接支持…

jorgepit-14189.medium.com](https://jorgepit-14189.medium.com/membership) 

作为一名数据科学家和 NLP 专家，您可能需要提取 web 文章并进行分析。从网页中提取干净文本的一个简单方法是使用 T4 图书馆。对于本教程，我们将使用[预测黑客数据科学博客](https://predictivehacks.com/)，从“[如何在没有经验的情况下获得数据科学工作](https://predictivehacks.com/how-to-get-a-data-science-job-without-experience/)”文章开始。

您可以通过运行以下命令来安装`newspaper3k`库:

```
conda install -c conda-forge newspaper3k# ORpip3 install newspaper3k
```

# 如何从网络文章中提取文本

让我们看看获得预测黑客文章的文本有多容易。

```
from newspaper import Article# enter the required URL
url = '[https://predictivehacks.com/how-to-get-a-data-science-job-without-experience/'](https://predictivehacks.com/how-to-get-a-data-science-job-without-experience/')article = Article(url)
article.download()
article.parse()# Get the article text:
print(article.text)
```

**输出:**

```
What is “Working Experience” in practice?

Let’s assume that you are a candidate who has recently graduated from the University holding a Bsc/Msc degree in Mathematics, Computer Science, Engineering, or other related fields, and you would like to start a career as Data Scientist. The grades and the University degree indicate your theoretical background and your ability to learn new things. However, in the real world, things are different from the University, not necessarily more difficult, and somehow the hiring managers would like to know more about you and especially the way that you work.

A related “working experience” proves that you can do the required job. That you are reliable you have enthusiasm for your science and that you are good at your job. In other words, for the company, a candidate with related working experience implies a lower risk.

But as we said above you lack working experience, let’s see how you can prove to them that you are reliable and good at your job so that they can trust you, i.e. hire you!

The Era of Ratings and Reviews

Definitely, we live in an era of ratings and reviews. Don’t you look at the reviews on Booking.com and Airbnb when you plan where to stay, don’t you look at TripAdvisor and Google Reviews when you choose a restaurant to eat, don’t you look at the reviews of a movie before you watch it and so on and so forth.

The same applies to companies. They want to have a review about you. Bear in mind that is common to ask for reference before they hire someone and also they prefer to hire someone who is a referral of an existing employee. This occurs because they want to learn more about their potential colleague before they proceed to the hiring.

Let us see how you can get “reviews” from the market and in parallel to show your work.

Stack Overflow

Nobody can ignore a user with a high reputation in Stack Overflow. Clearly, it indicates that you are really passionate about your science, you can be a good team player by providing solutions to your team, you are competent in your field and you contribute to your community. In addition, you give the opportunity to everybody to look at your profile and your work.

Blogging

You will grab the hiring manager’s attention if you write in a blog. Let’s assume that you write in medium and you have many followers and claps. Having many followers implies that some people have found your article useful. It is like a positive review obtained from the market and the community. This is something that you can use as a working experience and also can be a show-case of what you can do. Also, your articles reflect somehow your working style and your field of interest and expertise.

Freelancer in a Web Platform

There are many platforms such as Upwork, Fiverr, PeoplePerHour where you can undertake data science projects. This is a good start and can be used as a related working experience. Moreover, in these platforms, you receive ratings and reviews from your clients. Definitely, a strong profile in such platforms proves your ability to deliver in high quality. It indicates also your ability to communicate with the clients, to be reliable and to have self-awareness.

GitHub Account

I would recommend having a GitHub account which is like the portfolio of the Data Scientist. By sharing your profile you give them the possibility to see the way that you code, the projects that you have done and so on.

Kaggle Account

Kaggle is the world’s largest data science community with powerful tools and resources to help you achieve your data science goals. If you participate in many competitions and you have a relatively good score it indicates that you are good in Data Science, that you have worked with real-life problems and you have dealt with many different problems.

Summing Up

If you lack working experience and you see that in most job openings ask for it, do not get disappointed. If you love your work and you are good at it is a matter of time when you will enter the industry. The most dangerous part is to have a gap in your career. For that reason, during the time that you do not work and you are looking for a job try to invest in the things that we mentioned above. By doing this, you will improve your skills such as writing, coding etc, you will get hands-on experience and at the same time, you can use them as a related working experience.

Thus, a candidate with 1000 points reputation in Stack Overflow, labeled as Top Rated freelancer in the Web Platforms with 500 followers in Medium and with an active Kaggle profile, his/her job application will stand out and will be asked for an interview where at this point they assess also other things like the cultural fit, the interpersonal skills etc.
```

我们可以提取其他特征，如“顶部图像”、“作者”和“出版日期”。

# NLP 分析

**newspaper3k** 库提供了一些基本的 NLP 特性，比如“关键词提取”和“文本摘要”。让我们看看下面的例子。

# 关键词提取

```
article.nlp() 
article.keywords
```

**输出:**

```
['hacks', 'data', 'experience', 'working', 'things', 'reviews', 'profile', 'related', 'science', 'good', 'job', 'predictive']
```

# 文本摘要

```
print(article.summary)
```

**输出:**

```
A related "working experience" proves that you can do the required job. In other words, for the company, a candidate with related working experience implies a lower risk. Freelancer in a Web PlatformThere are many platforms such as Upwork, Fiverr, PeoplePerHour where you can undertake data science projects. Kaggle AccountKaggle is the world's largest data science community with powerful tools and resources to help you achieve your data science goals. By doing this, you will improve your skills such as writing, coding etc, you will get hands-on experience and at the same time, you can use them as a related working experience.
```

# 提取文章的 URL

我们可以提取出现在主页上的帖子的 URL。例如:

```
import newspaper 
predictive_hacks = newspaper.build('https://predictivehacks.com') for article in predictive_hacks.articles: 
    print(article.url)
```

**输出:**

```
[https://predictivehacks.com/how-to-create-a-simple-streamlit-app-how-to-deploy-it-on-heroku/](https://predictivehacks.com/how-to-create-a-simple-streamlit-app-how-to-deploy-it-on-heroku/)
[https://predictivehacks.com/10-tips-and-tricks-for-data-scientists-vol-12/](https://predictivehacks.com/10-tips-and-tricks-for-data-scientists-vol-12/)
[https://predictivehacks.com/python-pip-tips-for-data-scientists/](https://predictivehacks.com/python-pip-tips-for-data-scientists/)
[https://predictivehacks.com/unix-sed-command-tutorial-with-examples/](https://predictivehacks.com/unix-sed-command-tutorial-with-examples/)
[https://predictivehacks.com/how-to-connect-snowflake-with-s3-and-ec2-using-python/](https://predictivehacks.com/how-to-connect-snowflake-with-s3-and-ec2-using-python/)
[https://predictivehacks.com/awk-tutorial-for-data-scientists-and-engineers/](https://predictivehacks.com/awk-tutorial-for-data-scientists-and-engineers/)
[https://predictivehacks.com/how-to-choose-and-apply-the-right-statistical-test-in-python/](https://predictivehacks.com/how-to-choose-and-apply-the-right-statistical-test-in-python/)
[https://predictivehacks.com/interview-question-a-variation-of-russian-roulette/](https://predictivehacks.com/interview-question-a-variation-of-russian-roulette/)
[https://predictivehacks.com/how-to-generate-the-requirements-of-your-python-project-based-on-your-imports/](https://predictivehacks.com/how-to-generate-the-requirements-of-your-python-project-based-on-your-imports/)
[https://predictivehacks.com/how-to-schedule-tasks-in-snowflake/](https://predictivehacks.com/how-to-schedule-tasks-in-snowflake/)
```

# 外卖

我发现 newspaper3k 真的很快，它非常适合从网络文章中提取文本。然而，我更喜欢文本摘要的[变形金刚](https://predictivehacks.com/text-summarization-with-transformers/)

【https://predictivehacks.com】原载于[](https://predictivehacks.com/web-article-nlp-analysis-in-python/)**。**