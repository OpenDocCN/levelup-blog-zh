# Python 中的伪随机字符串

> 原文：<https://levelup.gitconnected.com/pseudo-random-strings-in-python-866b73c2fa22>

## 了解如何生成它们

在这篇文章中，我将展示如何用 Python 生成伪随机字符串。我们开始吧！

![](img/4d0b3fd63d21146ce0669410a086ae16.png)

[图片来自 Pixabay 中的免费照片](https://pixabay.com/photos/woodtype-printing-font-letterpress-846089/)

# “随机”模块

在第一种方法中，我们将使用[随机模块](https://docs.python.org/3/library/random.html)。这个模块允许我们创建伪随机数或序列，并且可以用于生成非安全相关的字符串。正如我们将在下一节看到的，在这种情况下，我们应该使用[秘密模块](https://docs.python.org/3/library/secrets.html)。

让我们从这种方法开始。首先，我们需要定义希望包含在字符串中的字符类型(大写字母、小写字母、数字等)。).我们可以通过使用字符串模块来做到这一点，它为每个字符集提供了一组常量。我们可以在一个“字母表”变量中加入两个或多个这样的常数。在我们的示例中，我们将使用字母(大写和小写)和数字:

```
import stringalphabet = string.ascii_letters + string.digits
```

每个常量都是一个字符串，所以“alphabet”保存了连接这些常量的结果。我们现在可以定义所需字符串的长度“n ”,将其作为参数传递给我们的函数，并调用 [random.choice()](https://docs.python.org/3/library/random.html#random.choice) n 次:

```
import random
import stringdef rand(n):
  alphabet = string.ascii_letters + string.digits
  return ''.join(random.choice(alphabet) for i in range(n))
```

如果您运行这个函数，您将看到它生成了一个具有定义的字母表和长度的随机字符串。

# “秘密”模块

如果你需要在一个安全相关的场景中创建一个随机字符串(例如，密码重置)，重要的是你使用[秘密模块](https://docs.python.org/3/library/secrets.html)而不是[随机模块](https://docs.python.org/3/library/random.html)。这个模块可以从 Python 3.6 中获得，在 [PEP 506](https://www.python.org/dev/peps/pep-0506/) 中被建议提供一个开箱即用的解决方案，在与安全相关的场景中使用加密强大的原语，因为很多人使用[随机模块](https://docs.python.org/3/library/random.html)来生成临时密码(参见[其基本原理在这里](https://www.python.org/dev/peps/pep-0506/#rationale))。

让我们看看如何使用[秘密模块](https://docs.python.org/3/library/secrets.html)用指定的字母表实现一个长度为 n 的随机字符串。您会发现方法实际上是相同的:

```
import secrets
import stringdef sec(n):
  alphabet = string.ascii_letters + string.digits
  return ''.join(secrets.choice(alphabet) for i in range(n))
```

就是这样！您现在已经学会了如何创建一个伪随机字符串，现在您只需要明智地选择模块！