# 如何在不重建提要的情况下将 XML 转换成 JSON

> 原文：<https://levelup.gitconnected.com/how-to-convert-xml-to-json-without-rebuilding-your-feed-b09734329bc9>

## 技术指南

## 利用一个简单的 API 将 XML 转换成 JSON，无需编码

![](img/1e61ae1c710a26b0ad16fb818431a034.png)

## XML 的问题是

说到 API，XML 是一种众所周知的提要格式，已经存在了一段时间，并且一直沿用至今。然而，正如塔姆林·罗德斯所总结的:

> “不幸的是，XML 确实存在。不管出于什么原因，有人曾经认为这是一个好主意，现在我们不得不坚持。因为大多数现代应用程序和 API 都使用 JSON，所以经常需要将 XML 转换成 JSON……”

因此，当我试图用 XML API 驱动我的一个 web 应用程序时，我在一个平台上遇到了问题。当我初始化这个调用时，平台不会选择 XML 属性值(比如`**systemCode="us-tv"**`)。以下是一集的元数据片段示例:

虽然有大量的 [XML 到 JSON 的转换器可用](https://www.google.com/search?q=xml+to+json+converter)，但是它们只能给你复制和粘贴数据的快照。但是，如果您有一个不断更新新元数据或者模板结构发生变化的提要，该怎么办呢？

## 解决方案:一个转换器 API

作为解决方案，我开发了一个 API，将 *live* [**XML 转换成 JSON**](https://github.com/factmaven/xml-to-json) **。**结果，我遇到问题的平台现在可以解析新格式的属性值:

使用 API 很简单，只需将 XML URL 粘贴到参数中:

```
[https://api.factmaven.com/xml-to-json?xml=https://example.com/feed.xml](https://api.factmaven.com/xml-to-json?xml=http://example.com/feed.xml)
```

点击回车键，XML 就会自动转换为[](https://www.dictionary.com/browse/automagically)*。享受您的新订阅格式。*

****我的代码是*** [***开源的，可在 GitHub***](https://github.com/factmaven/xml-to-json) ***上为那些想自己托管 API 的人做出贡献。****