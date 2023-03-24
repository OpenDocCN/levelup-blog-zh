# 使用 Ngx-Locutus 的角形微前端的 I18N

> 原文：<https://levelup.gitconnected.com/a-new-approach-to-i18n-in-angular-d46476b9874e>

![](img/fad2732efc2862f2e503b62ed5a45dc6.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Arpit Rastogi](https://unsplash.com/@arptrastogi?utm_source=medium&utm_medium=referral) 拍摄的照片

您是否曾经面临过将一个单片的 Angular 应用程序迁移到微前端架构的问题，或者只是想将您的应用程序重构为库？如果这本身不是一个挑战，那么将翻译文件分离到每个库和微前端以获得真正的独立性仍然是一个大问题。

## 为什么还要分离翻译文件呢？

微前端架构的要点是独立部署，因此不允许微前端之间存在依赖关系。

## 为什么它不能开箱即用。

通常，您使用一个流行的主流 i18n 库，如 Ngx-Translate 或 Transloco，这两个库默认情况下都从 assets 文件夹中加载翻译，其中为每种语言保存了一个 json 文件。

**微前端使用翻译。**当应用程序加载到浏览器中时，只有应用程序外壳的 assets 文件夹会存在于应用程序外壳上下文中。因此，不允许在微前端中相对引用翻译文件。相反，您必须编写一个 CustomLoader，它通过引用绝对 Uri 的 HttpClient 获取翻译文件，如下所示:

将出现的新问题是，当运行应用程序 shell 时，这个 http.get 将遇到 CORS 错误，因为这是一个明显的安全问题。然而，我们可以通过在应用程序外壳中创建一个 proxy.conf.json 来再次绕过这一点，欺骗应用程序外壳，使其认为它调用了自己，而实际上它向微前端资产的来源发出了请求。

然后将此配置添加到 project.json/package.json serve 部分的 options 部分:

```
“proxyConfig”: “apps/shell/proxy/proxy.conf.json”
```

现在你的微前端已经被翻译了，但是你的库呢？你的库本身不像你的应用程序那样运行在一个端口上，所以你不能像以前那样编写一个自定义加载器。相反，您可以创建一个预构建脚本，将 libraries 文件夹中的所有资源合并到应用程序资源中。一方面，这是一种真正的可能性，但另一方面，整个翻译过程被过度设计了很多。

个人备注:

> 我花了大约一周时间寻找这个问题的清晰解决方案，因为我真的不喜欢构建预构建脚本或创建全局翻译文件。我只是在想，如果翻译文件是纯 typescript 常量，并且不能在 assets 文件夹中引用，事情会简单得多。这样一切都可以很容易地分开。

所以我创建了一个替代的开源库来解决这个问题。😁

# Ngx-Locutus

[](https://github.com/HaasStefan/ngx-locutus) [## GitHub-HaasStefan/ngx-locutus:Angular 的可选国际化(i18n)库

### 用于大规模微前端翻译的可选角度翻译库。不再担心…

github.com](https://github.com/HaasStefan/ngx-locutus) 

*   每个翻译都有一个范围
*   每个翻译都是纯打字稿
*   翻译在单例服务中注册

## Typescript 翻译常数万岁

在 Ngx-Locutus 中，每个翻译文件都被实现为一个纯 Typescript 常量。一方面，这很好，因为翻译不是作为资产提供的，而是作为真正的代码，不需要以任何方式引用。另一方面，它甚至更酷，因为我们可以为每个翻译文件做一个接口定义，这样我们就可以保证每种语言都有相同的对象原型。

## 延迟加载翻译范围

为了不一次加载所有的翻译，我们为每种语言实现了一个 TranslationLoader，它实现了一个函数，当调用该函数时，它会缓慢地导入翻译并将其作为可观察的结果返回。

请注意，**非常重要的一点是**惰性导入实际上是**硬编码的**，因为否则翻译文件将被 Ivy 从树上摇下来——这意味着它们将不会包含在包中！

## 导入和配置 LocutusModule

在 AppModule 中，我们将调用静态函数 **forRoot()** ，在该函数中，我们传递一个翻译配置，该配置由作用域名称、翻译加载器和最初应该使用的语言组成。

对于功能模块，我们简单地使用 **forChild()** 函数，它也由作用域和加载器组成，但不是初始语言。

## 翻译 API

在 Ngx-Locutus 中进行实际的翻译是非常直接和灵活的。你可以使用一个指令让你在模板中使用作用域的翻译，或者你可以使用管道。

该指令只需要指定作用域，并为其翻译对象服务。它在后台自动订阅，每当语言改变或注册新的翻译时，就会触发重新加载。

管道转换翻译对象的键，并将范围作为参数。它以可观察的形式返回翻译，这样当语言发生变化时，您总能得到新的翻译。

如果您需要访问代码中的翻译，您应该使用以下方法之一:

*   **翻译**(范围:字符串，关键:字符串):可观察<字符串>
*   **getTranslations** (范围:字符串):可观察< any >
*   **instant** (scope: string，key: string): string *//未找到则抛出错误*

强烈建议避免使用 instant，因为很容易在最初几秒钟内抛出错误，因为翻译的延迟加载可能需要几毫秒。

# 结论

国际化应该是一件容易的事情，不管是使用 monolith 还是 microfrontends。Ngx-Locutus 是一种新的方法，试图涵盖这两种架构。我很高兴收到任何批评和祝福，因为我期待着继续开发 Ngrx-Locutus。

[](https://github.com/HaasStefan/ngx-locutus) [## GitHub-HaasStefan/ngx-locutus:Angular 的可选国际化(i18n)库

### 用于大规模微前端翻译的可选角度翻译库。不再担心…

github.com](https://github.com/HaasStefan/ngx-locutus) [](https://www.npmjs.com/package/ngx-locutus) [## ngx-locutus

### 用于大规模微前端翻译的可选角度翻译库。不再担心…

www.npmjs.com](https://www.npmjs.com/package/ngx-locutus) 

如果你想阅读无限量的文章并支持我，你可以使用我的推荐链接，这样我就可以获得 50%的月/年费:

[](https://medium.com/@stefan.haas.privat/membership) [## 通过我的推荐链接-斯特凡·哈斯加入媒体

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

medium.com](https://medium.com/@stefan.haas.privat/membership)