# Python 中的单元测试——补丁、模拟和依赖注入

> 原文：<https://levelup.gitconnected.com/unit-testing-in-python-mocking-patching-and-dependency-injection-301280db2fed>

## 如何测试代码中的困难部分

![](img/691a1c454a3183f76642b7ee2c6c0deb.png)

来源:[安德里亚·皮亚卡迪奥](https://www.pexels.com/photo/woman-in-white-shirt-showing-frustration-3807738/)

使用 Python 和 pytest 进行单元测试通常是微不足道的，但是当许多开发人员不得不修补依赖关系以使代码可测试时，他们会感到沮丧。在本文中，您将学习如何修补和使用模拟。如果您想重温 Python 中单元测试的基础知识，请看一下本系列的第一部分:[Python 中的单元测试—基础知识](https://medium.com/swlh/unit-testing-in-python-basics-21a9a57418a0#0e28)。

# 问题的抽象模式

我们想要测试的函数的一个依赖项会以三种不同的方式产生影响:副作用、返回值或异常。

问题 1:依赖性副作用

```
def a_function():
    ...  # Application code to be tested
    a_dependency()
    ...  # Application code to be tested
```

问题 2:依赖项返回值

```
def a_function():
    ...  # Application code to be tested
    foo = a_dependency()
    ...  # Application code to be tested; it might use foo
```

问题 3:依赖关系抛出异常

```
def a_function():
    ...  # Application code to be tested
    try:
        foo = a_dependency()
    except:
        ...  # Application code to be tested
        ...  # this might depend on the type of Exception
   ...  # Application code to be tested
```

# 问题——简单的例子

大多数例子都要复杂得多，通常还需要一些重构来使代码更容易维护。所以我创建了三个例子，它们更接近真实的应用程序，同时又避免了真实应用程序的膨胀。

示例 1:我们想向数据库添加一个用户。你可以看到`db`没有返回任何东西，但是我们改变了系统的状态。我们希望确保在单元测试运行时，我们实际上没有改变我们的生产系统！

```
import bcrypt
from models import db, Userdef insert_user_into_db(username, password):
    password_hash = bcrypt.hashpw(password.encode('utf-8'), bcrypt.gensalt(12))
    user = User(password=password_hash, username=username)
    db.session.add(user)
    db.session.commit()
```

示例 2:基于当前日期生成文件名。您可以看到依赖关系`datetime`返回值:

```
import datetimedef generate_filename():
    return f"{datetime.datetime.now():%Y-%m-%d}.png"
```

类似地，您可以想象一个函数，它返回英语句子中的天气，并使用 API 获得实际的天气([示例](https://gist.github.com/MartinThoma/5c7224ceae47e74645e0145d26dc03ec))。

例 3:在我的项目`[edapy](https://github.com/MartinThoma/edapy)`中，我查看了 PDF 文件中的元数据。我使用依赖项 PdfFileReader，并将文件本身作为依赖项。由于 PDF 文件可能会被破坏，PyPDF2 可能会抛出异常。所以你可以想象这样的代码:

```
import PyPDF2.utils
from PyPDF2 import PdfFileReaderdef get_pdf_info(pdf_path):
    info = {}
    try:
        pdf_toread = PdfFileReader(fp, strict=False)
    except PyPDF2.utils.PdfReadError:
        info["is_errornous"] = True
        return info# a lot more
    return info
```

当你想测试这样的函数时，你会遇到这样的问题:期望的输出不仅依赖于函数本身，还依赖于外部的东西。在上面的例子中，系统时间、外部服务和文件系统。

# 外部依赖关系的示例

您的测试可能有许多外部依赖:

*   日期或时间
*   互联网:你需要使用的网络服务
*   文件系统:您需要创建/读取/编辑/删除的文件
*   数据库:您选择/插入/更新/删除的数据
*   随机性:您的代码可能会使用`random`或`np.random`

就像上面的例子一样，它们使得独立的单元测试变得困难甚至不可能。

# 解决方案:打补丁！

测试这一点的总体策略总是相同的:用你能控制的东西来代替让你头疼的外部依赖。替换依赖的行为叫做 ***打补丁*** ，替换的行为叫做 ***嘲弄*** 。根据 mock 具体做什么，您可能还会听到它被称为 Test Double、Test Stub、Test Spy 或 Fake Object。在 Python 的实践中，这种区别并不重要。如果你感兴趣，我推荐[马丁·福勒:《模仿和树桩的区别](https://martinfowler.com/articles/mocksArentStubs.html#TheDifferenceBetweenMocksAndStubs#TheDifferenceBetweenMocksAndStubs)。我会把它们都叫做嘲笑。

让我们举一个小例子，如何使用补丁！

无论您使用哪个`transaction`，函数`is_credit_card_fraud`都会抛出一个 ValueError。

这就是你如何用装饰者`@patch`修补依赖性的方法:

这就是如何用上下文处理程序(`with ...`)修补依赖关系`fraud_example.dark_magic`:

当您现在执行`pytest`时，测试将会成功。你将总是得到 0.999 作为`dark_magic`的返回值🎉

本例中可能令人惊讶的部分是`patch`装饰器的第一个参数:它是`"fraud_example.dark_magic"`而不是`"external_dependency.dark_magic"`！替换的目标总是您想要测试的文件中加载的内容，而不是它是从哪里加载的。丽莎·罗奇在她的演讲[中优雅地指出了这一点，揭开了补丁功能的神秘面纱](https://www.youtube.com/watch?v=ww1UsGZV8fQ)。

# 直接替换:不要这样！

下面是一个不使用`patch`的例子，看似可行，但有一个很大的缺陷。如果您直接替换`datetime.datetime`而不是修补它，它将在之后的所有其他上下文中被覆盖！⚠️

# 模仿和魔术

您现在知道了如何替换一个依赖项，因此是时候讨论用什么来替换它了。这就是`unittest.mock.Mock`和`unittest.mock.MagicMock`发挥作用的地方。

你用 Mock 做的所有事情都会返回一个 Mock。调用函数？获取一个 Mock 作为返回值。访问属性？获取一个模拟值。

Python 有所谓的“神奇”方法。我更喜欢术语“dunder”方法——它只是指所有以分数**d**double**开始和结束的方法。例如`__iter__`或`__contains__`。MagicMock 有这些定义，Mock 没有。我会在任何地方使用 MagicMock，除非被模仿的对象没有定义任何魔法函数。**

mock 类的一个核心特性是，它们不仅允许您删除难以测试的依赖项，还允许您断言与 mock 交互的方式。典型的方法有 [assert_called](https://docs.python.org/3/library/unittest.mock.html#unittest.mock.Mock.assert_called) ()， [assert_called_with](https://docs.python.org/3/library/unittest.mock.html#unittest.mock.Mock.assert_called_with) ()， [assert_not_called](https://docs.python.org/3/library/unittest.mock.html#unittest.mock.Mock.assert_not_called) ()。

# 规格，自动规格&规格集

`MagicMock`真正不好的一点是，你可以用它做任何事情——包括访问不存在的属性、调用不存在的方法或者用错误的参数个数调用现有的方法。模拟对象缺少一个 T21 规格。如果你不喜欢这样，在修补对象时使用`autospec=True`:

```
patch.object(Foo, 'foo', autospec=True)
```

或者你可以创建一个这样的模拟:

```
>>> import datetime
>>> from unittest.mock import Mock
>>> a = Mock(spec=datetime)# Not Ok!
>>> a.foo
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/moose/.pyenv/versions/3.8.1/lib/python3.8/unittest/mock.py", line 635, in __getattr__
    raise AttributeError("Mock object has no attribute %r" % name)
AttributeError: Mock object has no attribute 'foo'# That is ok:
>>> a.datetime
<Mock name='mock.datetime' id='139883597784544'>
```

`patch`的下一个参数是 autospec。spec 查看被模仿的对象，`autospec`也查看该对象的属性(以及它们的属性和那些属性，…)。

最后还有`spec_set`。这可以防止你设置不存在的属性。

通常，我会在任何地方使用`autospec=True`和`spec_set=True`。使用自省的代码可能是一个你不想要的例子。

# 猴子补丁

`monkeypatch`是 pytest 的夹具。我将在下一篇文章中解释什么是夹具。现在，只需接受它作为一个参数，您可以在不指定它的情况下将其赋予您的测试，pytest 会处理它。你甚至不需要导入任何东西。

对于信用卡欺诈的例子，它看起来像这样:

如今，什么时候应该使用`unittest.mock.patch`和——如果必要的话——`unittest.mock.Mock`或者 pytests `monkeypatch`的问题可以归结为个人喜好。核心 Python 补丁/模仿只存在于 Python 3.3 之后，我想这是`monkeypatch`存在的主要原因。

# 外部包装

有几个软件包旨在简化修补，并为众所周知的依赖关系提供更好的模拟。

例如，您可以使用[冷冻枪](https://pypi.org/project/freezegun/)来模拟系统时间:

```
import freezegun
from mock_example import generate_filenamedef test_generate_filename():
    with freeze_time("1990-04-28"):
        assert generate_filename() == "1990-04-28"
```

对于 boto3 / botocore (Cloud-stuff)，有 [moto](https://pypi.org/project/moto/) 。

对于`[requests](https://pypi.org/project/requests/)`，有`[responses](https://pypi.org/project/responses/)`:

# 依赖注入

如果以上听起来很复杂，还有一个更简单的选择:依赖注入。本质上是将外部状态明确地添加为一个参数，这使得在测试中容易调整。例如，上面的代码可能是:

```
import datetimedef generate_filename(now=None):
    if now is None:
        now = datetime.datetime.now()
    return f"{now:%Y-%m-%d}.png"
```

现在测试很简单:

```
import datetime
from mock_example import generate_filenamedef test_generate_filename():
    now = datetime.datetime(1990, 4, 28)
    assert generate_filename(now) == "1990-04-28.png"
```

在某些情况下，应用这种模式感觉非常自然，而在其他情况下则不然。只有在感觉自然的时候才这样做。例如，我不太可能将模块作为参数传递，尽管这是可能的。那会感觉很奇怪。

# 临时文件:模拟是代码的味道吗？

这很大程度上取决于细节，但我喜欢尽可能少地嘲讽。原因很简单，不嘲笑意味着你要测试更多的系统。严格地说，如果你测试了不止一个单元，你就不能再称这个测试为单元测试了。这将是一个整合测试，但这也是必不可少的，对不对？如果宝马卖给你一台发动机、一些座椅和一个方向盘，并声称“所有部件都能工作”，你不会高兴的。他们需要合作。大量的模拟可能会阻止您测试事物如何协同工作。

在一个理想的世界中，你将拥有两者:单元测试是非常可控的，并且在失败的情况下，可以很容易地缩小错误的来源。和集成/端到端测试，表明整个系统工作正常。

也有人认为嘲笑的需求是重构需求的指示器([讨论](https://github.com/pytest-dev/pytest/issues/4576#issuecomment-449865322))。Harry Percival 在 PyCon 2020 上发表了演讲[停止使用模拟(一段时间)](https://www.youtube.com/watch?v=rk-f3B-eMkI)，并指出使用模拟的测试代码往往很脆弱，因为它与实现细节紧密相关。

我通常不嘲笑任何东西的一个好例子是文件系统交互。如果可能的话，我会像在实际应用程序中一样编写文件。当测试完成时，测试也需要清理。为此我使用了`[tempfile](https://docs.python.org/3/library/tempfile.html)`模块。

# 依赖注入:随机性

就像给默认使用当前时间的函数添加一个时间参数可能会让你的代码更容易测试一样，给使用随机性的函数添加一个`random_state`参数或者一个`seed`参数也会有所帮助。

以下是生成随机数生成器的一些方法:

```
>>> import random
>>> random.seed(0)
>>> random.random()
0.8444218515250481>>> import numpy as np
>>> np.random.seed(0)
>>> np.random.random()
0.5488135039273248>>> random_state = np.random.RandomState(seed=0)
>>> random_state.random()
0.5488135039273248
```

设置一个随机状态/种子对调试也很有帮助。如果你没有听说过海森堡或希格斯粒子，你就错过了一些编程术语。如果你对研究感兴趣，再现性很重要。

# 术语

*   **修补 vs 嘲讽**:修补一个函数就是调整它的功能。在单元测试的上下文中，我们修补掉依赖关系；所以我们替换了依赖关系。嘲讽就是模仿。通常我们修补一个函数来使用我们控制的模拟，而不是我们不控制的依赖。
*   **猴子补丁 vs 嘲讽**:在开发环境中，嘲讽很明显是关于单元测试的([例子](https://stackoverflow.com/a/2666006/562769))。然而，除了单元测试之外，monkey patching 还有几个应用。例如，如果缺少一小部分功能或部分代码被破坏，您可以在运行时修补第三方代码。你只要扩展代码。PyCharm 调试器中使用了 Monkey 补丁([来源](https://youtu.be/ZpJxwpyJpq4?t=367))。
*   **Monkey patching vs pytest . Monkey patch**:第一个是一般概念，第二个是 pytest 中的具体函数，它将 Monkey patching 应用于单元测试。
*   **unittest . mock . patch vs pytest . monkey patch**:这是个人喜好。每当第三方选项没有太大优势时，我更喜欢坚持使用内置。在这种情况下，我甚至认为核心 Python unittest.mock.patch 更干净。由于这个原因，到目前为止我没有解释 pytest.monkeypatch。如果你想知道其中的区别，有一篇关于它的不错的[博文](https://krzysztofzuraw.com/blog/2016/mocks-monkeypatching-in-python.html)。

# 关于建筑的一个注记

为了保持代码的整洁，包装第三方依赖项通常是个好主意。例如，你可以有一个处理 I/O 的模块，或者一个处理 API 请求的模块。然后你有几个模块可能需要很多嘲讽，或者单元测试是没有意义的，因为有趣的部分是与第三方的集成。您的代码的其余部分保持易于测试，保持您定义的语言，并关心您知道的对象。这被称为[适配器模式](https://en.wikipedia.org/wiki/Adapter_pattern)。

# 还有什么？

*   其他类型的模拟，如 [PropertyMock](https://docs.python.org/3/library/unittest.mock.html#unittest.mock.PropertyMock) 或
*   [pytest-mock](https://pypi.org/project/pytest-mock/) ，提供模拟夹具；虽然我不确定这主要是 Python 3.3 之前的遗留问题，还是它真的让事情变得更简单了。
*   第三方包[模仿](https://pypi.org/project/mock/)，不应该和 Python 3.3+一起安装，因为它被放在了标准库中。

如果你想了解更多关于默认模拟的信息，可以看看耶尔雷·迪亚兹写的一篇很棒的文章:[什么是模拟？Python 中嘲讽的备忘单](https://medium.com/@yeraydiazdiaz/what-the-mock-cheatsheet-mocking-in-python-6a71db997832)。

# 下一步是什么？

在这个系列中，我们已经有了:

*   第 1 部分:[Python 中单元测试的基础知识](https://medium.com/swlh/unit-testing-in-python-basics-21a9a57418a0)
*   第 2 部分:[补丁、模拟和依赖注入](/unit-testing-in-python-mocking-patching-and-dependency-injection-301280db2fed)
*   第 3 部分:[如何用数据库、模板和受保护的页面测试 Flask 应用程序](https://medium.com/analytics-vidhya/how-to-test-flask-applications-aef12ae5181c)
*   第 4 部分:[毒性和氮氧化物](https://medium.com/python-in-plain-english/unit-testing-in-python-tox-and-nox-833e4bbce729)
*   第 5 部分:[结构化单元测试](https://medium.com/python-in-plain-english/unit-testing-in-python-structure-57acd51da923)
*   第 6 部分:[CI-管道](/ci-pipelines-for-python-projects-9ac2830d2e38)
*   第 7 部分:[基于属性的测试](/unit-testing-in-python-property-based-testing-892a741fc119)
*   第八部分:[突变测试](https://medium.com/analytics-vidhya/unit-testing-in-python-mutation-testing-7a70143180d8)
*   第 9 部分:[静态代码分析](https://towardsdatascience.com/static-code-analysis-for-python-bdce10b8d287) — Linters、类型检查和代码复杂性
*   第 10 部分: [Pytest 插件来爱](https://towardsdatascience.com/pytest-plugins-to-love-%EF%B8%8F-9c71635fbe22)

如果您对使用 Python 测试的其他主题感兴趣，请告诉我。