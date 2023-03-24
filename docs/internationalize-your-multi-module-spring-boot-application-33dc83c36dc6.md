# 国际化您的多模块 Spring Boot 应用程序

> 原文：<https://levelup.gitconnected.com/internationalize-your-multi-module-spring-boot-application-33dc83c36dc6>

![](img/6b370fdf05e1bc86f4426f69378baccc.png)

## *如何以及在哪里定义面向用户的字符串。*

# 消息.属性

你可能已经读过了`messages.properties`文件。它允许您定义希望呈现给最终用户的任何字符串。

`messages.properties`:

要在代码中检索这些字符串之一，你首先需要得到一个`MessageSource`。这是通过[弹簧依赖性注射](https://www.baeldung.com/spring-dependency-injection)提供的。

构造函数注入:

```
@Service // Or some other `Component` annotation
class SomeService( private val messageSource: MessageSource )
```

属性注入:

```
@Autowired private lateinit var messageSource: MessageSource
```

然后你可以调用 getMessage:

```
messageSource.getMessage("title.example", null, LocaleContextHolder.getLocale())
```

`args`可提供换弦。例如:

```
messageSource.getMessage("message.hello", arrayOf("Joe Bloggs"), LocaleContextHolder.getLocale())
```

# 国际化

注意上面，我们将`LocaleContextHolder.getLocale()`作为`Locale`传递给`getMessage`调用。这告诉`MessageSource`提供哪种语言。

`LocaleContextHolder`提供 spring 知道的电流`Locale`。这是一个强大的机制。我们不需要添加任何自定义代码到我们的`RestController`中或者以其他方式来确定`Locale`。调用者可以将区域设置为请求头，spring 将使其可用。

为了提供其他语言字符串，我们添加了一个新的消息文件，`messages_fr.properties`:

要了解更多信息，请查看本[指南](https://www.baeldung.com/spring-boot-internationalization)。

# 模块化

当模块化你的应用程序时，你可能会遇到麻烦。默认情况下，每个模块的`messages.properties`文件会相互冲突。只有一组消息可用。

为了避免这种情况，所有的消息文件需要有不同的名称。一个好的习惯是使用你的模块名。比如:`messages-application.properties`、`messages-application_fr.properties`、`messages-library.properties`、`messages-library_fr.properties`。

现在我们需要将这些暴露给`MessageSource`。默认情况下，只会找到名为`message.properties`的文件。

在您的`application.properties`文件中添加:

```
spring.messages.basename=messages-application,messages-library
```

请注意，我们在这里没有包括语言扩展名或文件扩展名。

**就这样。现在，您可以在每个不同的模块中提供字符串。**

*最初发表于*[*【https://www.brightec.co.uk】*](https://www.brightec.co.uk/blog/internationalize-your-multi-module-spring-boot-application)*。*