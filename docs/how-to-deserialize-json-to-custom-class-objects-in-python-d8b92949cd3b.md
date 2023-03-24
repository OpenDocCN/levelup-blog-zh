# 如何在 Python 中将 JSON 反序列化为自定义类对象

> 原文：<https://levelup.gitconnected.com/how-to-deserialize-json-to-custom-class-objects-in-python-d8b92949cd3b>

将 JSON 反序列化为定制类对象只是需要一些小心

![](img/ceafecaf9d085d1e14689acb9e1217ef.png)

卡罗琳娜·加西亚·塔维森在 [Unsplash](https://unsplash.com/s/photos/paper-origami?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

[1。自定义类定义](#130e)
[2。json.loads()带 object_hook](#52ef)
[3。嵌套对象](#d8c6)
[4。jsonpickle](#1b1e)
[结论](#9f83)

这是关于[如何在 Python 中把一个类对象序列化为 JSON 的后续文章](https://changsin.medium.com/how-to-serialize-a-class-object-to-json-in-python-849697a0cd3#2a21)以结束 Python 中序列化和反序列化问题的循环。序列化是将对象转换成字符串或文件，从保存的字符串中获取原始对象是反序列化。如果您了解序列化是如何完成的，那么您就几乎可以逆转反序列化的过程。然而，通常情况下，细节决定成败。让我们先从自定义类的定义开始。

# 1.自定义类别定义

为了使插图与前一篇文章相匹配，让我们首先使用同一个简单的标签类

```
class Label:
    def __init__(self, label, x, y, width, height):
        self.label = label
        self.x = x
        self.y = y
        self.width = width
        self.height = height
```

使用 json.dumps()的序列化字符串是:

```
{"label": "person", "x": 10, "y": 10, "width": 4, "height": 10}
```

# 2.json.loads()带有 object_hook

默认的反序列化方法是 json.loads()，它将一个字符串作为输入，并输出一个 json 字典对象。要将 dictionary 对象转换为自定义类对象，需要编写一个反序列化方法。最简单的方法是向类本身添加一个静态方法。具有 serialize (to_json)和 deserialize (from_json)方法的整个类应该如下所示:

有了反序列化方法，我们可以调用 json.loads 方法，并将 Label 类的新反序列化方法指定为 object_hook:

loads()方法不是返回一个 JSON dictionary 对象，而是按照预期生成一个适当的 Label 对象。

# 3.嵌套对象

到目前为止一切顺利。现在，让我们看看在有嵌套对象的更复杂的情况下，您需要做些什么。ImageLabelCollection 类包含一个 Label 对象列表，作为类变量“bboxes”的一部分。下面用序列化方法 to_json()复制了类的定义。

生成的 json 字符串示例如下:

序列化的 ImageLabelCollection 对象

要将字符串反序列化为类对象，您需要编写一个自定义方法来构造对象。您可以向 ImageLabelCollection 添加一个静态方法，在该方法中，您可以从加载的 JSON 字典中构造标签对象，然后将它们作为一个列表分配给类变量 bbox。

这个过程有点繁琐，但有一个更好的方法。您可以编写 from_json()静态方法，在该方法中您可以处理逐步构造类对象。

当使用上面的 from_json()方法作为对象挂钩调用 json.loads()时，它将在自下而上构建对象时迭代调用该方法。

问题是这个方法被多次调用，每次都调用 dictionary 对象的不同部分，所以您必须添加逻辑来处理如何根据类型将 dictionary 对象转换成适当的类对象。在上面的例子中，我使用某些键的存在作为线索来决定应该调用哪个构造函数。这仍然有点乏味，但比自己编写整个代码的原始解决方案要好。

# 4.jsonpickle

最后一种反序列化方法是使用名为 jsonpickle 的外部库。您可以通过以下方式轻松安装它:

```
pip install jsonpickle
```

用法很简单。调用 jsonpickle.encode()进行序列化，调用 decode()进行反序列化。

序列化的结果与我们最初的 JSON 字符串略有不同:

```
{"py/object": "__main__.ImageLabelCollection", "version": 1, "type": "bounding-box-labels", "bboxes": {"20210715_111300 16.jpg": [{"py/object": "__main__.Label", "label": "StabilityOff", "x": 1, "y": 1025, "width": 553, "height": 29}, {"py/object": "__main__.Label", "label": "StabilityOn", "x": 1, "y": 964, "width": 563, "height": 30}]}}
```

它添加了一些额外的注释信息，以便以后进行反序列化。

# 结论

鉴于 jsonpickle 的简单性，对于一般用途来说，这应该是最容易序列化的方法。然而，由于 jsonpickle 在 JSON 字符串中的附加属性，它的局限性在于，您需要使用 jsonpickle 的 decode()方法来取回对象。一般来说，这应该不是问题，但是如果您有一些需要自己处理的自定义对象，您可以使用建议的自定义反序列化方法。整个代码示例在这里共享[。感谢阅读。](https://github.com/changsin/Medium/blob/main/notebooks/JSON_Deserialization.ipynb)