# 用 Python 实现 Singleton

> 原文：<https://levelup.gitconnected.com/implement-singleton-in-python-ebfb1bc81887>

## 如何用 Python 构建 Singleton

Singleton 是一个只有一个实例的类，也就是说，重复实例化的类只会返回同一个实例。当你想在类的所有实例化中设置管理资源的一致性时，或者一些实例化非常昂贵，你不想在系统的不同部分中一次又一次地重复花费，那么你可以使用 Singleton。

单例用例的例子包括线程池，其中需要管理所有实例的共享资源、日志系统等。

# 如何用 Python 实现？

在其他语言中，例如 Jave，它允许您创建一个私有构造函数，这是一个永远无法从外部访问的构造函数，以迫使您使用其他方法来实例化该类。但在 Python 中却不是这样，Python 中的构造函数总是可公开访问的，所以我们需要另一个变量来控制实例化。我们先来看看代码。

首先，在 __init__ 函数中，我们添加了一个额外的参数`instance`。如果用户试图使用

```
a = Singleton(object())
```

它将遇到断言错误并得到消息，

```
"objects must be created using Singleton.get_instance"
```

所有类的创建都必须使用`Singleton.get_instance`方法，当它被重复创建时，将返回先前创建并存储在`__unique_instance`中的实例。

这里我们使用装饰者`classmethod`而不是`staticmethod`有两个原因，

1.  我们需要访问类变量`__unique_instance`。
2.  我们通过方法返回类实例化，所以按照惯例，`classmethod`非常适合这种情况。

现在如果你运行代码

```
sg1 = Singleton.get_instance()
sg2 = Singleton.get_instance()
sg3 = Singleton.get_instance()
```

你会得到，

```
first time instantiation
instance created before
instance created before
```

该实例只会被实例化一次。