# 如何让你的 Pytest 测试像骨头一样干燥

> 原文：<https://levelup.gitconnected.com/how-to-make-your-pytest-tests-as-dry-as-bone-e9c1c35ddcb4>

## 使用固定物并将其参数化，让您和您的评审者都满意

![](img/46379700363267a7a8db885a7d3fe28d.png)

照片由[米凯拉父母](https://unsplash.com/@mparente?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 介绍

Pytest 附带了一系列不错的语法特性，所有 Python 开发人员都应该了解并经常使用这些特性。为什么会这样？因为，当你使用它们时，你将会极大地提高你的测试代码的质量，甚至更重要的是，当你写测试时，你的心情。在之前的文章中，我谈到了参数化 Pytest 测试。今天，我们来看看*固定装置*以及如何充分利用它们。你准备好了吗？让我们深入问题！

# 问题是

在 Pytest 中编写测试时，我使用最多的一个特性是[fixture](https://docs.pytest.org/en/6.2.x/fixture.html)。你现在可能会说:等等，什么是*夹具*？这里有一个简单的介绍。

Fixtures 是允许您可靠地、可重复地安排您的测试所依赖的或者可以利用的东西的构造。有了 fixtures，你还可以让你的代码更简洁，可读性更强。我最常使用夹具的情况如下:

1.  执行复杂的测试设置，比如启动和结束测试所依赖的服务。
2.  创建可以在测试中使用的复杂对象。

这听起来可能有点抽象，但是它们的用法非常简单，最好用一些代码来解释，如下所示:

那么，我们在这里做了什么？我们定义了两个函数`medium_dict`和`setup_some_service.`，它们都用`pytest.fixture`来修饰，这将一个无辜的函数变成了一个夹具:)。

要使用 fixture 或其返回值，您所要做的就是向您的测试函数添加一个与 fixture 同名的参数。在我们的例子中，那就是`medium_dict`和`setup_some_service`。正如您所看到的，您可以向您的测试函数传递任意多的 fixtures。您还可以将一个 fixture 传递给一个 fixture，并在那里使用它。所以它们非常灵活。

超级棒的是，你可以从固定装置上`yield`。这样，`yield`关键字之前的所有内容都会在测试函数执行之前运行。测试完成后，`yield`关键字之后的所有内容都会被执行。这是一种简单的测试设置和拆卸方法。

顺便提一下，如果您在测试的顶层添加一个 conftest.py 文件，并在其中定义 fixture，那么您的所有测试都将能够使用这些 fixture。自动地。没有任何显式导入。这一方面有点怪异，但也很方便:)。

现在我们知道了什么是夹具以及如何利用它们，我们来看实际问题:如何使夹具更加灵活？或者换句话说，我们如何参数化一个 fixture 来改变它的行为？

# 解决方案

要参数化一个 fixture，我们实际上只需要三样东西。首先，我们必须向`pytest.fixture`装饰器添加一个名为`*params*`的参数。其次，我们必须给 fixture 的函数参数添加一个名为`*request*`的参数。第三，使用`pytest.mark.parameterize`和 indirect 关键字来设置实际的参数值。

同样，这可能不是非常清楚，所以下面的代码应该能更清楚地说明这一点:

在这里，我向您展示了一个名为`complex_object`的 fixture，它有一个名为 *request* 的参数。通过*请求*，您可以使用其 *param* 属性访问分配的参数。

此外，我已经向 fixture 装饰器添加了参数 *params* 。这样，您可以为参数化夹具定义默认值。正如您从测试函数`test_with_complex_object_default_value`中看到的，这允许您使用那种夹具，而无需任何额外的代码。注意，您必须*为 params 参数*分配一个 iterable。这样，您就可以拥有多个默认值，并且对于这些值中的每一个，都会生成一个测试。如果你只需要一个，那么你的 iterable 的长度必须是 1。

现在，如果您对 fixture 的默认参数不满意，您可以使用`pytest.mark.parameterize`装饰器来更改它们。这里您所要做的就是添加您想要修饰的 fixture 的名称作为第一个参数，添加新的值作为第二个参数，并将关键字参数`indirect`设置为 True。这就是你在`test_with_complex_object`函数中看到的。在这个例子中，您将得到两个测试，其中`complex_object`分别用["a"]和["a "，" b"]参数化。

很简单，不是吗？

# 结论

在这篇简短的帖子中，我向您展示了如何编写 fixtures，更重要的是，您如何对它们进行参数化。如果您在您的测试代码中应用了这一点，我相信您可以减少测试代码的行数，同时进行更多的测试。

感谢您关注这篇文章。和往常一样，你可以在 [LinkedIn](https://www.linkedin.com/in/simon-hawe-75832057/) 上随意联系。