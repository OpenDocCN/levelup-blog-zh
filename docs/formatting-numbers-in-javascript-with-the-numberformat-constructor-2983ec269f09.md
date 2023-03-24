# 用 NumberFormat 构造函数在 JavaScript 中格式化数字

> 原文：<https://levelup.gitconnected.com/formatting-numbers-in-javascript-with-the-numberformat-constructor-2983ec269f09>

![](img/5fb462bb7f7ad78e30be59c66090f47e.png)

K. Mitch Hodge 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 有很好的国际化特性。其中之一是它能够为非英语用户格式化数字。

这意味着我们可以为使用非英语语言环境的人显示数字，而无需添加另一个库来完成这项工作。我们可以用`Intl.NumberFormat`构造函数格式化数字。构造函数有两个参数。

第一个参数是区域设置字符串或区域设置字符串数组。区域设置字符串应该是 BCP 47 语言标记，可以选择附加 Unicode 密钥扩展。由构造函数创建的对象有一个`format`方法，它接受一个我们想要格式化的数字。

# 构造函数和格式化方法

要使用`Intl.NumberFormat`构造函数，我们可以用构造函数创建一个对象，然后对构造函数新创建的对象使用`format`方法来格式化数字。我们可以编写类似下面的代码:

```
console.log(new Intl.NumberFormat('en', {
  style: 'currency',
  currency: 'GBP'
}).format(222));
```

上面的代码将数字 222 格式化为以英镑为单位的价格金额。我们通过传入带有`currency`值的`style`选项，并将`currency`属性设置为 GBP，这是英镑的货币符号。

`Intl.NumberFormat`构造函数接受两个参数，第一个是 locales 参数，它接受一个 locale 字符串或一个 locale 字符串数组。这是一个可选参数。它使用一个 BCP 47 语言标签和可选的 Unicode 扩展键`nu`来指定数字格式的编号系统。`nu`的可能值包括:`"arab"`、`"arabext"`、`"bali"`、`"beng"`、`"deva"`、`"fullwide"`、`"gujr"`、`"guru"`、`"hanidec"`、`"khmr"`、`"knda"`、`"laoo"`、`"latn"`、`"limb"`、`"mlym"`、`"mong"`、`"mymr"`、`"orya"`、`"tamldec"`、`"telu"`、`"thai"`、`"tibt"`。

构造函数创建的对象实例的`format`方法返回一个带格式化数字的字符串。BCP 47 语言标签的简略列表包括:

*   ar —阿拉伯语
*   保加利亚语
*   加利福尼亚—加泰罗尼亚语
*   zh-Hans-中文，汉族(简体变体)
*   cs —捷克语
*   da —丹麦语
*   德语
*   el —现代希腊语(1453 年及以后)
*   英语
*   es —西班牙语
*   fi —芬兰语
*   fr —法语
*   他—希伯来语
*   胡—匈牙利语
*   是—冰岛语
*   它—意大利语
*   ja —日语
*   朝鲜文
*   nl —荷兰语
*   不——挪威人
*   pl —波兰语
*   pt —葡萄牙语
*   rm —罗曼语
*   ro —罗马尼亚语
*   ru —俄语
*   HR-克罗地亚语
*   sk —斯洛伐克语
*   sq —阿尔巴尼亚语
*   sv —瑞典语
*   泰语
*   tr —土耳其语
*   乌尔都语
*   id —印度尼西亚语
*   英国—乌克兰语
*   是—白俄罗斯人
*   sl —斯洛文尼亚语
*   et —爱沙尼亚语
*   lv —拉脱维亚语
*   lt —立陶宛语
*   tg —塔吉克语
*   fa —波斯语
*   六—越南语
*   hy —亚美尼亚语
*   az —阿塞拜疆语
*   欧盟—巴斯克语
*   hsb —上索布语
*   mk —马其顿语
*   tn —茨瓦纳
*   科萨语
*   祖祖鲁
*   af —南非荷兰语
*   ka 语—格鲁吉亚语
*   fo —法罗群岛语
*   嗨—印地语
*   马耳他山
*   东南-北萨米人
*   ga —爱尔兰语
*   马来语(宏语言)
*   kk —哈萨克语
*   ky —吉尔吉斯
*   西南语——斯瓦希里语(宏观语言)
*   tk —土库曼语
*   乌兹别克语
*   tt —鞑靼语
*   bn —孟加拉语
*   pa-Panjabi
*   gu —古吉拉特语
*   或者——奥瑞亚
*   塔语—泰米尔语
*   泰—泰卢固语
*   卡纳达语
*   ml —马拉雅拉姆语
*   阿萨姆语
*   马拉蒂先生
*   sa —梵语
*   蒙古语
*   博语—藏语
*   cy —威尔士语
*   km —高棉语中部
*   老挝语
*   gl —加利西亚语
*   kok-kon kani 语(宏观语言)
*   叙利亚语
*   西-僧伽罗语
*   iu —因纽特人
*   am-阿姆哈拉语
*   tzm —中央地图集 Tamazight
*   尼泊尔语
*   fy —西弗里斯兰
*   ps — Pushto
*   菲律宾语
*   dv —迪维希语
*   哈—豪萨
*   哟——约鲁巴语
*   库斯科-克丘亚州
*   国家统计局-儿科
*   巴-巴什基尔
*   lb —卢森堡语
*   吉隆坡—卡拉利苏特
*   ig —伊博人
*   二—四川彝族
*   阿恩-马普顿贡
*   MOH-Mohawk
*   布雷顿
*   ug —维吾尔语
*   米—毛利人
*   oc-Occitan(1500 年后)
*   科西嘉岛
*   gsw —瑞士德语
*   SAH-Yakut
*   qut —危地马拉
*   rw —基尼亚卢旺达语
*   沃-沃洛夫
*   prs —达里语
*   gd —苏格兰盖尔语

