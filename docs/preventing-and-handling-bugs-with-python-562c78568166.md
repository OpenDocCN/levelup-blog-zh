# 用 Python 预防和处理 bug

> 原文：<https://levelup.gitconnected.com/preventing-and-handling-bugs-with-python-562c78568166>

![](img/c739f053499ca45528134da0fd5f538f.png)

[大卫·克洛德](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 是一种方便的语言，通常用于脚本编写、数据科学和 web 开发。

在本文中，我们将看看如何用 Python 来预防和处理错误。

# 引发异常

当我们用`raise`关键字遇到一些意外情况时，我们可以抛出异常。

例如，我们可以编写以下代码来引发异常:

```
print('What is your name?')
name = input()
if name != 'Joe':
  raise Exception('Wrong name')
```

在上面的代码中，如果输入的`name`不是`'Joe'`，我们会引发一个异常。

我们使用了`raise`关键字和`Exception`构造函数。

由于我们没有捕捉到异常，如果输入除了`'Joe'`之外的任何内容，程序都会崩溃。

为了优雅地处理异常，我们可以将引发异常的代码包装在一个`try...except`块中，如下所示:

```
print('What is your name?')
name = input()
try:
  if name != 'Joe':
    raise Exception('Wrong name')
except Exception as err:
  print(str(err))
```

上面的代码捕捉异常并打印出来自`err`对象的错误消息。

# 获取字符串形式的回溯

我们可以将堆栈跟踪记录为一个字符串，它列出了在异常出现之前运行的代码，并通过使用`traceback`库将其写入一个文件。

为此，我们可以使用`traceback`模块获取字符串形式的堆栈跟踪。

在下面的示例中，我们将捕获错误，获取错误的堆栈跟踪，并将其写入文件，如下所示:

```
import tracebackprint('What is your name?')
name = input()
try:
  if name != 'Joe':
    raise Exception('Wrong name')
except Exception as err:
  error_file = open('error_info.txt', 'w')
  error_file.write(traceback.format_exc())
  error_file.close()
  print(str(err))
```

在上面的代码中，我们使用了`traceback.format_exec()`函数来获取字符串形式的堆栈跟踪。

然后我们就用`write`写了。

# 断言

在 Python 中，我们可以使用断言作为健全性检查来确保我们的代码没有出错。

我们可以用带条件的`assert`语句做一个断言。

如果断言失败，如果在条件后添加错误消息字符串，则引发`AssertionError`。

例如，我们可以写:

```
print('What is your name?')
name = input()
try:
  assert name == 'Joe', 'Wrong name'
except AssertionError as err:
  print(str(err))
```

在上面的代码中，我们有:

```
assert name == 'Joe', 'Wrong name'
```

哪个检查是`name`哪个是`'Joe'`。否则，通过`'Wrong name'`消息引发`AssertionError`。

我们需要消息来引发异常。

然后我们像处理其他类型的异常一样捕捉`AssertionError`。

# 记录

我们可以用`logging`模块使日志记录变得容易。

要将日志添加到我们的应用程序中，我们可以用一行代码来配置日志。

例如，我们可以编写以下内容:

```
import logging
logging.basicConfig(level=logging.DEBUG, format=' %(asctime)s -  %(levelname)s -  %(message)s')
logging.debug('Start of program')print('What is your name?')
name = input()
try:
  assert name == 'Joe', 'Wrong name'
except AssertionError as err:
  logging.debug(str(err))
```

上面的代码导入了`logging`模块，为日志记录设置配置。

然后我们调用`logging.debug`来记录应用程序的调试信息。

例如，我们在程序启动时添加一个，在程序崩溃时添加另一个。

格式字符串`%(asctime)s`是事情发生的时间，`%(levelname)s`是调试级别名称，`%(message)s`是消息。

其他格式字符串包括:

*   `%(created)f` —创建日志记录的时间
*   `%(filename)s` —代码的文件名
*   `%(funcName)s` —正在运行的功能
*   `%(levelno)s` —记录级别
*   `%(lineno)d` —行号
*   `%(module)s` —模块名称
*   `%(msecs)d` —创建日志记录时的毫秒部分
*   `%(pathname)s` —正在运行的文件的路径
*   `%(process)d` —进程 ID
*   `%(processName)s` —流程名称
*   `%(relativeCreated)d` —创建日志记录的时间，以毫秒为单位
*   `%(thread)d` —螺纹 ID
*   `%(threadName)s` —线程名称

![](img/6ebf8bc45414973dca714eee6b91b3aa.png)

照片由[蜜牙](https://unsplash.com/@honeyfangs?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 日志记录级别

以下是可能的日志记录级别:

*   调试—最低级别。用于小细节，用`logging.debug()`记录
*   信息—记录一般事件的信息，用`logging.info()`记录
*   警告—指出潜在的问题，用`logging.warning()`记录
*   错误—记录错误，用`logging.error()`记录
*   CRITICAL —日志记录的最高级别，用于指示致命错误，用`logging.critical()`记录

# 禁用日志记录

我们可以通过调用`logging.disable`来禁用日志记录，并将日志记录的级别作为参数传入。

例如，如果我们有:

```
import logging
logging.basicConfig(level=logging.DEBUG, format=' %(asctime)s -  %(levelname)s -  %(message)s')
logging.debug('Start of program')
print('What is your name?')
name = input()
try:
  assert name == 'Joe', 'Wrong name'
except AssertionError as err:
  logging.debug(str(err))
  logging.disable(logging.CRITICAL)
  logging.critical('Critical error')
```

然后，我们将不会在屏幕上看到`Critical error`，因为我们禁用了临界日志记录级别。

# 记录到文件

我们可以传入一个`filename`参数来将我们的错误记录到一个文件中。例如，我们可以写:

```
import logging
logging.basicConfig(filename='log.txt', level=logging.DEBUG, format=' %(asctime)s -  %(levelname)s -  %(message)s')
logging.debug('Start of program')
print('What is your name?')
name = input()
try:
  assert name == 'Joe', 'Wrong name'
except AssertionError as err:
  logging.debug(str(err))
```

在上面的代码中，我们将消息记录到`log.txt`中，而不是在屏幕上。这更有帮助，因为我们可以稍后再看，而且消息不会塞满屏幕。

# 结论

当遇到意想不到的事情时，我们可以抛出异常。然后我们可以用`try...except`滑车抓住它们。

此外，我们可以使用带有错误消息的`assert`语句来检查预期的条件，如果遇到不期望的情况，就引发`AssertionError`。

最后，我们可以用`logging`和`traceback`模块记录事情，帮助我们调试错误。