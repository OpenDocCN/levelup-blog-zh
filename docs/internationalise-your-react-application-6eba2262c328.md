# 国际化您的 React 应用程序

> 原文：<https://levelup.gitconnected.com/internationalise-your-react-application-6eba2262c328>

![](img/0b002f236d0a69a8a86916e295de2fa9.png)

照片由 [Pexels](https://www.pexels.com/photo/ball-shaped-blur-close-up-focus-346885/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 Porapak Apichodilok[拍摄](https://www.pexels.com/@nurseryart?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

在一个多语言的世界里(我说的不是编程语言！)我们需要我们的网络应用程序支持多种语言。

但是对于开发人员来说，维护这种多语言支持是一件非常痛苦的事情。

以 React-intl 为例。因为他们使用上下文来启用多语言，所以很难在共享库中启用国际化，并在您的主应用程序中使用共享组件(冲突、上下文之间的混淆以及应用程序中的更多复杂性)。
React 应用程序已经够复杂了，这给解决方案增加了更多的复杂性。另一个问题是翻译的存储。一种语言只能使用一个 JSON 文件。我们有可能在将它们发送给提供商之前合并它们，但是这是一个复杂且非常方便的用法。最后，存储的键是一个唯一的长字符串，通过使用 JSON，我们可以简单地嵌套它们。

这就是[反应的地方——翻译](https://github.com/psyycker/react-translation)改变它！

[](https://www.npmjs.com/package/@psyycker/react-translation) [## @ psycker/react-translation

### React 的现代翻译库

www.npmjs.com](https://www.npmjs.com/package/@psyycker/react-translation) 

没有上下文，没有唯一的入口点，通过使用 javascript 事件，没有库的两个版本之间的冲突问题！

React 翻译被认为是一种没有入口点，没有包装器，因此没有冲突风险的方法。

可以从不同的地方添加翻译并自动合并它们。你可以按照你喜欢的方式对它们进行分类，并拥有一个更好的文件架构。和 json 文件中的长字符串说再见。您可以嵌套您的对象并更好地查看您的翻译文件！

# 它是如何工作的？！

当然你必须安装这个库！

```
npm install --save @psyycker/react-translation
```

## 初始化

首先，你要初始化库。有一个默认的导出，它将初始化事件并将它们链接到应用程序的主体。

在你的应用程序的索引上(你可以使用一个共享库，可能在你的任何索引上)做一个简单的导入:

```
import "@psyycker/react-translation"
```

不要担心这个导入被多次执行(在共享库的情况下),因为它只会触发一次。其他时间不会覆盖事件

在需要设置默认区域设置时，您可以覆盖该配置:

```
import { setTranslationConfig } from "@psyycker/react-translation"
// Always call first before initialising the config
import "@psyycker/react-translation"

// Do not call inside a component
setTranslationConfig({
  defaultLocale: "en-US"
})
```

## 注册您的翻译

假设你想使用英语(美国)

您首先要创建您的 JSON 文件:

```
{
  "component": {
    "title": "My Value"
  }
}
```

这个文件，我们把它命名为`english-us.json`将会在`translations`文件夹中(但是可以在任何地方！)

要将它的内容添加到应用程序中，您只需注册它:

```
import { registerTranslations } from "@psyycker/react-translation";
import englishUS from "./translations/english-us.json";

registerTranslations({
  "en-US": englishUS
})
```

这将在库中注册新的区域设置`en-US`，并在其中添加 JSON 文件的内容。如果你想为相同的地区注册翻译，但是来自不同的文件，只需再次调用 register translations，它将合并两个翻译！没有超驰装置！

## 使用你的翻译

有两种方法可以使用你的翻译。你可以选择挂钩`useTranslation`或者使用`Translation`组件。

如果您想使用挂钩:

```
import { useTranslation } from "@psyycker/react-translation"; 

function MyComponent() {

  const { getTranslation } = useTranslation();

  return (
    <div>
     {getTranslation({
      translationKey: "component.title",
      defaultValue: "My Default Value"
    })}</div>
  )
}
```

如果您想使用该组件:

```
import { Translation} from "@psyycker/react-translation";

function MyComponent() {

  return (
    <div>
          <Translation 
            translationKey="component.title" 
            defaultValue="My default value"
         />
    </div>
  )
}
```

在这两种情况下，结果将显示相同。但是，使用组件会使代码更容易阅读，这是推荐的做法。

## 使用参数

您可以使用参数，以便

```
Hello {insertHere}
```

成为

```
Hello World!
```

为此，让我们编辑一下我们的翻译文件:

```
{
  "component": {
    "title": "Hello {input}"
  }
}
```

输入可以是你喜欢的任何东西！

然后，以同样的方式，您可以在`parameters`属性中添加您的`input`值！

```
import { Translation } from "@psyycker/react-translation"; 

function MyComponent() {

  return (
    <div>
          <Translation 
            translationKey="component.title" 
            defaultValue="My default value"
            parameters={{
              input: "My Custom Input"
            }}
         />
    </div>
  )
}
```

你也可以用钩子来做:

```
getTranslation({
      translationKey: "component.title",
      defaultValue: "My Default Value",
      parameters: { input: "My Custom Input" }
      })
```

最后的结果会是没有任何意义的`Hello My Custom Input`！

## 更改区域设置

最后，如果您想更改语言环境，只需调用从库中直接导入的`changeLocale`函数即可。

这个库是其他库的替代品，非常新！不要犹豫，打开一个问题，以获得支持或有更多的功能！

感谢您的阅读！