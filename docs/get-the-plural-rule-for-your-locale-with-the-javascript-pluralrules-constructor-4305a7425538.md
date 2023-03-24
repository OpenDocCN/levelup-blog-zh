# 使用 JavaScript PluralRules 构造函数获取您的语言环境的复数规则

> 原文：<https://levelup.gitconnected.com/get-the-plural-rule-for-your-locale-with-the-javascript-pluralrules-constructor-4305a7425538>

![](img/6dea70fb067fd8d76085c001584dd259.png)

照片由[维塔利·神圣](https://unsplash.com/@vitalysacred?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

地区的复数规则是不同的，如果我们为多种语言构建应用程序，很难知道所有的规则。幸运的是，JavaScript 中有一个`Intl.PluralRules`构造函数，在这里我们可以获得您选择的地区的复数语言规则和想要查找的复数规则的类型。

我们可以调整查找中使用的位数，检查数字时使用的整数或小数位数。此外，我们可以选择在使用`Intl.PluralRules`构造函数查找复数规则时使用多少有效数字。

`Intl.NumberFormat`构造函数接受两个参数，第一个是 locales 参数，它接受一个 locale 字符串或一个 locale 字符串数组。这是一个可选参数。它需要一个 BCP 47 语言标签。BCP 47 语言标签的简略列表包括:

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

第二个参数接受一个具有几个属性的对象— `localeMatcher`、`type`、`minimumIntegerDigits`、`minimumFractionDigits`、`maximumFractionDigits`、`minimumSignificantDigits`和`maximumSignificantDigits`。

`localeMatcher`选项指定要使用的语言环境匹配算法。可能的值是`lookup`和`best fit`。查找算法搜索区域设置，直到找到符合正在比较的字符串字符集的区域设置。`best fit`查找至少比`lookup`算法更适合的语言环境。

`type`选项让我们选择要使用的复数规则的类型。可能的值是`cardinal`和`ordinal`。`cardinal`指事物的数量，是默认值。`ordinal`指序数，用于对物品进行排序和排名，像英语中的 1、2、3。

`minimumIntegerDigits`、`minimumFractionDigits`和`maximumFractionDigits`被认为是一组选项。`minimumIntegerDigits`指定要使用的最小整数位数，范围从 1 到 21，默认选项为 1。`minimumFractionDigits`是要使用的最小分数位数，范围从 0 到 20。对于普通数字和百分比格式，默认值为 0。货币格式的默认值由 ISO 4217 货币代码列表指定，如果列表中没有指定，则为 2。`maximumFractionDigits`是分数位数的最大值，取值范围为 0 到 20。普通数字的默认值是`minimumFractionDigits`和 3 之间的最大值。货币格式的默认值是`minimumFractionDigits`和 ISO 4217 货币代码列表提供的小数位数之间的最大值，如果列表不提供该信息，则为 2。百分比格式的默认值是介于`minimumFractionDigits`和 0 之间的最大值。

`minimumSignificantDigits`和`maximumSignificantDigits`被认为是另一组选项。如果至少定义了该组中的一个选项，则忽略第一组。`minimumSignificantDigits`是要使用的最小有效位数，取值范围为 1 到 21，默认值为 1。`maximumSignificantDigits`是要使用的最大有效位数，取值范围为 1 到 21，默认值为 21。

![](img/f5a42f90c717b3107855ba246e62692c.png)

由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 方法

构造的对象有一些方法。有`select`和`resolvedOptions`两种方法。`select`方法返回一个带有复数规则的字符串，并接受一个数字作为参数。返回的规则是根据我们在构造函数中设置的值来计算的，比如区域设置、要获取的复数规则的类型以及设置的数字选项的数量。`resolvedOptions`方法返回一个带有选项的对象，这些选项是我们在对象初始化过程中使用区域设置和排序规则选项计算复数规则时选择的。

例如，我们可以使用`select`方法获得英语的复数规则，如下所示:

```
const rule = new Intl.PluralRules('en', {
  type: 'ordinal'
}).select(1);
console.log(rule);
```

如果上面的代码运行，我们得到一个“一”记录。我们可以对其他数字这样做，如下面的代码:

```
const rule0 = new Intl.PluralRules('en', {
  type: 'ordinal'
}).select(0);
console.log(rule0);
// otherconst rule2 = new Intl.PluralRules('en', {
  type: 'ordinal'
}).select(2);
console.log(rule2);
// twoconst rule3 = new Intl.PluralRules('en', {
  type: 'ordinal'
}).select(3);
console.log(rule3);
// fewconst rule4 = new Intl.PluralRules('en', {
  type: 'ordinal'
}).select(4);
console.log(rule4);
// otherconst rule5 = new Intl.PluralRules('en', {
  type: 'ordinal'
}).select(5);
console.log(rule5);
// otherconst rule6 = new Intl.PluralRules('en', {
  type: 'ordinal'
}).select(6);
console.log(rule6);
// other
```

我们也可以将它用于非英语地区，如下面的代码所示:

```
const rule0 = new Intl.PluralRules('fr', {
  type: 'cardinal'
}).select(0);
console.log(rule0);
// oneconst rule1 = new Intl.PluralRules('fr', {
  type: 'cardinal'
}).select(1);
console.log(rule);
// oneconst rule2 = new Intl.PluralRules('fr', {
  type: 'cardinal'
}).select(2);
console.log(rule2);
// twoconst rule3 = new Intl.PluralRules('fr', {
  type: 'cardinal'
}).select(3);
console.log(rule3);
// fewconst rule4 = new Intl.PluralRules('fr', {
  type: 'cardinal'
}).select(4);
console.log(rule4);
// otherconst rule5 = new Intl.PluralRules('fr', {
  type: 'cardinal'
}).select(5);
console.log(rule5);
// otherconst rule6 = new Intl.PluralRules('fr', {
  type: 'cardinal'
}).select(6);
console.log(rule6);
// other
```

对于返回的字符串，如果需要，我们可以将它映射到相应复数规则的后缀，这样我们就可以将正确的后缀附加到给定的单词或数字上。

要使用`resolvedOptions`方法，我们可以编写如下内容:

```
const options = new Intl.PluralRules('en', {
  type: 'ordinal',
  maxiumSignificantDigits: 1
}).resolvedOptions()console.log(options);
```

当我们运行上面的语句时，我们从上面的`console.log`语句中得到以下内容:

```
{
  "locale": "en",
  "type": "ordinal",
  "minimumIntegerDigits": 1,
  "minimumFractionDigits": 0,
  "maximumFractionDigits": 3,
  "pluralCategories": [
    "few",
    "one",
    "two",
    "other"
  ]
}
```

我们可以看到在构造函数和`pluralCategories`属性中设置的所有选项，这些选项是针对该地区的可能的复数规则。

不同地区的复数规则是不同的。如果我们构建的应用程序能够感知不同的区域，那么很难感知所有的区域。

幸运的是，JavaScript 中有一个`Intl.PluralRules`构造函数，在这里我们可以获得您选择的地区的复数语言规则和想要查找的复数规则的类型。

我们可以调整查找中使用的位数，检查数字时使用的整数或小数位数。此外，我们可以选择在为`Intl.PluralRules`构造函数查找复数规则时使用多少有效数字。

对于给定数量的单词，我们可以使用复数规则来映射到正确的后缀。