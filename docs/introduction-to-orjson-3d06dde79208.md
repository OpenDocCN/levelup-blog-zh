# orjson 简介

> 原文：<https://levelup.gitconnected.com/introduction-to-orjson-3d06dde79208>

一个替代的 Python JSON 库，支持数据类、日期时间和 numpy

![](img/a84412bdb7f9f52407aacb3a2999316c.png)

由[杯先生/杨奇煜·巴勒](https://unsplash.com/@iammrcup?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/file?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

今天的主题是优化 Python 应用程序中的 JSON 序列化和反序列化。如果您经常使用内置的`json`模块，并且希望提高应用程序的性能或每秒事务处理量，那么您应该考虑使用`orjson`模块。根据[官方文件](https://github.com/ijl/orjson) , `orjson`是

> “…一个快速而正确的 Python JSON 库，本机支持数据类、日期时间和数字”

让我们来看看与其他 Python JSON 库相比的优缺点。

*   序列化数据类
*   本机序列化日期时间对象，numpy.ndarry，UUID
*   序列化为字节而不是字符串
*   序列化字符串而不将 unicode 转义为 ASCII
*   具有严格的 UTF-8 和 JSON 格式一致性
*   序列化和反序列化的速度要快得多
*   不提供`load()`或`dump()`函数来读取/写入类似文件的对象

让我们继续下一部分，开始安装必要的模块

# 设置

## 基本安装

强烈建议您在继续安装之前创建一个虚拟环境。您可以通过`pip install`轻松安装`orjson`。在您的终端上运行以下命令:

```
pip install orjson
```

## 升级现有包

如果您已经有一个现有的`orjson`包，并且想要升级它，运行下面的命令

```
pip install -U orjson
```

# 履行

## 导入

在 Python 文件的顶部添加以下导入声明。

```
import orjson
```

## 序列化和反序列化

让我们看看下面的例子，它序列化和反序列化字符串、列表和字典。

```
data = {
"emoji_tears": "😂",
"emoji_clock": "⏰",
"integer": 123,
"float": 10.4,
"boolean": False,
"list": ["element1", "element2"],
"dict": {"key1": "value1", "key2": "value2"},
"russian": "Привет",
"chinese": "您好",
"japanese": "こんにちは"
}# serialize, returns byte instead of string
json_byte = orjson.dumps(data)# de-serialize
orjson.loads(json_byte)
```

对于序列化，可以指定以下输入参数:

*   `default` —返回支持类型的可调用函数。可用于序列化子类或任意类型。此外，您可以通过引发一个像`TypeError`这样的异常来执行一个规则来处理不支持的日期类型。
*   `option` —通过`orjson`中整数常量修改数据的序列化方式。

## 默认

让我们来看看下面的例子，如果输入数据包含`Decimal`数据类型，这个例子会产生一个错误。

```
import decimalorjson.dumps(decimal.Decimal("3.141592653"))
```

运行它时，您将得到以下输出

```
TypeError: Type is not JSON serializable. decimal.Decimal
```

为了支持`Decimal`序列化，您需要创建一个可调用的函数或 lambda，并将其作为`default`参数传递。

```
import decimaldef default(obj):
    if isinstance(obj, decimal.Decimal):
        return str(obj)
    raise TypeErrororjson.dumps(decimal.Decimal("3.141592653"), default=default)
```

## [计]选项

`option`参数决定序列化方法。让我们来看看下面序列化`datetime`对象和`numpy.array`的代码。

```
import orjson
import datetime
import numpy as npdata = {
"datetime": datetime.datetime.now(),
"numpy": np.array([[1, 2], [3, 4]])
}json_byte = orjson.dumps(data, option=orjson.OPT_NAIVE_UTC | orjson.OPT_SERIALIZE_NUMPY)orjson.loads(json_byte)
```

下面的列表包含了一些最常用的整型常量:

*   `OPT_APPEND_NEWLINE` —将`\n`追加到输出中。
*   `OPT_INDENT_2` —漂亮的打印输出，缩进两个空格。
*   `OPT_NAIVE_UTC` —将不带`tzinfo`的`datetime.datetime`对象序列化为 UTC。这对设置了`tzinfo`的`datetime.datetime`对象没有影响。
*   `OPT_NON_STR_KEYS` —序列化字符串以外类型的字典键。对于字符串键，此选项比默认选项慢。
*   `OPT_OMIT_MICROSECONDS` —不序列化`datetime.datetime`和`datetime.time`实例上的微秒字段。
*   `OPT_SERIALIZE_NUMPY` —序列化`numpy.ndarray`实例。
*   `OPT_SORT_KEYS` —按排序顺序对字典键进行序列化。默认情况下，以未指定的顺序进行序列化。
*   `OPT_STRICT_INTEGER` —对整数实施 53 位限制，而不是标准的 64 位。Q

## 数据类

供您参考，`orjson`将`dataclass`序列化为映射，每个属性按照类定义中给定的顺序序列化。让我们看看下面的例子:

```
import dataclasses, orjson, typing@dataclasses.dataclass
class Person:
    id: int
    name: str
    status: bool = dataclasses.field(default=True)@dataclasses.dataclass
class Class:
    id: int
    name: str
    students: typing.List[Person]data = Class(1, "Class A", [Person(1, "John Doe", False), Person(2, "Mary Sue")])json_byte = orjson.dumps(data)orjson.loads(json_byte)
```

当您打印出`json_byte`变量时，您应该得到以下输出。

```
b'{"id":1,"name":"Class A","students":[{"id":1,"name":"John Doe","status":false},{"id":2,"name":"Mary Sue","status":true}]}'
```

## 文件读/写

您可以通过`write()`功能轻松地将字节内容正常保存到文件中。但是，您需要在模式参数中包含`b`。让我们看看下面的代码片段。

```
with open("example.json", "wb") as f:
    f.write(orjson.dumps(data))
```

同样，从文件中读取数据也很简单，如下所示:

```
with open("example.json", "rb") as f:
    json_data = orjson.loads(f.read())
```

# 结论

让我们回顾一下今天所学的内容。

我们首先简要介绍了`orjson`模块。与 Python JSON 库的其他部分相比，我们了解了这个模块的优点和缺点。

接下来，我们继续通过`pip install`安装它。还有一个通过`-U`输入升级现有包的选项。

此外，我们通过 loads 和 dumps 函数尝试了不同的序列化和反序列化用例。为了序列化，`orjson`带有`default`和`option`参数来控制进程。

最后，我们测试了如何将字节数据保存到文件中，并将其加载到 Python 应用程序中。

感谢你阅读这篇文章。希望在下一篇文章中再见到你！

# 参考

1.  [orjson Github 页面](https://github.com/ijl/orjson)