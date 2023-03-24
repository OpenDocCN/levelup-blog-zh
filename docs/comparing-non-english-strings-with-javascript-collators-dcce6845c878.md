# 用 JavaScript 排序器比较非英语字符串

> 原文：<https://levelup.gitconnected.com/comparing-non-english-strings-with-javascript-collators-dcce6845c878>

![](img/c1bc6c2492694d34a6293145f769f045.png)

照片由[凯尔·格伦](https://unsplash.com/@kylejglenn?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

通过将两倍等于或三倍等于操作符与字符串方法相结合，我们可以很容易地以区分大小写或不区分大小写的方式比较字符串。然而，这没有考虑非英语字符串中的字符，如法语或意大利语。这些语言的字母可能包含重音符号，这在普通的字符串比较中是无法识别的。

为了处理这个场景，我们可以使用`Intl.Collator`对象来比较带有重音符号的字符串或者不同地区的字符串。对象是排序器的构造器，排序器是让我们以语言敏感的方式比较字符的对象。使用排序器，我们可以根据指定的语言比较单个字符的顺序。

# 字符串相等比较的基本排序器用法

要使用排序器，我们可以构造一个排序器对象，然后使用它的`compare`方法。`compare`方法根据地区对整个字符串的字母顺序进行比较。例如，如果我们想使用德语字母表的顺序比较两个字符串，我们可以编写以下代码:

```
const collator = new Intl.Collator('de');
const order = collator.compare('Ü', 'ß');
console.log(order);
```

我们通过编写`new Intl.Collator(‘de’)`来指定我们正在比较德语字母表中的字符串，从而创建了 Collator 对象。然后我们使用创建的`compare`方法，该方法将两个参数作为您想要以字符串形式比较的两个字符串。

然后从`compare`函数返回一个数字。如果第一个参数中的字符串按字母顺序排在第二个之后，则返回`1`，如果两个字符串相同，则返回`0`，如果第一个参数中的字符串按字母顺序排在第二个字符串之前，则返回`-1`。

因此，如果我们像下面的代码那样颠倒字符串的顺序:

```
const collator = new Intl.Collator('de');
const order = collator.compare('ß', 'Ü');
console.log(order);
```

然后`console.log`输出-1。

如果它们相同，如下面的代码所示:

```
const collator = new Intl.Collator('de');
const order = collator.compare('ß', 'ß');
console.log(order);
```

然后我们为`order`返回 0。

**总结:**如果字符串相等，函数返回`0`。如果不相等，函数返回`1`或`-1`，这也表示字符串的字母顺序。

# 高级用法

排序器很有用，因为我们可以把它作为回调函数放在`Array.sort`方法中，对数组中的多个字符串进行排序。例如，如果数组中有多个德语字符串，如下面的代码所示:

```
const collator = new Intl.Collator('de');
const sortedLetters = ['Z', 'Ä', 'Ö', 'Ü', 'ß'].sort(collator.compare);
console.log(sortedLetters);
```

然后我们得到`[“Ä”, “Ö”, “ß”, “Ü”, “Z”]`。

该构造函数采用了许多选项，这些选项考虑了不同语言的字母表的特征。正如我们在上面看到的，构造函数中的第一个参数是 locale，它是 BCP-47 语言标签，或者是这种标签的数组。这是一个可选参数。BCP-47 语言标签的简略列表包括:

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

例如，`de`代表德语，`fr-ca`代表加拿大法语。因此，我们可以通过运行以下代码对加拿大法语字符串进行排序:

```
const collator = new Intl.Collator('fr-ca');
const sortedLetters = ['ç', 'à', 'c'].sort(collator.compare);
console.log(sortedLetters);
```

Collator 的构造函数也可以接受一个字符串数组，用于多地区比较— `new Intl.Collator([/* local strings */])`。array 参数允许我们对来自多个地区的字符串进行排序。例如，我们可以同时对加拿大法语字母表和德语字母表进行排序:

```
const collator = new Intl.Collator(['fr-ca', 'de']);
const sortedLetters = [
  'Ü', 'ß', 'ç', 'à', 'c'
].sort(collator.compare);console.log(sortedLetters);
```

然后我们从`console.log`语句中得到`[“à”, “c”, “ç”, “ß”, “Ü”]`。

![](img/d3045172cd652b319d6d45ef2fe1ca42.png)

[澳门图片社](https://unsplash.com/@macauphotoagency?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 附加选项

Unicode 扩展键包括`"big5han"`、`"dict"`、`"direct"`、`"ducet"`、`"gb2312"`、`"phonebk"`、`"phonetic"`、`"pinyin"`、`"reformed"`、`"searchjl"`、`"stroke"`、`"trad"`、`"unihan"`在我们的区域设置字符串中也是允许的。它们指定了我们想要用来比较字符串的排序规则。但是，当第二个参数的选项中有与此重叠的字段时，参数中的选项将覆盖第一个参数中指定的 Unicode 扩展键。

可以通过在第一个参数中将`kn`添加到您的区域设置字符串中来指定数字排序规则。例如，如果我们想比较数字串，那么我们可以写:

```
const collator = new Intl.Collator(['en-u-kn-true']);
const sortedNums = ['10', '2'].sort(collator.compare);
console.log(sortedNums);
```

然后我们得到了`[“2”, “10”]`,因为我们在构造函数的 locale 字符串中指定了`kn`,这使得 collator 比较数字。

此外，我们可以用`kf`扩展键指定是先排序大写字母还是小写字母。可能的选项有`upper`、`lower`或`false`。`false`表示区域设置的默认值将是选项。该选项可以在区域设置字符串中通过添加 Unicode 扩展键来设置，如果两者都提供了，那么`option`属性将优先。例如，要使大写字母优先于小写字母，我们可以写:

```
const collator = new Intl.Collator('en-ca-u-kf-upper');
const sorted = ['Able', 'able'].sort(collator.compare);
console.log(sorted);
```

这将首先对大写字母相同的单词进行排序。当我们运行`console.log`时，我们得到`[“Able”, “able”]`，因为我们在‘Able’中有一个大写的‘A’，在‘Able’中有一个小写的‘A’。另一方面，如果我们改为在构造函数中传递`en-ca-u-kf-lower`，如下面的代码所示:

```
const collator = new Intl.Collator('en-ca-u-kf-lower');
const sorted = ['Able', 'able'].sort(collator.compare);
console.log(sorted);
```

然后在`console.log`之后，我们得到`[“able”, “Able”]`，因为`kf-lower`意味着我们将小写字母的同一个单词排在大写字母的前面。

构造函数的第二个参数接受一个可以有多个属性的对象。对象接受的属性有`localeMatcher`、`usage`、`sensitivity`、`ignorePunctuation`、`numeric`和`caseFirst`。`numeric`与区域设置字符串中 Unicode 扩展键的`kn`选项相同，`caseFirst`与区域设置字符串中 Unicode 扩展键的`kf`选项相同。`localeMatcher`选项指定要使用的语言环境匹配算法。可能的值是`lookup`和`best fit`。查找算法搜索区域设置，直到找到符合正在比较的字符串的字符集的区域设置。`best fit`找到至少比`lookup`算法更适合的语言环境。

`usage`选项指定排序器是用于排序还是搜索字符串。默认选项是`sort`。

敏感度选项指定比较字符串的方式。可能的选项有`base`、`accent`、`case`和`variant`。

`base`比较字母的基数，忽略重音。例如`a`与`b`不同，而`a`与`á`相同，`a`与`Ä`相同。

`accent`指定一个字符串只有在有一个基本字母或它们的重音不相等时才是不同的，那么它们就是不相等的，忽略大小写。所以`a`和`b`不一样，但是`a`和`A`一样。`a`和`á`不一样。

`case`选项指定基本字母或大小写不同的字符串被视为不相等，因此`a`不会与`A`相同，`a`不会与`c`相同，但`a`与`á`相同。

`variant`表示基本字母、重音、其他标记或大小写不同的字符串被视为不相等。例如`a`不会与`A`相同，而`a`不会与`c`相同。但是`a`也不等同于`á`。

`ignorePunctuation`指定排序字符串时是否应忽略标点符号。这是一个布尔属性，默认值是`false`。

我们可以按以下方式使用带有第二个参数的排序器构造函数:

```
const collator = new Intl.Collator('en-ca', {
  ignorePunctuation: false,
  sensitivity: "base",
  usage: 'sort'
});
console.log(collator.compare('Able', 'able'));
```

在上面的代码中，我们通过检查标点符号进行排序，只有在基本字母不同的情况下才考虑字母的不同，我们保留大写字母首先排序的默认设置，因此我们在`console.log`中得到`[‘Able’, ‘able’]`。

我们可以按如下方式搜索字符串:

```
const arr = ["ä", "ad", "af", "a"];
const stringToSearchFor = "af";const collator = new Intl.Collator("fr", {
  usage: "search"
});
const matches = arr.filter((str) => collator.compare(str, stringToSearchFor) === 0);
console.log(matches);
```

我们将`usage`选项设置为`search`来使用排序器搜索字符串，当`compare`方法返回`0`时，我们知道我们有相同的字符串。所以当我们运行`console.log(matches)`时，我们得到`[“af”]`日志。

我们可以调整比较字母的选项，所以如果我们有:

```
const arr = ["ä", "ad", "ef", "éf", "a"];
const stringToSearchFor = "ef";const collator = new Intl.Collator("fr", {
  sensitivity: 'base',
  usage: "search"
});const matches = arr.filter((str) => collator.compare(str, stringToSearchFor) === 0);console.log(matches);
```

然后我们在`console.log`中得到`[“ef”, “éf”]`,因为我们将`sensitivity`指定为`base`,这意味着我们认为具有相同基本重音的字母是相同的。

此外，我们可以指定 numeric 选项来对数字进行排序。例如，如果我们有:

```
const collator = new Intl.Collator(['en-u-kn-false'], {
  numeric: true
});
const sortedNums = ['10', '2'].sort(collator.compare);
console.log(sortedNums);
```

然后我们得到`[“2”, “10”]`，因为第二个参数中对象的`numeric`属性覆盖了第一个参数中的`kn-false`。

# 结论

JavaScript 提供了很多字符串比较选项来比较非英语的字符串。`Intl`中的 Collator 构造函数提供了许多选项，让我们可以用普通的比较操作符(如 double 或 triple equals)无法完成的方式来搜索或排序字符串。它让我们对数字进行排序，并考虑大小写、重音、标点符号或每个字符中这些特征的组合来比较字符串。此外，它接受带有键扩展名的区域设置字符串进行比较。

所有这些选项使 JavaScript 的排序器构造函数成为比较国际字符串的最佳选择。