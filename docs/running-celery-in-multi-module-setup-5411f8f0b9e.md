# 在多模块设置中运行芹菜

> 原文：<https://levelup.gitconnected.com/running-celery-in-multi-module-setup-5411f8f0b9e>

![](img/9a5f3ea4d285c75037fea5a3018efc98.png)

这篇文章是针对芹菜初学者的。它讨论了种植芹菜的不同方法。

*   使用具有单一模块的芹菜
*   使用具有多个模块的芹菜。
*   使用 celery，将多个模块拆分成多个包。

## 先决条件

你应该安装芹菜和 Redis。我们将使用 Redis 作为我们的经纪人。

## 使用具有单一模块的芹菜

让我们创建一个名为`tasks.py`的模块，内容如下:

```
from celery import Celeryapp = Celery('tasks', broker='redis://localhost')@app.task
def hello():
    return 'hello'
```

让我们开始芹菜工人

```
celery -A tasks worker -l INFO
```

让我们启动一个 shell 并对一个任务进行排队。

```
In [1]: from tasks import hello
In [2]: hello.apply_async()
Out[2]: <AsyncResult: 079bd2b9-05da-4660-9064-b369b3863cb0>
```

您应该会在工人控制台上看到一条消息。这验证了芹菜工人处理了任务。

# 使用具有多个模块的芹菜。

让我们用下面的内容创建一个名为`mathematics.py`的模块。

```
from tasks import app@app.task
def sum(a, b):
    return a + b
```

我们需要请求 Celery `app`实例包含这个模块，这样当 worker 启动时，它就知道这个模块的任务。

因此，`tasks.py`中的`app`实例化需要修改如下:

```
app = Celery('tasks', broker='redis://localhost', include=['mathematics'])
```

重启芹菜工。

重启 python shell，将任务`sum`排队。

```
In [2]: from mathematics import sum
In [4]: sum.apply_async(args=(3, 4))
Out[4]: <AsyncResult: 1ba8aea0-a604-49d6-aa45-a0315d2b58c6>
```

您应该会在工作人员的控制台上看到计算出的总和。这验证了芹菜工人处理了任务。

## 在多包装设置中使用芹菜

让我们创建一个名为`internationalization`的包。

```
╰─$ mkdir internationalization╰─$ touch internationalization/__init__.py
```

让我们在其中创建模块`german.py`和`french.py`。

```
# internationalization/german.pyfrom tasks import app@app.task
def hello():
    return "Hallo"
```

国际化/french.py

```
# internationalization/french.pyfrom tasks import app@app.task
def hello():
    return 'Bonjour'
```

我们需要在我们的芹菜应用程序中包含这些任务模块。修改 tasks.py 中的`app`,使其看起来像:

```
app = Celery('tasks', broker='redis://localhost', include=['mathematics', 'internationalization.french', 'internationalization.german'])
```

重启芹菜工。

重启 python shell，对来自 german.py 和 french.py 的任务`hello`进行排队

```
In [1]: from internationalization.french import hello
In [2]: hello.apply_async()
Out[2]: <AsyncResult: 09297a37-2733-4820-8f37-cd6eeeb5ce5b>
```

您应该会在工作人员的控制台上看到该消息。这验证了芹菜工人处理了任务。

随时询问任何关于芹菜的问题，我很乐意回答。