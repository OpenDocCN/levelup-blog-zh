# pytest 综合指南

> 原文：<https://levelup.gitconnected.com/a-comprehensive-guide-to-pytest-3676f05df5a0>

## 欢迎来到丛林，我们有乐趣和游戏！

![](img/11506710a5ae2c65963de95853ed40f9.png)

不可否认，测试的丛林绝不是*而是*“乐趣和游戏”—照片由 [Pascal Debrunner](https://unsplash.com/@debrupas?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

*测试*。软件工程的一个方面引起了来自世界各地开发者的抱怨。虽然我没有万灵药，但是如果您正在使用 Python，并且需要关于单元测试(使用 pytest)的详尽文献，就不要再找了。

为了获得这样的知识，我跌跌撞撞地穿过神秘的文档森林，深入到 Stack Overflow 充满敌意的水域，在谷歌无情的山脉中跨过第 37 页陡峭的悬崖。希望这是你在这个话题上需要的最后一篇文章。

# 入门指南

安装 pytest 和相关依赖项:

```
pipenv install --dev pytest pytest-cov pytest-mock
```

还有更多的插件，但是在你需要任何其他东西之前，覆盖率和模拟应该还有很长的路要走。

我还使用了一个名为 *pipenv* 的包管理器，我强烈推荐这个包管理器，它可以轻松方便地管理包。Opensource.com 有一篇关于为什么你应该用它而不是香草的极好的文章。

# *pytest* 设置

将一个`conftest.py`文件放在项目的根目录下。该文件:

*   包含 pytest 的“全局”设置，包括标记注册(稍后将详细介绍)和全局共享的 fixtures(稍后也将详细介绍)
*   负责给 pytest 范围；通过将文件放入根目录，pytest 将识别 src 模块，而不需要手动将其添加到`PYTHONPATH`

pytest 在发现测试方面做得很好，所以实际的测试可以放在任何地方。我推荐以下文件夹结构:

```
/
|- <project name>/
|- tests/
   |- acceptance/
   |- unit/
      |- # tests go here
```

开箱即用，所有 pytest 测试文件必须以*为前缀*或*为后缀*并带有“test*”*(单数)。例如，如果你正在测试一个叫做`handler.py`的模块，你的测试文件应该叫做`test_handler.py`或者`handler_test.py`。测试文件实际上不需要共享要测试的模块的基本名称——它可以被称为`test_zzyzx_the_end_of_the_world.py`,它仍然可以工作——但是为了保持理智，你可能不应该这样做。

单元测试本身可以按照任何有意义的方式来组织，但是我建议反映实际代码的结构。这允许您快速找到给定代码的相关测试。**单元测试应该只测试每个文件中包含的逻辑(换句话说:模拟任何导入)**，一个显著的例外是定制的异常/错误类，它们可能会在给定的模块中被无意地测试。

# 写作测试

对于准系统测试，您*不需要*导入 pytest 模块。您可以为方法编写的最简单的 pytest 测试如下:

```
def hello_world():
    return 'hello world'def test_function():
    assert hello_world() == 'hello world'
```

pytest 对大多数断言使用标准 Python `assert` s。pytest 甚至可以检查断言对象的上下文，例如:

```
def hello_worlds():
    return {
        'english': 'hello world',
        'chinese': '你好世界'
    }def test_function():
    assert hello_world() == {'english': 'hello wurld',
                             'japanese': 'ハロー・ワールド'}E   AssertionError: assert {'english': 'hello world', 'chinese': '你好世界'} == {'english': 'hello wurld', 'japanese': 'ハロー・ワールド'}
E     Differing items:
E     {'english': 'hello world'} != {'english': 'hello wurld'}
E     Left contains 1 more item:
E     {'chinese': '你好世界'}
E     Right contains 1 more item:
E     {'japanese': 'ハロー・ワールド'}
```

这就是在不导入 pytest 的情况下可以做到的程度。本文档的其余部分涵盖了需要`import pytest`的特性。

pytest 还可以检查异常的存在和上下文。

```
import pytestdef hello_error():
    raise NotImplementedErrordef test_function():
    with pytest.raises(NotImplementedError):
        hello_error()def test_context():
    with pytest.raises(NotImplementedError) as e:
        hello_error()
    assert e.xyz == abc
    # note that the exception context object is referenced *outside* the `with` block
```

在一个测试函数下可以有多个断言；是否应该这样做完全是一个语义决定。

# 固定装置

fixture 是可重用代码的独立部分，可以从测试和其他 fixture 中调用。它们通常用于安装和拆卸，但也可以用于抽象重复的动作。

# 夹具基础

固定物用`@pytest.fixture`装饰，并通过传递固定物的名称来请求。一种方法可以请求多个设备。夹具也可以请求其他夹具。

```
import pytest

@pytest.fixture
def hello():
    return 'hello'

@pytest.fixture
def world():
    return 'world'

@pytest.fixture
def hello_world(hello, world):
    return hello + ' ' + world

def test_function(hello_world, hello, world):
    assert hello_world = hello + ' ' + world@pytest.fixture
def hello():
    return 'hello'@pytest.fixture
def world():
    return 'world'@pytest.fixture
def hello_world(hello, world):
    return hello + ' ' + worlddef test_function(hello_world, hello, world):
    assert hello_world = hello + ' ' + world
```

Fixtures 有独立的执行上下文*，除了在一个测试中被多次调用的*。

以下示例在不同的测试中请求相同的 fixture，并为每个测试提供不同的对象。

```
import pytest

@pytest.fixture
def a_list():
    return ['a']

def test_str(a_list):
    a_list.append('b')
    assert a_list == ['a', 'b']

def test_int(a_list):
    a_list.append(2)
    assert  a_list == ['a', 2]
```

另一方面，这个例子在*相同的*测试中请求了两次相同的 fixture(一次是传递的，第二次是直接的)，并返回相同的对象。

```
import pytest

@pytest.fixture
def a_list():
    return ['a']

@pytest.fixture
def append_b(a_list):
    return a_list.append('b')

def test_int(b_list, a_list):
    assert  a_list == ['a', 'b']
```

# 花式夹具(官方称为“作为夹具的工厂”)

为了能够向 fixture 传递参数(例如，动态生成输入数据)，您需要使用工厂作为 fixture 范例。本质上，fixture 本身返回一个函数来完成您需要的功能。

```
import pytest

@pytest.fixture
def gen_input():
    def _gen_input(a: bool):
        return {'a': a}

    return _gen_input

def test_input(gen_input):
    assert gen_input(True) == {'a': True}
```

不幸的是，文档字符串和代码完成特性并不能很好地适应这种范式，但是这并不意味着您不应该包含文档！

# 自动使用装置

您可以指定在每次测试中自动使用夹具。如果所有的测试都有一个通用的设置组件，这是很有用的。

```
import pytest

@pytest.fixture(autouse=True)
def some_func():
    # do stuff before every test
```

然而，请注意，autouse fixtures 将*而不是*产生任何对象，除非它们被测试函数明确请求。

# 共享夹具和夹具生命周期

要跨不同模块共享夹具，将它们放在`conftest.py`文件中。

默认情况下，每个函数调用和拆除一次 fixtures。您可以对此进行更改，使 fixture 的执行上下文在以下范围内共享(无耻地从文档中窃取):

*   `function`(默认)—夹具在测试结束时被破坏
*   `class` —夹具在课程的最后一次测试后被销毁
*   `module` —模块中最后一次测试后，夹具被销毁
*   `package` —包装内最后一次测试后，夹具被销毁
*   `session` —夹具在测试阶段结束时被销毁。

范围作为参数传递给 fixture 装饰器。

```
import pytest

@pytest.fixture(scope="session")
def test_something():
    # something
```

# 生产夹具

有时你可能有复杂的对象参与安装和拆卸。输入产量装置。

```
import pytest

@pytest.fixture
def read_file():
    f = open('hello_world.txt', 'r')
    yield f.readlines()
    f.close()  # teardown after yield

# alternatively, using `with`:
@pytest.fixture
def read_file():
    with open('hello_world.txt', 'r') as f:
        yield f.readlines()

def test_file(read_file):
    assert read_file = ['hello world']
```

`yield`向请求函数提供一个对象，并“暂停”夹具的执行。当请求函数完成时，夹具以相反的顺序被拆除(“恢复”夹具的执行)。

在 pytest v3 之前，`@pytest.yield_fixture`是要使用的装饰器。虽然我们现在已经过了那个时代，但是一些旧文档和堆栈溢出问题/答案可能是那个时代的。

# 嘲弄的

单元测试应该是原子的；嘲笑能做到这一点。您希望模拟对大多数(如果不是全部)外部函数的调用，尤其是当所述函数运行缓慢时。

pytest 中的模拟需要`pytest-mock`，您将在上面的*入门*小节中安装它。它提供了`mocker` fixture，可以模仿对象、方法和变量。

# 模拟变量

变量模拟主要用于模拟全局变量(在函数范围之外)。假设您有以下包含 Lambda 处理程序的文件:

```
# lambda_handler.pyis_cold_start = True
def handler():
    if is_cold_start:
        # do stuff
        is_cold_start = False
    # do more stuff
    return True  # arbitrary return value for illustration
```

你希望能够在`is_cold_start = False`的时候测试`handler()`；为此，您必须模仿`is_cold_start`。

```
# handler_test.pyimport pytest
import lambda_handler

def test_handler(mocker):
    mocker.patch.object(lambda_handler, 'is_cold_start', False)
    assert handler()
```

签名如下:

```
mocker.patch.object(
    module,      # this is NOT a string
    'variable',  # this IS a string
    value        # this is whatever
)
```

模块名称跟在导入名称后面。例如，如果`lambda_handler.py`在文件夹`src/`中，那么模块将是`src.lambda_handler`。

# 模拟功能

函数模拟有一些细微差别。这里有一系列的例子。

## 基础

```
# hello_world.pyimport os

def say_passphrase():
    passphrase = os.environ.get('PASSPHRASE')
    return passphrase or 'I need somebody (Help!)'
```

要模拟对`os.environ.get()`的调用:

```
# hello_world_test.pyimport pytest
from hello_world import say_passphrase

def test_passphrase(mocker):
    mocker.patch('hello_world.os.environ.get', return_value='hello world')
    assert say_passphrase() == 'hello world'
```

注意，第一个参数是被模拟为字符串的方法的完全限定名*。*

因为`hello_world.py`导入了`os`模块，所以模拟的目标是驻留在`hello_world`模块中的`os.environ.get()`的实例。或者，如果在`hello_world.py`中使用了`from os import environ`，那么完全限定名将会是`hello_world.environ.get`。

## 不同的返回值

如果在多个测试中需要相同的模仿，您可以将模仿者放在一个 fixture 中并返回模仿的对象。给定与上面相同的`hello_world.py`，下面是测试的样子:

```
# hello_world_test.pyimport pytest
from hello_world import say_passphrase

@pytest.fixture
def mock_getenv(mocker):
    return mocker.patch('hello_world.os.environ.get')

def test_passphrase_exists(mock_getenv):
    mock_getenv.return_value = 'hello world'
    assert say_passphrase() == 'hello world'

def test_passphrase_not_exists(mock_getenv):
    mock_getenv.return_value = None
    assert say_passphrase() == 'I need somebody (Help!)'
```

这里需要注意一些事情:

*   对`mocker.patch()` *的调用没有*包含`return_value`参数——这创建了一个通用的模拟对象，我们稍后可以操作它，然后 fixture 返回它
*   测试不直接请求`mocker`；相反，他们请求`mock_getenv`fixture——这为测试提供了模拟对象，因为测试不需要任何额外的模拟，所以`mocker`不需要单独传入
*   每个测试的`return_value`可以是唯一的；这是由于夹具行为(见上文)

## 动态返回值(通过 side_effect)

假设同一个方法在同一个函数中被调用了多次，使用了不同的参数。你怎么嘲笑这个？让我们来了解一下！

```
# hello_world.pyimport os

def say_passphrase():
    greeting = os.environ.get('GREETING')
    subject = os.environ.get('SUBJECT')
    return greeting + ' ' + subject
```

在这个例子中，`os.environ.get()`被调用了两次，每次都有不同的参数。我们需要一种方法来区分这两个电话。

```
# hello_world_test.pyimport pytest
from hello_world import say_passphrase

def getenv_side_effect(key, default = None):
    if key == 'GREETING':
        return 'hello'
    elif key == 'SUBJECT':
        return 'world'
    else:
        return default

@pytest.fixture
def mock_getenv(mocker):
    return mocker.patch('hello_world.os.environ.get', side_effect=getenv_side_effect)

def test_passphrase(mock_getenv):
    assert say_passphrase() == 'hello world'
```

`side_effect`参数允许您传递一个将被调用的函数来代替实际的函数。这允许您根据传入的参数指定不同的返回值。还要注意，副作用功能是*而不是*一个固定装置，也不是必需的。

## 更动态的返回值

如果您对同一个方法进行了多次调用，并且想要简洁地测试该方法的不同返回场景，那么该如何操作呢？

这个想法是将工厂设备与副作用功能结合起来，就像这样:

```
# hello_world.pyimport os

def say_passphrase():
    greeting = os.environ.get('GREETING', 'hola')
    subject = os.environ.get('SUBJECT', 'mundo')
    return greeting + ' ' + subject
```

这与前面的例子几乎相同，除了默认值现在被传递给`os.environ.get()`。

```
# hello_world_test.pyimport pytest
from hello_world import say_passphrase

def getenv_side_effect(key_exists: bool):
    def _getenv_side_effect(key, default = None):
        if key == 'GREETING':
            return 'hello' if key_exists else default
        elif key == 'SUBJECT':
            return 'world' if key_exists else default

    return _getenv_side_effect

@pytest.fixture
def mock_getenv(mocker):
    def _mock_getenv(key_exists):
        return mocker.patch('hello_world.os.environ.get',
                            side_effect=getenv_side_effect(key_exists))

    return _mock_getenv

def test_passphrase_exists(mock_getenv):
    mock_getenv(True)
    assert say_passphrase() == 'hello world'

def test_passphrase_not_exists(mock_getenv):
    mock_getenv(False)
    assert say_passphrase() == 'hola mundo'
```

或者，为了获得相同的效果，您可以为不同的场景使用多个副作用函数，并且在每个测试中加载不同的副作用。

# 模仿对象/类

模仿对象类似于模仿函数。这里有两个例子。

## 隐式类嘲讽

在这个例子中，您使用`requests`进行一些 API 调用。`requests.get()`返回一个`Response`对象，它有一些属性，其中一个是`status_code`。

```
# hello_world.pyimport requests

def check_status():
    response = requests.get('hello.world')  # this returns a Response class
    return response.status_code
```

在测试中，您模拟了`requests.get()`调用，并将其存储为模拟对象(`mock_response`)。然后，您可以链接模拟对象的属性，以便模拟对`status_code`属性的访问，而不显式模拟`Response`类。

```
# hello_world_test.pyimport pytest
from hello_world import check_status

def test_check_status(mocker):
    mock_response = mocker.patch('hello_world.requests.get')
    mock_response.return_value.status_code = 200
    assert check_status() == 200
```

## 显式类模仿

这里你传入了一个已经被实例化的对象。假设`name`属性属于`str`类型，并且`get_employer()`返回一个同样具有`name`属性的`Company`对象。

```
# hello_world.pyfrom people import Person

def greet(person: Person):
    name = person.name
    company = person.get_employer().name
    return f'hello, {name} from {company}'
```

在测试中，您必须显式模拟`Person`类，但是`Company`可以隐式模拟。

```
# hello_world_test.pyimport pytest
from hello_world import greet

def test_greet(mocker):
    mock_person = mocker.patch('hello_world.Person')  # explicitly mocked Person
    mock_person.name = 'Rich Fairbank'
    mock_person.get_employer.return_value.name = 'Capital One'  # implicitly mocked Company
    assert greet() == 'hello, Rich Fairbank from Capital One'
```

## 内部类嘲讽

在这个场景中，你试图模仿一个在被测试方法内部的对象。比方说，`greet()`是某个类的一部分(为了简单起见，省略了它),它创建自己的`Person`实例来问候你。为了举例，假设为初始化`Person`而传入的值是静态的(尽管实际上会从其他地方检索)。

```
# hello_world.pyfrom people import Person

def greet():
    person = Person("Dumbledore", "Hogwarts")
    name = person.name
    company = person.get_employer().name
    return f'{name} from {company} says "Greetings."'
```

在测试中，您将不得不显式地模拟内部的`Person`方法，与您将它作为参数传入时略有不同。

```
# hello_world_test.pyimport pytest
from hello_world import greet

def test_greet(mocker):
    mock_person = mocker.patch('hello_world.Person')  # the overall class is mocked the same way
    mock_person().name = 'Dumbledore'  # however, note the parentheses here...
    mock_person().get_employer.return_value.name = 'Hogwarts'  # and here

    assert greet() == 'Dumbledore from Hogwarts says "Greetings."'
```

# 标记

那是用一个*一个*做标记。标记是一种标记/标注测试的方式，这样您就可以有选择地运行测试的子集(例如，您希望只运行您正在处理的测试)。

要标记某样东西，只需用`@pytest.mark.<your mark>`装饰即可，例如:

```
import pytest

@pytest.mark.wip
def test_something():
    # blah
```

您还必须在`conftest.py`中向 pytest 注册您的标记:

```
# conftest.pydef pytest_configure(config):
    config.addinivalue_line("markers", "wip: mark test that is in progress")
```

您可以通过添加另一个`config.addinivalue_line()`来添加额外的标记。

要运行标记，请使用`pytest -m <mark1> <mark2> ...`

# 参数化

有时，您希望用不同的输入集运行相同的测试。输入参数化！

```
# hello_world.pydef greet(name: str, is_casual: bool, use_exclamation: bool):
    greeting = 'sup' if is_casual else 'salutations,'
    ending = '!' if use_exclamation else ''
    return f'{greeting} {name}{ending}'
```

您希望能够轻松地测试不同的布尔组合，而无需多次重写基本相同的测试。

```
# hello_world_test.pyfrom hello_world import greet

@pytest.mark.parameterize('is_casual', 'use_exclamation',
                          [(True, True), (True, False), (False, True), (False, False)])
def test_greet(is_casual, use_exclamation):
    greeting = 'sup' if is_casual else 'salutations,'
    ending = '!' if use_exclamation else ''
    assert greet('Rich', is_casual, use_exclamation) == f'{greeting} Rich{ending}'
```

通过参数化，您可以提供要参数化的参数和要使用的值的列表。pytest 将对提供的每组参数运行一次测试。

# 那都是乡亲们！

如果你已经做到了这一步，你就是一名骑警，我希望我能帮助你满足你的测试需求。如果没有，请联系或评论你想实现的目标，我会看一看的！另外，如果你注意到我提供的信息有任何问题，请告诉我。我最不想做的事就是传播错误的信息。

嘿，**理查德石**在这里。我是一名软件工程师，在普渡大学学习化学工程。我热爱技术，我在那个领域写一些有趣和令人兴奋的话题。有时候我也会写一些其他的东西。你可以在[的 Twitterverse](https://twitter.com/heyrichardshi) 中找到我这个不懂社交媒体的自己，或者你可以在 [GitHub](https://github.com/heyrichardshi) 上查看我的一些项目。如果你喜欢这首曲子，请考虑给我买一份拉面来支持我！