第二个参数接受一个具有几个属性的对象— `localeMatcher`、`style`、`unitDisplay`、`currency`、`useGrouping`、`minimumIntegerDigits`、`minimumFractionDigits`、`maximumFractionDigits`、`minimumSignificantDigits`和`maximumSignificantDigits`。

`localeMatcher`选项指定要使用的语言环境匹配算法。可能的值是`lookup`和`best fit`。查找算法搜索区域设置，直到找到符合正在比较的字符串字符集的区域设置。`best fit`找到至少比`lookup`算法更适合的语言环境。

`style`选项指定要使用的格式样式。`style`选项的可能值包括`decimal`、`currency`、`percent`和`unit`。`decimal`是默认选项，用于普通数字格式，`currency`用于货币格式，`percent`用于百分比格式，`unit`用于`unit`格式。

`currency`选项用于货币格式化。可能的值是 ISO 4217 货币代码，例如 USD 代表美元，EUR 代表欧元。没有默认值。

如果`style`属性设置为`currency`，则必须提供`currency`属性。

属性设置货币在货币格式中的显示方式。可能的值有:`symbol`用于添加本地化货币符号，这是默认值，`code`用于添加 ISO 货币代码，`name`用于使用类似“美元”的本地化货币名称。`useGrouping`选项用于设置数字的分组分隔符，是一个布尔值。

`minimumIntegerDigits`、`minimumFractionDigits`和`maximumFractionDigits`被视为一组选项。`minimumIntegerDigits`指定要使用的最小整数位数，范围从 1 到 21，1 为默认选项。`minimumFractionDigits`是要使用的最小分数位数，范围从 0 到 20。

对于普通数字和百分比格式，默认值为 0。货币格式的默认值由 ISO 4217 货币代码列表指定，如果列表中没有指定，则为 2。`maximumFractionDigits`是要使用的小数位数的最大值，取值范围为 0 到 20。

普通数字的默认值是`minimumFractionDigits`和 3 之间的最大值。货币格式的默认值是`minimumFractionDigits`和 ISO 4217 货币代码列表提供的小数位数之间的最大值，如果列表不提供该信息，则为 2。百分比格式的默认值是介于`minimumFractionDigits`和 0 之间的最大值。

`minimumSignificantDigits`和`maximumSignificantDigits`被认为是另一组选项。如果至少定义了该组中的一个选项，则忽略第一组。`minimumSignificantDigits`是要使用的最小有效位数，取值范围为 1 到 21，默认值为 1。`maximumSignificantDigits`是要使用的最大有效位数，取值范围为 1 到 21，默认值为 21。

格式化数字的一些例子包括要求数字的最小位数。我们可以像下面这样用构造函数和`format`方法来实现:

```
console.log(new Intl.NumberFormat('en', {
  style: 'currency',
  currency: 'GBP',
  minimumIntegerDigits: 5
}).format(222));
```

