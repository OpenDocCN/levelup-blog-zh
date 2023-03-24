# 如何使用 Python 中的 Pydantic 模型的自定义验证器来验证数据

> 原文：<https://levelup.gitconnected.com/how-to-validate-your-data-with-custom-validators-of-pydantic-models-743561a4ab53>

## 学习使用 Pydantic 以灵活的方式验证数据

![](img/1a7a78bf15afaa5dfbc0ee46c611a08d.png)

[图片由 jackmac34 在 Pixabay](https://pixabay.com/photos/tampons-authorisation-validation-1143489/)

*Pydantic* 是一个流行的 Python 库，用于使用类型注释进行[数据验证](https://lynn-kwong.medium.com/python-typing-and-validation-with-mypy-and-pydantic-a2563d67e6d)和[设置管理](https://lynn-kwong.medium.com/how-to-use-pydantic-to-read-environment-variables-and-secret-files-in-python-8a6b8c56381c)。与对 Python 代码进行静态类型检查的 [*mypy*](http://mypy-lang.org/) 不同， [*pydantic*](https://pydantic-docs.helpmanual.io/) 在运行时强制执行类型提示，并在数据无效时提供用户友好的错误。当适当地添加类型注释时， *pydantic* 通常可以很好地完成开箱即用的数据验证。然而，有时使用默认的验证器是不够的，您需要创建自定义的验证器来实现一些特殊的数据逻辑进行数据验证。在本帖中，我们将用简单的例子介绍一些常用的自定义验证器，它们可以扩展以解决复杂的实际问题。

## 创建一个基本的 pydantic 模型

首先，让我们创建一个标准的 *pydantic* 模型，并使用默认的验证器来验证和规范化我们的数据。一个 *pydantic* 模型只是一个从 *pydantic* 的`BaseModel`继承而来的类。模型的属性是用类型注释声明的，这些注释将用于数据验证和转换。注意你需要先用 pip 安装*活塞*。

注意，在这篇文章中使用了 Python 3.10，这就是为什么我们可以使用内置的`list`而不是来自`typing`库的`List`来注释一个列表。如果本地没有安装 Python 3.10，可以使用[***conda***](https://lynn-kwong.medium.com/how-to-create-virtual-environments-with-venv-and-conda-in-python-31814c0a8ec2)安装在虚拟环境中。

创建的 *pydantic* 模型非常简单，包含三个带有类型注释的属性。正如所演示的，类型注释不仅用于类型提示，如果可能的话，它们还用于强制类型。例如，任何东西都可以转换成字符串。但是，只有一些特定的类型(字符串、浮点数、十进制数、布尔值等)可以转换为整数。如果一个属性的类型不能被强制，将会产生一个`ValidationError`和一些有用的错误信息，这些信息对调试非常有帮助。

## 创建自定义验证器来验证单个字段

现在让我们创建一个自定义验证器来验证`storage_type`字段。实际上，这种类型的简单验证可以用一个[枚举](https://lynn-kwong.medium.com/stop-using-magical-numbers-in-your-python-code-try-to-use-enum-like-a-pro-e1278c35b5ba)或一个[文字类型](https://peps.python.org/pep-0586/)来实现。然而，让我们在这里把重点放在基本语法上。一旦你知道了语法，你就可以用它来解决更复杂的问题。我们假设存储类型只能是“SSD”或者“HDD”。

为了创建一个定制的验证器，我们需要导入`validator`函数，它像[装饰器](https://lynn-kwong.medium.com/understand-and-master-the-decorator-in-python-481aa444933f)一样工作，并且装饰这个类的一些方法。修饰的方法将用于验证指定的字段。在这种情况下，要验证的字段是`storage_type`字段。注意，验证器像类方法一样工作，我们不需要为修饰方法添加`[@classmethod](https://betterprogramming.pub/how-to-use-the-magical-staticmethod-classmethod-and-property-decorators-in-python-e42dd74e51e7)`。不过，遗漏的`@classmethod`可能会被警告一些像 [*pylint*](https://lynn-kwong.medium.com/use-black-mypy-and-pylint-to-make-your-python-code-more-professional-b594512f4362) 这样的短毛。在这种情况下，您只需添加`@classmethod`，定制验证器仍将正常工作。

由于验证器作为[类方法](https://betterprogramming.pub/how-to-use-the-magical-staticmethod-classmethod-and-property-decorators-in-python-e42dd74e51e7)工作，第一个参数是按照惯例命名为`cls`的类。第二个参数是被验证的字段的值，可以随意命名。让我们使用`value`而不是`v`，linters 不喜欢它，因为它太短，不符合命名惯例。当值无效时，我们应该抛出一个`ValueError`或`TypeError`，而不是`ValidatorError`。我们用一个简单的例子来看看。

正如我们所看到的，当字段的值无效时，错误类型和错误消息会打印在控制台中。

## 在同一个验证器中验证多个字段

上面我们已经看到了如何验证单个字段，我们也可以在同一个验证器中验证多个字段。我们可以使用`"*"`来指定所有的字段，或者将它们明确地指定为单独的位置参数，而不是在列表或元组中:

重要的是，我们使用`pre`参数来指定当前验证器应该在其他验证之前还是之后被调用。这样，我们可以处理原始数据，然后将转换后的数据存储在相同的字段中。在这个例子中，`uppercase_strings`验证器将在`check_storage_type`验证器之前被调用，因此我们可以传递一个小写字符串(“ssd”和“hdd”)，它仍然可以被正确验证，因为所有的字符串都被`uppercase_strings`验证器转换为大写:

它显示所有的字符串字段都被我们的验证器转换成了大写。

## 检查以前验证过的字段

我们可以使用`values`位置参数来检查之前验证过的字段。`values`是包含已经验证的字段的键/值对的字典。让我们假设所有的苹果电脑应该只有 SDD 磁盘:

在类中首先声明的字段将首先被验证。在这个例子中，`brand`字段在`storage_type`字段之前被验证。注意，如果有些字段有类型注释，而有些没有，那么验证顺序可能会非常混乱。作为最佳实践，建议总是向所有字段添加类型注释，即使它们有默认值。有关字段排序的更多详情，请点击 [*此处*](https://pydantic-docs.helpmanual.io/usage/models/#field-ordering) 。

如果我们现在把“硬盘”传给苹果电脑，就会出现一个`ValidationError`:

## 用默认值验证字段

默认情况下，当一个字段有默认值时，它不会被 *pydantic* 验证。我们可以将`always`参数设置为`True`,以强制验证具有默认值的字段:

## 验证数组字段的每一项

当字段是包含多个值的列表时，您可以将`each_item`设置为`True`，而不是手动循环列表中的每一项，这样它们就可以自动循环。让我们给`ratings`字段添加一个验证器，它是一个整数列表。验证程序要求等级应该在 1 到 5 之间:

正如我们所看到的，数组字段的每个元素都是独立检查的。

## 使用根验证器来验证所有字段

默认情况下，验证器的`values`参数只存储已经验证过的字段。哪些字段在`values`中可用取决于它们在模型中声明的顺序。如果我们想访问`values`参数中的所有字段，并且不关心序列，我们可以使用根验证器:

注意，第二个参数是`values`而不是`value`，因为使用根验证器时，所有的字段都被一起验证。另外，请注意，我们需要将`skip_on_failure`设置为`True`，否则，即使某些字段无效，根验证器也会被调用。在这种情况下会有一个`KeyError`，我们定义的验证器将不能正常工作。

## 为多个模型重用同一个验证器

当我们有同一个验证器可以应用的多个模型时。我们可以将`allow_reuse`设置为`True`，这样同一个验证器可以在多个验证器中使用。这种用法要求您对装饰器在 Python 中的工作原理有一些基本的了解。如果你想快速更新，这篇文章会很有帮助。您至少应该知道，装饰器只是一个函数的语法糖，它接受另一个函数作为输入，并返回一个经过装饰的函数作为输出。

让我们为属性创建一个新模型，并对其应用相同的大写验证器。我们将创建一个独立的函数来进行转换，并在每个模型中使用它:

在这个例子中，验证器装饰器的`@`语法糖被移除了。从技术上讲， *pydantic* 验证器是一个装饰器工厂，`validator(“*”, pre=True, allow_reuse=True)`返回实际的装饰器。底层装饰器是一个函数，它将助手函数`uppercase_strings`作为输入，并将装饰后的函数作为输出返回，输出存储在属性`_uppercase_strings`中。函数在 Python 中是一个[一级公民](https://en.wikipedia.org/wiki/First-class_citizen)，可以用作任何其他数据类型。因此，它可以作为另一个函数的输入和输出来传递。

现在，如果我们向`Computer`和`Attribute`模型传递一些小写字符串，它们将被验证器转换成大写:

当有许多模型需要应用相同类型的验证时，我们需要在所有的模型中指定`allow_reuse`，这有点麻烦。程序员很懒，我们想尽可能避免代码重复。为了解决这个问题，我们可以创建另一个帮助函数来处理参数:

更新后的模型应该与上面显示的完全一样。

在这篇文章中，我们介绍了如何为 Pydantic 模型创建定制验证器。当您想要用一些默认验证器无法实现的规则来清理数据时，自定义验证器会非常有用。使用简单的代码片段给出了一系列涵盖不同用例的示例，这些代码片段很容易理解。我们不需要记住所有的方法，但是应该知道哪些是可用的，哪些可以用于我们的实际问题。

相关文章:

*   [用 mypy 和 pydantic 进行 Python 分型和验证](https://lynn-kwong.medium.com/python-typing-and-validation-with-mypy-and-pydantic-a2563d67e6d)
*   [了解并掌握 Python 中的装饰器](https://lynn-kwong.medium.com/understand-and-master-the-decorator-in-python-481aa444933f)