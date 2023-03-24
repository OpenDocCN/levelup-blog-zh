# 使用 Python 构建成熟的 CLI 高级 Python

> 原文：<https://levelup.gitconnected.com/advanced-python-building-a-full-fledged-cli-using-python-f11e1083cc91>

![](img/cce7d83ba2f50d607425899138d026f4.png)

[https://Batman . fandom . com/wiki/The _ Batman _(TV _ series)？file = qtyuk 6 bhyhxeumivi 6 kjzokm 2 w 4 . jpg](https://batman.fandom.com/wiki/The_Batman_(TV_series)?file=QTYUK6BHYHXEuMiVI6KjZOkm2w4.jpg)

要学习编程语言的高级特性，您应该尝试用该语言构建一些实用工具。构建命令行界面或 CLI 工具，不仅能让您深入了解编程语言。它还教你如何与用户以及操作系统进行交互。在某些情况下，您可能会学习如何读取文件和使用这些文件。我总是试图构建一些通用的实用工具，以学习任何编程语言。上一次当我试图学习 [Deno](https://deno.land/) 时，我写了一篇文章[通过实例构建一个成熟的 CLI 工具| Deno](/build-a-cli-tool-deno-by-example-79d39f25eb0a)。所以我决定用 Python 来构建一个类似的工具。

Python 是最常用的编程语言之一。但是，它主要用于编写脚本和数据提取实用程序。编写一个完整的命令行工具对开发人员来说有点困难或者不太了解。的确，以前 Python 没有很好的支持来构建成熟的应用程序。但是随着像[设置工具](https://pypi.org/project/setuptools/)、 [venv](https://docs.python.org/3/library/venv.html) 这样的工具的引入。现在编写和维护 CLI 应用程序变得容易多了。

> 在本文中，我们将学习如何创建 CLI 应用程序。在这个应用程序中，我们将读取文件、读取 stdin 并解析它。我们将构建 [JQ 工具](https://stedolan.github.io/jq/)的部分克隆。

## 先决条件

1.  Python 3.7 及以上版本
2.  Python 基础知识([列表](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions)，命令行参数， [json](https://docs.python.org/3/library/json.html) ，[字典](https://docs.python.org/3/tutorial/datastructures.html#dictionaries))

**注意:**本文不适合初学者。如果你是 python 的新手，你应该看看另一篇文章[使用 Python](https://medium.com/geekculture/building-a-cli-tool-love-calculator-flames-using-python-39419e7f1f64) 构建 CLI 工具爱情计算器(FLAMES)。

## 目录

1.  设置**虚拟环境** ( **venv)**
2.  设置**入口点**
3.  解析**命令行参数**
4.  读取并解析 **JSON 文件**
5.  **从另一个命令到脚本的输入/管道数据**
6.  安全评估 JSON 数据
7.  试用 **JQ** 工具

## 1.设置**虚拟环境**

以前用 python 管理依赖关系很繁琐。然而在引入了**虚拟环境** ( **venv)** 之后，现在创建一个开发环境是非常容易的。遵循以下命令:

```
md jq-cli
cd jq-cli## Create Virtual Env
python -m venv venv## Activate Virtual Env
source venv/bin/activate
```

## 2.设置**入口点**

用 Python 创建一个工作脚本有点乏味。 [Setuptools](https://pypi.org/project/setuptools/) 帮助创建一个运行命令工具。为此，我们需要创建一个 **setup.py** 文件。要了解更多信息，请访问[https://setup tools . pypa . io](https://setuptools.pypa.io/en/latest/)。

**注意:**以防你的 python 没有 setuptools。运行`pip install **—** upgrade setuptools`

```
**## jq-cli/setup.py**"""Package configuration."""
from setuptools import find_packages, setupsetup(
    name="jq",
    version="0.1",
    packages=find_packages(where="src"),
    package_dir={"": "src"},
    install_requires=[],
    ## Entry point for command line and function to be executed
    entry_points={
        **'console_scripts': ['jq=main:main'],**
    }
)
```

在上面的安装文件中，我们需要一些常见的包配置，如名称、版本。**入口点**是定义命令行工具的入口点。jq 将是最终的脚本/命令名。`**main:main**`告诉 **setuptool** 使用主包(本例中为 main.py)作为入口脚本，使用主函数作为要执行的入口函数。

让我们创建一个简单的 hello world 脚本( **src/main.py)** 。

```
## Create a file **src/main.py,** enter below codedef main():
    print("Welcome to Command Line Tool")
```

一旦创建了运行脚本。我们需要运行以下命令来创建 CLI 工具。

```
pip install -e .  --no-deps
python setup.py develop
```

一旦你运行了上面的命令，你会看到一个文件名 **jq** 将被创建在 **bin** 文件夹 **(venv/bin/jq)** 中。这是 **jq** 工具的开发版本。你可以运行测试脚本。

```
./venv/bin/jq**## Output:**
Welcome to Command Line Tool
```

## 3.解析**命令行参数**

我们的 **jq** 工具支持解析带有要解析的键(查询)的 JSON 文件。为此，我们需要听取用户的意见。在 python 中获得命令行参数非常容易。在 **main.py** 中添加以下行。

```
import sys**key_to_parse = sys.argv[1]
json_file_path = sys.argv[2]**print(key_to_parse, json_file_path)def main():
    print("Welcome to Command Line Tool")
```

**运行和测试:** `./venv/bin/jq ".actor.movies[2]" user.json`

## 4.读取并解析 **JSON 文件**

一旦我们获得了 JSON 文件的路径位置，我们需要读取一个 JSON 文件并将其转换成一个字典。为了加载和转换字典，我们可以使用 python 中的 **json** 模块。

```
**import json** import syskey_to_parse = sys.argv[1]
json_file_path = sys.argv[2]def main():
    # Read JSON file
    json_file = open(sys.argv[2])
    json_str = "" # Read JSON file line by line
    for line in json_file:
        json_str += line # Parse json string to dictionary
    **json_dict = json.loads(json_str)** print(json_dict)
```

为了测试上面的脚本，我们需要创建一个测试 JSON。让我们创建一个 **"test/user.json"** 文件。

```
{
  "id": 1,
  "version": "1.0.1",
  "contributors": [
    "deepak",
    "gary"
  ],
  "actor": {
    "name": "Tom Cruise",
    "age": 56,
    "Born At": "Syracuse, NY",
    "Birthdate": "July 3 1962",
    "movies": [
      "Top Gun",
      "Mission: Impossible",
      "Oblivion"
    ],
    "photo": "[https://jsonformatter.org/img/tom-cruise.jpg](https://jsonformatter.org/img/tom-cruise.jpg)"
  }
}
```

**运行和测试:** `./venv/bin/jq ".actor.movies[2]" test/user.json`

## **5。从另一个命令到脚本的输入/管道数据**

我们也可以使用`cat`命令读取文件。CLI 工具支持来自另一个命令的管道数据的输入是一个很好的做法。为了支持这一点，我们需要读取 **stdin** 数据流。我们可以使用 **sys** 模块获取 **stdin** 流数据。

```
import sys
import jsonkey_to_parse = sys.argv[1]
# Remove -- json_file_path = sys.argv[2]def main():
    # Read JSON file
    **json_file = open(sys.argv[2]) if len(sys.argv) > 2 else sys.stdin**
    json_str = "" # Rest of the code
```

**运行和测试:** `cat test/user.json | ./venv/bin/jq ".actor.movies[2]"`

## 6.安全评估 JSON 数据

根据我们的 JQ 需求，我们需要基于**用户查询**解析 JSON。为了安全地评估查询，我们需要一个简单的实用函数来解析查询并整理字典 JSON 数据。

```
import json
import sys
**import re**key_to_parse = sys.argv[1]**reg = "(\.)|(\[(\d)\])"**def safeEval(key, obj):
    keys = re.findall(r"(\.(\w+))|(\[(\d+)\])", key)
    for (_, m1, __, m2) in keys:
        obj = obj[m1] if m1 in obj else None if m1 else obj[int(m2)]
        if obj == None:
            break
    return objdef main():
    ## Rest of the code ## Safe evaluate dictionary and parse.
    **print(safeEval(key_to_parse, json_dict) or "")**
```

在上面的代码中，我们使用 **re** 模块来解析查询并获取所有键。一旦我们得到了所有的键，我们就遍历键并解析嵌套的字典。

## 7.试试 JQ

现在我们的工具已经可以试用了，您可以运行下面的命令来评估对 user.json 文件的查询。

```
venv/bin/jq ".id" "test/user.json"
venv/bin/jq ".actor.movies[2]" "test/user.json"
venv/bin/jq ".actor.movies" "test/user.json"
venv/bin/jq ".actor" "test/user.json"
venv/bin/jq ".actor.doesnotexits" "test/user.json"
```

**输出:**

```
1
Oblivion
['Top Gun', 'Mission: Impossible', 'Oblivion']
{'name': 'Tom Cruise', 'age': 56, 'Born At': 'Syracuse, NY', 'Birthdate': 'July 3 1962', 'movies': ['Top Gun', 'Mission: Impossible', 'Oblivion'], 'photo': '[https://jsonformatter.org/img/tom-cruise.jpg'](https://jsonformatter.org/img/tom-cruise.jpg')}
```

正如你所看到的，我们的工具运行良好。您可以压缩 **venv** 文件夹并分发它。所有功能都将如上所述工作。还可以将环境变量设置为扩展路径 venv/bin/ folder。那么你可以在任何地方使用 JQ。该 **jq** 工具还通过管道支持来自 **curl** 命令的数据。

```
curl --silent [https://jsonplaceholder.typicode.com/users/1](https://jsonplaceholder.typicode.com/users/1) | venv/bin/jq ".address.street"
```

你可以在 Github 上找到工作代码。

[](https://github.com/deepakshrma/jq-cli-py) [## GitHub - deepakshrma/jq-cli-py

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/deepakshrma/jq-cli-py)