# React Native 中的本地化

> 原文：<https://levelup.gitconnected.com/incorporating-react-native-localization-in-react-native-eee822cdae93>

![](img/8982dc08c714af68ec62576d04b1d8a3.png)

memegenerator.net

我正在建立一个反应本地应用程序，我必须提供 3 种不同的语言，即英语，印地语和马拉地语的支持。我开始阅读各种提供本地化支持的 React 本地库。我选择了 [React 原生本地化](https://github.com/stefalda/ReactNativeLocalization)。文档看起来简洁明了，并提供了支持。不再赘述，让我列出安装这个库的步骤，然后您将看到如何在您的项目中使用它。

# 装置

*   在你的根目录下运行`yarn add react-native-localization && npx pod-install`。
*   卸载以前安装的应用程序并运行新版本
*   运行`yarn start --reset-cache`
*   瞧啊。

# 实例化

创建一个`translations.js`文件，并将其添加到`utils`文件夹中。将下面给出的代码片段添加到文件中

```
import React from 'react';
import LocalizedStrings from 'react-native-localization';export const translations = new LocalizedStrings({ 'en-US': { tagline: 'Appointments made easy', }, 'en-IN': { tagline: 'Appointments made easy', }, en: { tagline: 'Appointments made easy', }, hi: { tagline: 'नियुक्तियों को आसान बनाया', }, mr: { tagline: 'नेमणुका सुलभ केल्या', },});export const setLocale = (locale: DynamicObject) => {const prevTranslations = { ...translations.getContent() };const newTranslations: DynamicObject = {};Object.keys(locale).forEach((langCode) => { newTranslations[langCode] = { ...(prevTranslations && Object.keys(prevTranslations) ? prevTranslations[langCode] : {}), ...locale[langCode], }; }); translations.setContent(newTranslations);};const TranslationContext = React.createContext(translations);
```

我们传递我们将在站点中使用的带有文本字符串的区域设置，并创建一个`Localization`类的实例，然后将其导出。正如您所观察到的,**属性名称保持不变，但是每个地区的内容都不同。**考虑一下，这是一个文件，我们在其中添加了要在整个站点使用的常用/重复文本字符串。您可以创建一个单独的`locale.json`文件，为每个地区添加通用的文本字符串。

我们还导出了一个函数，帮助我们为各自的屏幕设置新的语言环境。

# 设置语言代码

在我们开始使用应用程序之前，我们必须为本地化库设置语言的语言代码，以便用我们选择的语言显示文本。让我们假设，我们想在应用程序启动之前设置它。将下面给出的代码添加到您的`AppNavigation.js`文件中。

```
import React from 'react';
import { translations, TranslationContext } from '../utils/translations';const AppNavigation = () => {
  const [isReady, setIsReady] = React.useState(false); React.useEffect(() => { if (!isReady) { translations.setLanguage('hi'); // hi = hindi
      setIsReady(true); } }, [isReady]); if(!isReady) {
    return null;
  } return (
    <TranslationContext.Provider value={translations}> <NavigationContainer>
        <Stack.Navigator>
          <Stack.Screen name="SelectLanguage" component={SelectLanguage} /> </Stack.Navigator> </NavigationContainer>
    <TranslationContext.Provider value={}> );};
```

我们使用库提供的`setlanguage`方法设置语言的语言代码，重新渲染组件。为了使本地化库以所选语言呈现正确的文本，我们必须在设置语言代码后重新呈现。

# 使用

假设我要渲染一个名为`SelectLanguage`的屏幕。我将在`screens`文件夹中创建`Selectlanguage/Selectlanguage.js`和`Selectlanguage/locale.json`。`locale.json`将由显示在`SelectLanguage`屏幕上的文本字符串的地区组成。举个例子，

```
{"en-US": {"selectLanguage": "Select your language"},"en-IN": {"selectLanguage": "Select your language"},"en": {"selectLanguage": "Select your language"},"hi": {"selectLanguage": "अपनी भाषा का चयन करें"},"mr": {"selectLanguage": "आपली भाषा निवडा"}}
```

然后，我们可以在我们的屏幕`SelectLanguage.js`中使用它，就像这样:

```
// Screens/SelectLanguage/SelectLanguage.js
import React, { useContext} from 'react';
import { setLocale, TranslationContext } from '@utils/translations';setLocale(locale);const SelectLanguageScreen = () => {
  ....Business Logic....
  const translations = useContext();return (
    <SelectLanguage translations={translations} />
  );}
```

`translations`保存所有的文本字符串，你可以像这样使用它`translations.selectLanguage`。该库将以您选择的语言呈现文本。`translations`应该只在组件内部使用**。**

因此，总结一下我们的帖子，我们需要用地区和文本字符串实例化库(属性名称保持不变，但是每个地区的内容会发生变化)，设置语言代码，然后使用翻译。

就是这样！

以圣父、圣子和圣灵的名义，您的应用程序已经本地化。