# 模具组件翻译

> 原文：<https://levelup.gitconnected.com/stencil-component-translations-4efc44018b1d>

## 为没有依赖关系的模具组件设置 i18n 的快速方法。

![](img/da7fb57f05c1de70450b21982da50a75.png)

卢卡斯·乔治·温迪特在 [Unsplash](https://unsplash.com/s/photos/internationalization?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我一直在使用相同的策略来快速国际化各种项目中没有依赖关系的[模板](https://stenciljs.com/)组件。其中包括项目[owly](https://owlly.ch/)或 [Bonjour foundation](https://bonjour.help/) 。

由于所有这些项目都是开源的，我将与你分享我的食谱🧑‍🍳。

# 目标

我最近在社区的帮助下国际化了我们的项目 [DeckDeckGo](https://deckdeckgo.com) ，但是这篇文章的目标是将翻译添加到一个相对较小的组件或一组组件中，而没有依赖性。

当我创建包含一些带有默认值的`slot`的组件时，我使用这种解决方案，这些组件的主要市场是我们可爱的“默认四种语言+英语”瑞士🇨🇭.

# 设置

在您的项目中创建一个新的实用程序`translations.util.ts`并添加声明。

```
interface Translation {
  [key: string]: string;
}

interface Translations {
  de: Translation;
  en: Translation;
}
```

对于这个例子，我将“只”使用德语和英语。对于现实生活中的用例，用你的需求来扩展它。

我声明了一个`interface Translation`，但是，它可以被替换为一个打字稿`Record<string, string>`。结果是一样的，正如你所愿。

在声明之后，添加一个默认(后备)语言的常量和一个受支持语言的列表。

```
const DEFAULT_LANGUAGE: 'en' = 'en';

const SUPPORTED_LANGUAGES: string[] = ['de', 'en'];
```

最后，添加有效的翻译。

```
const TRANSLATIONS: Translations = {
  de: {
    question: 'Wie fühlen Sie sich heute?',
    super: 'Sehr gut',
    bad: 'Nicht gut'
  },
  en: {
    question: 'How do you feel today?',
    super: 'Super',
    bad: 'Bad'
  }
};
```

在这个解决方案中，因为我的目标是保持快速和简单，所以我对翻译进行编码。

可以在单独的`json`文件中处理这些，并在运行时动态处理`import`。这是我为更复杂的用例开发的两个特性。毕竟，我可能真的需要把这件事写在博客上？如果你认为那会是一个有趣的主题，请告诉我！

# 检测语言

我没有重新发明轮子。我查看了广泛使用的开源库 [ngx-translate](https://github.com/ngx-translate/core) 并实现了对浏览器语言的检测。

在同一个文件中，添加下面的函数和默认语言的初始化。

```
const initBrowserLang = (): string | undefined => {
  if (typeof window === 'undefined' 
      || typeof window.navigator === 'undefined') {
    return undefined;
  }

  let browserLang: string | null =
    window.navigator.languages 
    && window.navigator.languages.length > 0 ? 
              window.navigator.languages[0] : null; // @ts-ignore
  browserLang = browserLang || window.navigator.language || window.navigator.browserLanguage || window.navigator.userLanguage;

  if (typeof browserLang === 'undefined') {
    return undefined;
  }

  if (browserLang.indexOf('-') !== -1) {
    browserLang = browserLang.split('-')[0];
  }

  if (browserLang.indexOf('_') !== -1) {
    browserLang = browserLang.split('_')[0];
  }

  return browserLang;
}

const browserLang: string | undefined = initBrowserLang();
```

# 功能

设置和检测都准备好了，我们需要一个函数来呈现翻译。

```
export const translate = 
             (key: string, customLang?: 'de' | 'en'): string => {
  const lang: string | undefined = customLang || browserLang; return TRANSLATIONS[lang !== undefined 
                      && SUPPORTED_LANGUAGES.includes(lang) ? 
                         lang : DEFAULT_LANGUAGE][key];
};
```

它使用浏览器或参数语言，对照支持的语言列表进行检查，或者返回默认语言，并返回相关的翻译。

# 使用

在我们的组件中，上述函数可用于呈现翻译。

```
import {Component, h, Host} from '@stencil/core';

import {translate} from './translations.utils';

@Component({
  tag: 'question',
  shadow: true
})
export class Question {
  render() {
    return <Host>
      <p>{translate('question')}</p>
      <slot name="answer">{translate('super', 'de')}</slot>
    </Host>;
  }
}
```

它渲染文本，指定或不指定语言，也可以与`slot`一起使用。

# 摘要

![](img/659feb33c8aa210cccf9dbed5521802e.png)

这是我将 i18n 设置到一个相对较小的组件集的小窍门。我希望这是有用的，如果你认为我应该在另一篇文章中分享更复杂的解决方案，请告诉我。

到无限和更远的地方！

大卫

你可以通过[推特](https://twitter.com/daviddalbusco)或我的[网站](https://daviddalbusco.com/)联系我。

为您的下一张幻灯片尝试一下 [DeckDeckGo](https://deckdeckgo.com/) 🤟。

[![](img/6fb98ff4c0bba1406d5988f880c127f9.png)](https://deckdeckgo.com)