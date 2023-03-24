# 使用 Hugo 进行图像处理

> 原文：<https://levelup.gitconnected.com/image-processing-using-hugo-f03e92bd9bd>

![](img/79cc05792e31effc55152a45f39d77fc.png)

*照片由*[*@ Pascal hook*](https://unsplash.com/@pascalwhoop)*在 Unsplash* 上拍摄

# 介绍

自从我把这个博客从 Jekyll 迁移到 Hugo，我想尝试 Hugo 提供的所有功能。其中一个特点是图像处理。它可以帮助我开发一个手机友好的博客。图像处理可能会对你有所帮助，你可以从智能手机上阅读这些内容，因为智能手机的互联网连接速度较慢，可以更快地加载图像。

虽然我尽可能少地开发了这个博客，但是处理图像可能是个问题。这就是 Hugo 的功能帮助我创建这个问题的解决方案的地方。在本文中，您将学习使用 Hugo 实现图像处理。

# 短代码

Shortcodes 是 Hugo 的一个特性，可以帮助重用图像处理的实现。你可能想在这里阅读更多关于短码的信息。现在让我们创建一个。

您可以在 Hugo 项目中创建短代码，路径如下:

1.  `/layouts/shortcodes/<name>.html`
2.  `/themes/<theme>/layouts/shortcodes/<name>.html`

您可以在`/layouts/shortcodes/img.html`创建短代码。

```
{{- $src := .Get "src" -}}{{- $alt := .Get "alt" -}}<figure> <img src="{{ $src }}" alt="{{ $alt }}" width="100%" height="auto"/> <figcaption>{{ $alt }}</figcaption></figure>
```

假设你已经把你的图片放到了 Hugo 的资产目录中。例如，您将图像文件放在`/assets/img/testing/ehe.png`中，然后您可以像这样在您的内容中使用它:

```
{{< img src="/img/testing/ehe.png" alt="alt" >}}
```

目前雨果还不会渲染。让我们调整 shortcode，这样它将呈现图像。

```
{{- $alt := .Get "alt" -}}{{- $res := resources.GetMatch (.Get "src") -}}<figure> <img src="{{ $res.RelPermalink }}" alt="{{ $alt }}" width="100%" height="auto"/> <figcaption>{{ $alt }}</figcaption></figure>
```

现在你的图像被渲染了。让我们创建不同宽度的多个版本。

# 宽度

现在定义你想要渲染的宽度。例如:

```
...{{- $ws := slice 480 768 1366 1920 -}}...
```

然后，用 [Hugo 的 resize 函数](https://gohugo.io/content-management/image-processing/#resize)对其进行迭代。

```
...{{- range $ws -}}{{- $w := printf "%dx" . -}}{{- ($res.Resize $w).RelPermalink | safeURL -}}{{- end -}}...
```

现在，您将得到类似如下的输出:

```
/img/testing/ehe_hudb6c5cbc207f47e5a1b3b7a3072e7a12_81266_480x0_resize_box_3.png/img/testing/ehe_hudb6c5cbc207f47e5a1b3b7a3072e7a12_81266_768x0_resize_box_3.png/img/testing/ehe_hudb6c5cbc207f47e5a1b3b7a3072e7a12_81266_1366x0_resize_box_3.png/img/testing/ehe_hudb6c5cbc207f47e5a1b3b7a3072e7a12_81266_1920x0_resize_box_3.png
```

这些是您处理过的图像 URL，具有您之前定义的每个宽度。现在让我们在图像的源集中使用它们。

# 源集

要在您的图像上定义`srcset attribute`，您可以使用以下[格式](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-srcset):

```
<img srcset="/url/to/480.png 480w,/url/to/768.png 768w,/url/to/1366.png 1366w,/url/to/1920.png 1920w" alt="alt">
```

让我们在您的短代码中生成它。

```
...{{- $ws := slice 480 768 1366 1920 -}}{{- $srcset := slice -}}{{- range $ws -}}{{/* to avoid creating an image that is larger than the source */}}{{- if (le . $res.Width) -}}{{- $w := printf "%dx" . -}}{{- $url := ($res.Resize $w).RelPermalink | safeURL -}}{{- $fmt := printf "%s %dw" $url . -}}{{- $srcset = $srcset | append $fmt -}}{{- end -}}{{- end -}}...
```

现在你有了一个切片中的`srcset`格式。你可以用`Hugo's delimit function`加入他们。

```
...{{- $set := delimit $srcset "," -}}...
```

然后，用它做`srcset attribute`。

```
...<figure> <img srcset="{{ $set }}" src="{{ $res.RelPermalink }}" alt="{{ $alt }}" width="100%" height="auto"/> <figcaption>{{ $alt }}</figcaption></figure>
```

最后，让 HTML 根据视窗宽度渲染图像。

# 大小

要在你的图像上定义`sizes attribute`，你可以使用这个[格式](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-sizes):

```
...<figure> <img srcset="{{ $set }}" sizes="(max-width: 480px) 480px, 100vw" src="{{ $res.RelPermalink }}" alt="{{ $alt }}" width="100%" height="auto"/> <figcaption>{{ $alt }}</figcaption></figure>
```

这里是`/layouts/shortcodes/img.html`的完整源代码:

```
{{- $alt := .Get "alt" -}}{{- $res := resources.GetMatch (.Get "src") -}}{{- $ws := slice 480 768 1366 1920 -}}{{- $srcset := slice -}}{{- range $ws -}}{{/* to avoid creating an image that is larger than the source */}}{{- if (le . $res.Width) -}}{{- $w := printf "%dx" . -}}{{- $url := ($res.Resize $w).RelPermalink | safeURL -}}{{- $fmt := printf "%s %dw" $url . -}}{{- $srcset = $srcset | append $fmt -}}{{- end -}}{{- end -}}{{- $set := delimit $srcset "," -}}<figure> <img srcset="{{ $set }}" sizes="(max-width: 480px) 480px, 100vw" src="{{ $res.RelPermalink }}" alt="{{ $alt }}" width="100%" height="auto"/> <figcaption>{{ $alt }}</figcaption></figure>
```

现在，如果你建立你的 Hugo 网站，你会看到你的图片是自动生成的。

```
$ tree img/img/└── testing├── ehe_hudb6c5cbc207f47e5a1b3b7a3072e7a12_81266_1366x0_resize_box_3.png├── ehe_hudb6c5cbc207f47e5a1b3b7a3072e7a12_81266_480x0_resize_box_3.png├── ehe_hudb6c5cbc207f47e5a1b3b7a3072e7a12_81266_768x0_resize_box_3.png└── ehe.png1 directory, 4 files
```

感谢您的阅读！

*最初发布于 2021 年 10 月 25 日*[*https://clavinjune . dev*](https://clavinjune.dev/en/blogs/image-processing-using-hugo/)*。*