然后当我们运行上面代码中的`console.log`函数时，我们得到 00，222.00。我们还可以使用`minimumFractionDigits`选项指定小数点后的最小小数位数，如以下代码所示:

```
console.log(new Intl.NumberFormat('en', {
  style: 'currency',
  currency: 'GBP',
  minimumFractionDigits: 2
}).format(222));
```

然后，当我们运行上面代码中的`console.log`函数时，我们得到 222.00。我们可以用`useGrouping`选项改变数字的分组，如下面的代码所示:

```
console.log(new Intl.NumberFormat('hi', {
  style: 'decimal',
  useGrouping: true
}).format(22222222222));
```

我们可以看到，当我们记录上面代码的输出时，我们得到了 22，22，22，222。`hi`地区是印地语地区，其数字格式与英语不同，我们可以看到，在印地语中，当数字大于一千时，数字被分成两个一组。

此外，我们可以将数字格式化成非阿拉伯数字。例如，如果我们想用中文显示数字，我们可以将`nu`选项设置为地区字符串的 Unicode 扩展键。例如，我们可以写:

```
console.log(new Intl.NumberFormat('zh-Hant-CN-u-nu-hanidec', {
  style: 'decimal',
  useGrouping: true
}).format(12345678));
```

Then we get ‘一二,三四五,六七八’ logged. The `nu-hanidec` specified the number system that we want to display the formatted number in. In the example above, we specified the number system to be the Chinese number system, so we displayed all the digits in Chinese.

![](img/32e4769ba6b759c488707d6fc850d06f.png)

照片由 [Clément Falize](https://unsplash.com/@centelm?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 其他方法

除了`format`方法之外，`formatToParts`和`resolvedOptions`方法也可以在`Intl.NumberFormat`构造函数创建的对象中使用。`formatToParts`方法以数组的形式返回格式化数字的各个部分。`resolvedOptions`方法返回一个新对象，该对象具有格式化数字的选项，这些选项的属性反映了在对象实例化期间计算的区域设置和排序规则选项。

要使用`formatToParts`方法，我们可以编写以下代码:

```
console.log(new Intl.NumberFormat('hi', {
  style: 'decimal',
  useGrouping: true
}).formatToParts(22222222222));
```

然后我们得到:

```
[
  {
    "type": "integer",
    "value": "22"
  },
  {
    "type": "group",
    "value": ","
  },
  {
    "type": "integer",
    "value": "22"
  },
  {
    "type": "group",
    "value": ","
  },
  {
    "type": "integer",
    "value": "22"
  },
  {
    "type": "group",
    "value": ","
  },
  {
    "type": "integer",
    "value": "22"
  },
  {
    "type": "group",
    "value": ","
  },
  {
    "type": "integer",
    "value": "222"
  }
]
```

因为格式化的数字 22，22，22，22，222 被分成几部分，放入数组并返回，所以被记录。

要使用`resolvedOptions`方法，我们可以编写以下代码:

```
const formatOptions = new Intl.NumberFormat('hi', {
  style: 'decimal',
  useGrouping: true
}).resolvedOptions(22222222222)
console.log(formatOptions);
```

这将返回:

```
{
  "locale": "hi",
  "numberingSystem": "latn",
  "style": "decimal",
  "minimumIntegerDigits": 1,
  "minimumFractionDigits": 0,
  "maximumFractionDigits": 3,
  "useGrouping": true,
  "notation": "standard",
  "signDisplay": "auto"
}
```

在`console.log`输出中。上面的代码将得到我们使用的所有选项，格式为上面的数字 2222222222。

JavaScript 能够使用`Intl.NumberFormat`构造函数为非英语用户格式化数字。这意味着我们可以为使用非英语语言环境的人显示数字，而无需添加另一个库来完成这项工作。我们可以用`Intl.NumberFormat`构造函数格式化数字。构造函数有两个参数。

第一个参数是区域设置字符串或区域设置字符串数组。区域设置字符串应该是 BCP 47 语言标记，可以选择附加 Unicode 密钥扩展。由构造函数创建的对象有一个`format`方法，它接受一个我们想要格式化的数字。

当我们允许分组或者我们可以关闭分组时，它会自动对不同地区的数字进行分组，并且我们可以指定要显示的小数位数、有效位数和整数位数。