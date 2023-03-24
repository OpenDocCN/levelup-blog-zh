# 下一个 Django 版本将会有更好的测试用例隔离

> 原文：<https://levelup.gitconnected.com/next-django-version-will-have-better-testcases-isolation-71959eb7b62c>

![](img/80820519c5061f29549c6a52df03f0c2.png)

照片由[里卡多·戈麦斯·安吉尔](https://unsplash.com/@ripato?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/photos/VnOs8OuCvVY) 上拍摄

正如您可能知道的，Django 发布了它的 TestCase 类，带有事务的[两级嵌套](/using-transactions-to-make-django-tests-run-faster-224fdd695bfd):两个 TestCase 类彼此独立存在，TestCase 中的单个测试也是如此。这个特性是很多年前在 Django 1.8 中引入的，但是它有一个问题:隔离只在数据库级执行。因此，有可能编写一个粗心的测试，错误地依赖于 python 级别的隔离:

这里，`test1`“破坏”了 python 对象的状态，`test2`足够智能来解决这个问题，`test3`失败，因为回滚事务不会修改相应 python 对象的状态。

解决这个问题的标准方法(如果简单地编写不依赖于 python 对象隔离的测试由于某种原因不可行)是

这需要在每个测试函数的开始为每个涉及的模型进行额外的 db 查询，但在其他方面效果很好。

在即将到来的 django 版本中，不再需要这个黑客了！神奇的是在 TestData 类中自动发生的——不需要对项目进行任何更改，它深度复制相关对象并在必要时重置它们。该解决方案一直存在于第三方应用程序 [django-testdata](https://github.com/charettes/django-testdata) 中，并随着错误报告获得了一些用户群。现在它已经足够成熟，可以进入 django master(ticket[# 31395](https://code.djangoproject.com/ticket/31395))了，你只要在 git 中切换 django 分支，就可以在开发机器上试一试了。

[1]Lev Maximov(2020 年 6 月 22 日)*使用事务使 Django 测试运行更快*[https://level up . git connected . com/Using-Transactions-to-Make-Django-Tests-Run-Faster-224 FDD 695 bfd](/using-transactions-to-make-django-tests-run-faster-224fdd695bfd)

[2] Django 3.0 文档。*测试工具/test case . setuptestdata()*[https://docs . django project . com/en/3.0/topics/Testing/tools/# django . test . test case . setuptestdata](https://docs.djangoproject.com/en/3.0/topics/testing/tools/#django.test.TestCase.setUpTestData)

[3] Django 第 31395 号机票。(2020 年 3 月 22 日)*让 TestCase.setUpTestData 强制执行内存数据隔离。*[https://code.djangoproject.com/ticket/31395](https://code.djangoproject.com/ticket/31395)