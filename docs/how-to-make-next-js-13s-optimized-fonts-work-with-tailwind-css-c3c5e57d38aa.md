# 如何让 Next.js 13 的优化字体与 Tailwind CSS 一起工作

> 原文：<https://levelup.gitconnected.com/how-to-make-next-js-13s-optimized-fonts-work-with-tailwind-css-c3c5e57d38aa>

几周前，Vercel 发布了 Next.js 13，以及一个非常有用的工具: [**优化字体**](https://nextjs.org/docs/basic-features/font-optimization) 。

这使得在你的应用程序中使用谷歌字体变得非常容易，并且具有自托管文件的优势。[这意味着更快的加载速度，更多的用户隐私，以及更少的手动下载字体的麻烦。](https://nextjs.org/docs/basic-features/font-optimization#google-fonts)

![](img/2ad7229246ed07fe9682e4293f3c5387.png)

安德鲁·希曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 下面是如何在 Next.js 13 应用程序中使用 Tailwind 的优化字体特性

首先，确保你已经更新到 Next.js 13(咄！)

## 对 _app.tsx 的更改

现在，进入你的 **_app.tsx** 文件，导入你喜欢的谷歌字体:

```
import { Lora } from '[@next/font](http://twitter.com/next/font)/google';
```

然后，我们实例化导入的字体，并添加一些设置:

```
const lora = Lora({
  subsets: ['latin'],
});
```

> 现在字体已经加载了，但是我们如何应用它呢？

**不幸的是，Next.js 随机重命名了 font family 属性，所以我们不能只在 CSS 中使用‘Lora’**。[虽然有通过 Next.js](https://nextjs.org/docs/api-reference/next/font#css-variables) 自动设置 CSS 变量名的选项，但这似乎在@next/font 的 13.0.2 版本中被打破了。所以让我们自己来做:

仍然在 **_app.tsx** 中，将以下内容添加到函数的输出中:

```
<style jsx global>
{`
  :root {
    --lora-font: ${lora.style.fontFamily};
  }
`}
</style>
```

这将为我们的字体手动定义 CSS 变量。

## 总之，这就是我们的复制-粘贴-就绪 _app.tsx

```
import { Lora } from '[@next/font](http://twitter.com/next/font)/google';
import { AppProps } from 'next/app';import '@/styles/globals.css';const lora = Lora({
  subsets: ['latin'],
});function MyApp({ Component, pageProps }: AppProps) {
  return (
    <>
      <style jsx global>
        {`
          :root {
            --lora-font: ${lora.style.fontFamily};
          }
        `}
      </style>
      <Component {...pageProps} />;
    </>
  );
}export default MyApp;
```

## 配置 tailwind.config.js

最后，我们必须重新配置 Tailwind 来使用这种字体。幸运的是，我们也可以在这里使用 CSS 变量。

只要确保你的 **tailwind.config.js** 看起来像这样:

```
const { fontFamily } = require('tailwindcss/defaultTheme');/** [@type](http://twitter.com/type) {import('tailwindcss').Config} */
module.exports = {
  // ...
  theme: {
    extend: {
      fontFamily: {
        primary: ['var(--lora-font)', ...fontFamily.sans],
        serif: ['var(--lora-font)', ...fontFamily.serif],
      },
    }
  },
  // ...
};
```

就是这样！

我很确定在接下来的几个月里，当这个功能成熟时，这个过程会变得更容易，所以如果你找到了一个更好的方法来给 Tailwind 添加优化的字体，请留下你的评论。我会尽量把这个作为一个活文档:)

继续编码！