# 本地化你的 Chrome 扩展:一个简单的教程

> 原文：<https://levelup.gitconnected.com/localizing-your-chrome-extension-an-easy-tutorial-b0892e225576>

![](img/dd8a1b4383b171c824711c218e941ee3.png)

*你也可以在我的个人博客* [*这里*](https://shahednasserblog.tk/localizing-your-chrome-extension-an-easy-tutorial/) *查看这个帖子。*

本地化和国际化是为您的扩展获得更多用户的好方法。这里有一个简单的教程可以帮助你做到这一点。

本教程假设你已经知道如何创建一个 chrome 扩展。如果没有，你可以前往我的 [**其他教程**](https://shahednasserblog.tk/chrome-extension-tutorial-replace-images-in-any-website-with-pikachu/) **了解如何做到这一点。**

# 步骤 1:更改 Manifest.json

在本地化您的扩展时，您需要选择一个默认的区域设置。这需要在`manifest.json`文件中定义:

```
{
 ...
    "default_locale": "en",
    ...
}
```

您需要添加一个新的键`default_locale`,其值应该是您的扩展默认的区域设置。在上面的例子中，我们为英语选择了`en`。

你可以点击查看支持的地区列表[。如果你选择了一个不受支持的语言环境，Chrome 会忽略它。](http://code.google.com/chrome/webstore/docs/i18n.html#localeTable)

# 步骤 2:翻译字符串

接下来您需要做的是开始创建 JSON 文件，这些文件将在您的扩展中保存可本地化的字符串。

您需要首先在您的扩展的根目录下创建目录`locales`。然后，在该目录中，您将为每个地区创建一个文件夹，该文件夹将保存 JSON 文件以及每个地区的字符串翻译。例如，要添加英语地区`en`的字符串翻译，您需要在`_locales`中创建目录`en`，然后在其中创建`messages.json`。

您不必为每种语言创建`messages.json`文件，也不必翻译每种语言的所有字符串。唯一需要的是拥有默认区域设置的字符串。那么对于任何其他语言，如果消息没有被翻译成该语言环境，它将默认为默认语言环境中的字符串。

因此，基于前面的例子，我们需要创建文件`_locales/en/messages.json`并放置我们需要在整个扩展中翻译的字符串。

`messages.json`文件的格式如下:

```
{
 "KEY": {
     "message": "VALUE"
    },
    ...
}
```

对于您想要翻译的每个字符串，您需要指定`KEY`，然后它的值将是一个带有键`message`的对象，它的值将是这个区域设置中的字符串。

例如，如果我想添加字符串“Hello”:

```
{
 "greeting": {
     "message": "Hello"
    }
}
```

在这里，我定义了键`greeting`，它在英语地区的值是“Hello”。

还可以添加可选键`description`。只有当你想让其他人翻译你的字符串时，这才有帮助，你可以给他们一些关于何时使用这个消息或者应该如何翻译的上下文。

如果我们想把一个不应该翻译的参数传递给翻译字符串呢？假设我想称呼用户的名字。例如，“你好，约翰”。

要向字符串添加参数，字符串对象应该如下所示:

```
{
 "greeting": {
     "message": "Hello, $USERNAME$",
        "placeholder": {
         "username": {
             "content": "$1"
            }
        }
    }
}
```

我们是这样做的:

1.  我们使用`$USERNAME`作为`message`中参数的名称
2.  我们在`greeting`对象中添加了一个新的键`placeholder`。`placeholder`是一个对象，其中有对象，键是消息中存在的参数的名称，每个对象的值都有定义参数值的键`content`。
3.  在内容中使用`$1`意味着它将是传递给翻译函数的第一个参数(我们很快会谈到这一点)。该值可以是类似于`$2`(第二个参数)、`$3`(第三个参数)的东西，或者只是一个类似于“John”的字符串。如果翻译函数中没有传递值，`$USERNAME`将被一个空字符串替换。

`placeholder`中的每个对象也可以有一个可选键`example`,表示参数值可以是什么。例如:

```
{
 ...
    "username": {
     "content": "$1",
        "example": "John"
    }
    ...
}
```

这和上面解释的`description`键一样，可以帮助任何翻译这个扩展的人理解传递给消息的参数可能是什么。

假设我们现在想添加西班牙语的翻译。我们需要创建文件`locales/es/messages.json`。注意，我们在`_locales`中创建了一个新文件夹`es`。

然后在`messages.json`文件中，我们将有如下内容:

```
{
 "greeting": {
     "message": "Hola"
    }
}
```

我们需要使用在默认区域设置中使用的相同键`greeting`，因为我们稍后将使用它来获取基于区域设置的字符串。然后对于`message`,我们使用西班牙语的翻译。

注意，如果你使用一个参数，你也需要把它放在这里。例如:

```
{
 "greeting": {
     "message": "Hola, $USERNAME",
        "placeholder": {
         "username": { 
             "content": "$1"
            }
        }
    }
}
```

# 步骤 3:使用本地化字符串

# 在 JS 文件中:

要获得 JS 文件中的本地化字符串，您需要使用函数`chrome.i18n.getMessage`。例如:

```
chrome.i18n.getMessage('greeting');
```

该值将是当前语言环境中键`greeting`的翻译。如果翻译不存在，将使用默认区域设置中的值。

如果要传递一个参数，可以将其作为第二个参数传递:

```
chrome.i18n.getMessage('greeting', 'John');
```

注意，如果你想传递多个参数，你应该使用一个数组。

# 在 manifest.json 和 CSS 文件中

要在`manifest.json`和 CSS 文件中使用本地化字符串，您需要在字符串名称前加上`__MSG_`，然后在其后加上`__`。例如:

```
{
...
"name": "__MSG_greeting__"
...
}
```

`__MSG_greeting__`将为键`greeting`放置翻译。

# HTML 文件中的本地化

为了翻译 HTML 文件中的字符串，您需要使用 javascript 手动完成。

以下是如何用普通 Javascript 实现这一点:

您也可以使用 jQuery:

# 步骤 4(可选):根据区域设置更改 CSS 方向

当您本地化您的扩展时，您可能需要根据语言改变样式。最重要的是，使您的扩展兼容 RTL 和 LTR 语言。

Chrome 的国际化系统提供了一些有助于扩展本地化的消息或变量。在 CSS 中特别有用的是:

1.  **@@bidi_dir:** 基于当前区域设置的方向(值将是 **rtl** 或 **ltr** )
2.  **@@bidi_reversed_dir:** 当前方向的反向。所以如果`@@bidi_dir`的值是 rtl，`@@bidi_reversed_dir`的值将是 ltr，反之亦然。
3.  **@@bidi_start_edge:** 如果`@@bidi_dir`的值是 rtl，值就对了；否则，它将被留下。
4.  **@@bidi_end_edge:** 如果`@@bidi_dir`的值为 rtl，则该值为左；否则，它将被留下。基本上和`@@bidi_start_edge`相反。

这些消息在您的 CSS 中很有帮助，因为您可以在设计您的扩展时使用它们。例如:

```
html {
 direction: __MSG_@@bidi_dir__; //The direction will change based on the current locale
}
```

确保 CSS 中的每条消息前面都有`__MSG_`，后面有`__`

还提供了其他消息:

1.  **@@extension_id:** 您分机的 id。在`manifest.json`不能用
2.  **@@ui_locale:** 当前区域设置。

# 结论

完成上述步骤后，您的 chrome 扩展将被本地化，并为不同语言的用户做好准备！