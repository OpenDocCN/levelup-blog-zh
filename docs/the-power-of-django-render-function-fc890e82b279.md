# Django 渲染功能的威力

> 原文：<https://levelup.gitconnected.com/the-power-of-django-render-function-fc890e82b279>

用 Django 中的模板渲染函数可以做什么？

# 动机

![](img/d44e5592cdac6777db4082c527b4d069.png)

我们为教授编写了一个小型测试平台，允许向我们大学的学生自动发送电子邮件。一位教授可能需要发送大约 100 封包含不同数据的电子邮件。

所以，我们决定让教授们自己编写邮件模板。一封电子邮件看起来像这样:

```
**you need to pass the following test: 
Linear Algebra I****You will have 60 minutes. Once time is over, the test will be stopped automatically. 

The number of question in the test is 30\. 

To enter the system, you need these credentials:
Login: john123
Password: 37823****Test URL: someurl.com****We wish you a good luck!**
```

问题是，登录名和密码是由系统生成的，因此我们需要将这些凭据发送给学生。如你所见，有很多地方的课文是学生专用的。

# 天真的方式

首先，我们认为这个任务非常简单，我们所需要的只是 Python 中的一个“替换”函数。

因此，代码看起来像这样:

```
template = Template(template_text)
template_filled = template.replace(**"first_name"**, assignment.student.first_name)
template_filled = template.replace(**"boyfriend_girlfriend"**, assignment.student.boyfriend_girlfriend)
template_filled = template.replace(**"whatever_else"**, assignment.student.whatever_else)
send_email(assignment.student.email, template_filled)
```

![](img/218953c93de8188d1a433fd2c252f991.png)

问题是我们的教授每天都想包含越来越多不同的变量。此外，如果名字在文本中出现两次，这段代码也不起作用。而且有太多的硬编码。讨厌那个！

# Django 模板

然后我们意识到这看起来很熟悉。我们在 Django 中生成 HTML 输出时也是这样做的！为什么不重用这个？令人惊讶的是，这是如此简单！

代码现在看起来像这样:

```
**from** django.shortcuts **import** render
**from** django.template **import** Template, Context
**for** assignment **in** assignments:
  template = Template(template_text)
  context = Context({**'assignment'**: assignment})
  send_email(assignment.student.email, template.render(context))
```

那不是很美吗？

因此，我们的 template_text 现在看起来像这样:

```
**Dear {{assignment.person.first_name}}, /n /n****you need to pass the following test: /n
{{assignment.quiz_structure.name}}/n****You will have {{assignment.quiz_structure.minutes}} minutes. Once time is over, the test will be stopped automatically. /n
/n
The number of question in the test is {{assignment.quiz_structure.quantity}}/n /n****To enter the system, you need these credentials:
Login: {{assignment.person.user.username}}/n
Password: {{assignment.person.password}}/n****Test URL: someurl.com/n /n
We wish you a good luck!**
```

如果教授们愿意，他们现在可以使用“for-loops”。是的，他们希望:)

# 对该函数的进一步研究

让我们看看更多的寺庙渲染的例子，以便更好地理解它是如何工作的。

我们需要做两件事:

1.  使用“模板”功能写一些文本并创建一个模板
2.  将上下文数据传递给呈现器。

这里有一个简单的例子:

```
template = Template(**"Hello, {{var1}}!"**)
context = Context({**"var1"**: **"world"**})
**print**(self.style.SUCCESS(template.render(context)))
```

这导致“你好，世界！”，很明显。

下面是一个 for 循环的例子:

```
template = Template(
**"""
{% for student in students %}
Hello, {{student}}! 
{% endfor %}
"""**)
context = Context({**"students"**: [**"John"**, **"Andrew"**, **"Victor"**]})
**print**(self.style.SUCCESS(template.render(context)))
```

正如你所看到的，教授们甚至能够提前发送每个学生的所有问题:)我确信他们没有这样做。

感谢阅读！

你的 Django 项目运行太慢了吗？看看这篇文章:

[](/optimizing-django-queries-28e96ad204de) [## 优化 Django 查询

### 使用批量查询、预加载外键等。

levelup.gitconnected.com](/optimizing-django-queries-28e96ad204de)