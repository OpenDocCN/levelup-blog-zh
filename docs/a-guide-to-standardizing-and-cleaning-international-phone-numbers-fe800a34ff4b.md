# 清除电话号码的终极指南

> 原文：<https://levelup.gitconnected.com/a-guide-to-standardizing-and-cleaning-international-phone-numbers-fe800a34ff4b>

电话号码是常用的数据类型。所以你会认为他们很容易相处，对吗？对吗？

好吧，如果你有来自多个国家和个人的电话号码，那就太混乱了。

你可能会发现自己有一个电子表格，里面有一堆不同的电话号码，而你却不知道如何清理它们。

如果你想要一个快速的方法来做这件事，看看[干净的电子表格](http://www.cleanspreadsheets.com)！

然而，如果你试图自己编码或者手动清理它们，这里是你需要记住的。

# **清除一个电话号码能做什么？**

清除电话号码将允许您确定该号码是否有效，并将其分成各个组成部分，如国家代码、区号和用户号码。

# 这不是很简单吗？

你可能会这么想，但是不同的国家有不同的习俗，这很快就会失控。

为了避免这篇文章太长，我们将只关注 [G20 国家](https://en.wikipedia.org/wiki/G20)。世界前 19 大经济体加上所有欧盟国家。

# 国家代码

世界上的每个国家(以及一些地区)都有一个 1、2 或 3 位数的国家代码。这以加号(+)为前缀。

官方的 [E.123 指南](https://en.wikipedia.org/wiki/E.123)建议在存储国际电话号码时包含国家代码。但是，在国内拨号时，这不是必需的。

国家代码(如果出现的话)将位于电话号码的开头，很可能与电话号码的其余部分分开

![](img/1849b73483982ae258832791de0ae12a.png)

# 电话号码的其余部分

除了国家代码之外，电话号码的约定也因国家而异。

让我们来看看 G20 的每一个国家。

# 美国

![](img/4e22c9fd95984b8182f16729226bc91a.png)

开始，美国和其他 24 个国家/地区共享北美编号计划(NANP ),格式如下:

![](img/e0032baa23bda7bc0916617705876384.png)

有一个 3 位数的区号 **(AAA)** ，后面是 7 位数的用户号码。 **N** 只能包含从 2 到 9 的数字。

> *NANP 的国家部分包括:*美属萨摩亚、安圭拉、安提瓜和巴布达、巴哈马、巴巴多斯、百慕大、英属维尔京群岛、加拿大、开曼群岛、多米尼克、多米尼加共和国、格林纳达、关岛、牙买加、蒙特塞拉特、北马里亚纳群岛、波多黎各、圣基茨和尼维斯、圣卢西亚、圣文森特和格林纳丁斯、圣马丁岛、特立尼达和多巴哥、特克斯和凯科斯群岛以及美属维尔京群岛

美国的国家代码是+1。

# 墨西哥

![](img/dc1f02b50c54d77413ff603825a4c043.png)

在墨西哥，数字是 10 位数，第一个数字是 2-9。区号可以是 2 或 3 位数，后面是 8 或 7 位数的本地号码。

格式是:

*   AA NNNN XXXX 或(AA) NNNN XXXX

![](img/49d4f2c39ed83e9c1d51041b203ed31f.png)

*   AAA NNN XXXX 或(AAA) NNN XXXX

![](img/a9ef3050d15a1265548f9a557a3bb586.png)

国家代码是+52。

# 阿根廷

![](img/d4c69bf55af579b1a6bebad5ebac1cd1.png)

阿根廷的电话号码有 11 位数字。区号总是以 0 开头，但可以是 3-5 位数字。

电话号码通常被格式化为:

*   (0AA) NNNN-XXXX

![](img/e2dee81cf4249867b87286b3ca0ce3bf.png)

*   NNN-XXXX

![](img/f3e47a30a5a937347d0daf2109240363.png)

*   (0AAAA) NN-XXXX

![](img/05cac640a29865bb7037c958dcc18669.png)

+54 是国家代码，它代替了 0。

# 巴西

![](img/6d839e7cedc37d79b104a6ff9a2e266f.png)

**座机**

所有座机均为 10 位数字，格式为:

*   AA NXXX-XXXX *(N 是从 2 到 5 的数字)*

![](img/715e558d52706fb2b3c378b2b5780868.png)

**移动**

同时，所有手机都是 11 位数字，遵循以下格式:

*   AA NXXXX -XXXX *(N 是从 6 到 9 的数字)*

![](img/63d218bf9585b64ab7975418fe5dba87.png)

国家代码是在号码前面加上+55。

# 南非

![](img/c0bab8419f8855f819d66e3ccd877dc7.png)

对于本地电话，它以中继前缀 0、2 位区号和 7 位用户号码开始。对于国际电话，有一个+27，而不是 0。

*   0AA XXX XXXX

![](img/63beeedf33d34ea38c0c200dc2523bfc.png)

*   +27 AA XXX XXXX

![](img/e18b2d5b93591e3a9bc63a305d91cf3a.png)

# 中国

![](img/5914d529ce6ab46a17fdf219ab1a90ca.png)

根据地区或电话类型，号码可以是 10 位或 11 位。

**座机**

*   0aa NNNN XXXX

![](img/709b20008c50e64ed4df0a98a6336107.png)

区号可以是 2 或 3 位数，本地号码可以是 7 或 8 位数，具体取决于所在地区。

**移动**

遵循 1WX NNNN XXXX 格式的 11 位数

![](img/76a75d5e48041fee1888120d493454a3.png)

WX 是服务提供商，W 总是 3-9 之间的数字

NNNN 是确定区域的 HLR 代码，XXXX 是用户号码。

**国际**

删除 0 并添加+86

# 印度

![](img/f5b6ad23ac6812cae2b91799b2a7979e.png)

电话号码是 10 位数，不包括任何时候都需要的 0。数字也分为几类:

**座机**

*   AAA-NNNNNNN

![](img/8b092d89158813a8a01449675de9355f.png)

AAA 是用户中继拨号代码或长途代码，长度可以是 2-4 位数。NNNNNNN 是电话号码。

**手机**

*   AAAAA。

非本地电话需要以 0 或+91 为前缀，这适用于整个印度和国际。

![](img/15b900299ad7a2650fdf24bd84ca72fb.png)

# 日本

![](img/59c4a97213652639b94d880b00d9ec8f.png)

日本类似于我们在北美看到的，但是有一个 0 的长途区号和+81 的国际区号。

有些地区的区号或长或短，但常见的格式是:

*   0AA NNN-XXXX

![](img/de2ecb4ce08f3b76dc4593eb0b257f2f.png)

# 韩国

![](img/6ced016649d82665e22ee3d84f972586.png)

电话号码可以是 7-11 位数，这取决于您是否拨打不需要区号的本地电话。

**座机**

根据城市的不同，区号可以是 2-3 位数，但格式是:

*   0AA-NNN-XXXX 或
*   (0AA) NNN-XXXX 或
*   0AA NNN XXXX

![](img/ae63d4b028c7bb47f0616f0f317a9bd8.png)

**移动**

对于移动电话，它是一个 8 位数的用户号码，开头是 01 和区号:

*   01A-NNNN-XXXX

![](img/893122923e4a9f02867d15e5e744aaba.png)

国家代码是+82，在打国际电话时代替 0。

# 印度尼西亚

![](img/6d2428fa81e1af371b5981b188d2ce35.png)

座机使用区号，而手机不使用。

**座机**

打国内电话时，电话以 0 开头，打国际电话时以+62 开头。数字长度为 7 或 8 位数。

*   AA NNNN-XXXX

![](img/ff6fed183cddde3a794d2f1b90ed4f1b.png)

**手机**

号码长度为 8-12 位，包括一个中继前缀 0，后跟 8NNN，即移动前缀。

*   0 8NNN XXX XXXX

![](img/70af6ba808df79bc1dfdb1a18850ec47.png)

# 沙特阿拉伯

![](img/8c9b66dfd7ee4fa8179e17b3d336a2d4.png)

电话号码是 7 位数，有一个 3 到 5 位数的区号，包括中继前缀 0。国际代码是+966，它代替了 0。

*   0AA NNN XXXX

![](img/4e076a2ef8bc860bc0cdb507d4ef5c97.png)

# 澳大利亚

![](img/398ca753ed3b415b1656b647dcc372cf.png)

数字是 10 位数，写为

*   NNNN·XXXX，国际电话+61

![](img/0c9f3b7b51e008e9dac706c707c5ebe1.png)

对于本地电话，区号是可选的。

# 火鸡

![](img/ec44be1c18ff161955805659d7459a65.png)

国际电话号码的中继前缀为 0，国家代码为+90。在土耳其境外拨打电话时，0 会被挂断。

土耳其数字的常见格式是

*   0aa NNN XX NN

座机的前缀可以是 02、03 或 04

而手机号码有 05

![](img/18eec0b433542928cc2ed56a2f0b1d4a.png)

# 欧洲联盟

## 奥地利

![](img/cd9e404189e5e14ca8649668162cb672.png)

奥地利和其他一些国家是开放编号计划的一部分，因此区号或用户号码没有标准长度，但总共会有 4-13 位。

国际代码是+43。

**座机**

*   1 NNN·XXXX

![](img/ae6223a8e69fc58e926273bd23f438d3.png)

**手机**

电话号码以数字 6 开头，格式可以是:

*   6AA NNN XXXX

![](img/ccf85e315f99f2c3318d846571b1d152.png)

## 保加利亚

![](img/e4b4346c67a062b5f2dc70148e49dc75.png)

号码长度为 8 或 9 位数，带有一个额外的区号，中继前缀为 0 或国家代码为+359。

**座机**

*   NNN·XXXX

![](img/e5c50193618a0b84413da11f5d487a9d.png)

**手机**

*   0aa NNN XXXX

![](img/c9d2470788909a15ae992f43879eff62.png)

## 比利时

![](img/d302f0d62cb53819d5d12685bdd2212b.png)

电话号码将以 0 和“区号前缀”开头，对于座机，区号前缀可以是 1-2 位数，对于移动电话，区号前缀可以是 3 位数。

**座机**

根据地区不同，前缀号码可以写成

*   0AA NN NN NN or
*   NNN NN NN

![](img/34a70e3c7297388f117ed576ca0da9e9.png)

**移动**

区域前缀的第一个数字总是 4，格式可以写成:

*   04AA NN NN NN

![](img/401ee9448d326dab6ddcbbc7d0efb78c.png)

有时，在区号前缀和用户号码之间会有一个斜线或圆点，例如

*   04AA/NN NN NN or
*   04AA/NN。NN.NN or
*   04AA/NNN。NNN

![](img/f8e475874ae84f257ae5df1510aea2b3.png)

国际代码前缀是+32，去掉 0。

## 克罗地亚

![](img/17fced8afedb2d5c189d1cf7096ecf7d.png)

典型的格式与我们以前见过的一些非常相似。号码通常是 10 位数字，包括中继前缀 0。

*   (0AA) NNN XXXX

![](img/390dbca326a7838675a64f3c874e9ea8.png)

如果你在同一个地区，就不必拨区号。

国家代码是+385

## 塞浦路斯

![](img/df3f7ff3ed55700202b4267fd20bcfed.png)

**座机**

数字以 2 开头，格式如下:

*   2A-XXXXXX

![](img/67ec9c886f45bc24c8cafc6fc2b744b1.png)

**手机**

数字以 9 开头，但格式相同:

*   9A-XXXXXX

![](img/5a7b12ed2562067836761caf9b6bdf44.png)

国际代码是+357，加在号码前面。

## 捷克

![](img/e13f14ec86a6601ddffb2c74e13637b9.png)

典型的格式是 9 位数字，如 AAA NNN XXX，国际代码是+420。

![](img/4866fd5501fabcf532caa5a90572646a.png)

## 丹麦

![](img/5eae142982ce0cffdbba547435d0c855.png)

号码是 8 位数，没有区号。国际代码是+45，常用格式是:

*   NN NN NN NN

![](img/3733704b25dab2f34195ea084d71a575.png)

*   NNNN·XXXX

![](img/35923fc2bf02bb173eafa4a0b54063b0.png)

*   NNNNNNNN

![](img/660ac4c30eb18e023237b75d24cf44c8.png)

## 爱沙尼亚

![](img/377b4532bc5e88bddfb60227ae9cf48a.png)

爱沙尼亚有一个开放的编号计划，号码通常是 7 到 12 位。没有区号，典型的格式是:

**座机**

*   NNN·XXXX

![](img/099db03eae08ab7fa066ed9ebb6c2e23.png)

**移动**

*   NNNN·XXXX

![](img/9e85d0312e1d8600d7e040e9b584824b.png)

国家代码是+372

## 芬兰

![](img/2dd1ba61b5de64e39f7fc51d6bbd8e75.png)

这是另一个具有开放编号计划的国家，号码可以是 5 到 12 位，中继前缀为 0，国际代码为+358，将取代 0。

**座机**

所有座机都以 09 开头，格式如下:

*   09A NNNN XXXX

![](img/0eaa4634682173ee7b631d318211b56a.png)

**移动**

根据移动运营商的不同，号码可以以 04 或 05 开头，例如:

*   04A NNN XXXX

![](img/173cab3d273e37208397e3a366b2b1f6.png)

## 法国

![](img/92d197830fdd267cfa78895fabaf8c22.png)

有 10 个数字，可以写成

*   NN NN NN NN 或

![](img/936fdfa75c0ff456c512f5ed69b2b18b.png)

*   得一个例子。嗯嗯嗯嗯

![](img/74e3db243385c7035ce1ff955bf35550.png)

区号是 1-5 之间的数字

对于国际电话，该格式会删除 0 并添加+33

## 德国

![](img/a338a3cda14594329088f2efcd2b0733.png)

德国有一个开放的号码计划，但通常是 10-12 位数，中继前缀为 0。国家代码是+49，代替了 0。

常见的格式是:

*   (0AA) NNNN-XXXX

![](img/c67d278f1247c33a58113367aadcdf99.png)

## 希腊

![](img/a8b6014090af0ddaece3511d1674a602.png)

电话号码通常是 10 位数，但区号是 2 或 3 位数，写为:

*   AAN XXXXXXX 或
*   AAAN XXXXXX

![](img/290c3208aa0fdde6bd35760156a3d42b.png)

国际代码是+30，放在号码的前面。

## 匈牙利

![](img/56c442fcf6aa4a24c3a22d48dda2227c.png)

区号是 2 位数，用户号码是 6 位数。手机号码和布达佩斯的电话号码总共是 7 位数。国家代码是+36。

![](img/ecbc5ff6dd6fd40778e78a455bfd0ff9.png)

## 爱尔兰

![](img/27f3f9567819458065f11f2d78327a04.png)

爱尔兰也有一个开放的编号计划。

区号以中继前缀 0 开始，可以是 2 到 4 位数字，后面是 7 位数字的本地电话号码。

本地号码可以格式化为:

*   5 位数:NNNNN
*   6 位数字:NNN XXX
*   7 位数号码:NNN·XXXX

通常不鼓励使用连字符，手机号码通常采用以下格式:

*   08A NNN XXXX

![](img/1ee6da61ebbf430eab9d47561cdacf54.png)

国家代码是+353

## 意大利

![](img/30675dc120b31fdf90c282ad53c48f40.png)

另一个国家有开放的电话号码计划，但号码范围可以从 6-11 位。

国家代码是+39。

**座机**

电话以 0 开头，后面是区号。该格式的一个例子是:

*   NNNXXXX

![](img/36504542a13416c18b8c6801f18ba5db.png)

**移动**

数字以 3 开头，通常为 10 位数，格式如下:

*   3AA NNNXXXX

![](img/04257bfb3e3726a02b3b2a08092601c3.png)

## 拉脱维亚

![](img/c496abba3b2b4b2677da7a03538bfb9e.png)

国家代码是+361

**手机**

电话都以 2 开头，格式如下:

*   2AA NN XXX

![](img/a5a67c0a773946bb8ee934fdf7e841a0.png)

**座机**

座机号码可以以 5、6 或 7 开头，格式如下:

*   5 AA NNXXX

![](img/1a3be29b4e8f47873bf8361b5baf76cd.png)

## 立陶宛

![](img/af20ceb113590ac430f0dce44e5528ef.png)

根据城镇或城市的大小，区号是 2 到 5 位数字，后面是 8 位数字的国家或地方号码。

每个数字都以 8 开头。

国家代码是+370

一些常见的格式包括:

*   (8A)NNN·XXXX
*   (8AA) NN XXXX

![](img/1d5d930f6eb6312edfb27a3f095d69c1.png)

## 卢森堡

![](img/271e3278c6e767975208c9623bd5788e.png)

移动电话有一个 3 位数的网络代码，后跟 6 位数的用户号码，例如:

*   6X1 NNN XXX

![](img/6472d36946f466594ef8aec3e1e9e215.png)

手机号码总是以 6 开头，国家代码是+352

## 马耳他

![](img/15e415ecfc9ed5cb3efd54fc281ad0a5.png)

前缀为 21，国际代码为+356，例如:

*   21XX NNNN

![](img/84f87941df4932a1ad6aedcd451f13ec.png)

**移动**

根据移动运营商的不同，电话的前缀为 99、79 等

*   99XX NNNN

![](img/19cedf23a5ff304c8387cd2e8169a23c.png)

## 荷兰

![](img/bfd3c343b864be26360c11e2f560b5bd.png)

号码是 10 位数，中继前缀为“0”。

**座机**

*   0AA-NNNNNNN 或
*   0AAA-NNNNNN

![](img/147b9c1273d079ebeb08ed8d5a1cc00b.png)

**手机**

电话有一个 1 位数的区号 6 和 8 位数的用户号码:

*   06-NXXXXXXX

![](img/7fdf8da3d1e3d56f924fc22573e98065.png)

n 从来不是 6 或 7

国家代码是+31，代替了 0

## 波兰

![](img/470b9e85f34bda393d9ec33534fbce26.png)

电话号码是 9 位数，国家代码是+48，在号码前面。

**手机**

*   AAA-NNN-XXX

![](img/841eaf87b9fc0ca3959f820d9c0ccca5.png)

**座机**

*   AA-NNN-XX-NN 或
*   (AA)NNN-XX-NN

![](img/5e1048d086e8ec34e15924a9b9c692e0.png)

## 葡萄牙

![](img/929ce59abcf4f326cceb64bcfcd7253f.png)

与波兰类似，数字是 9 位数，写为 AAA AAA AAA

所有手机号码都以 9 开头，国家代码是+351，放在号码前面。

![](img/0a492f28651e19d65c13874902cdf44f.png)

## 罗马尼亚

![](img/59e5aae4e4edb75b9bb0a983d01bc3ec.png)

电话号码长度为 10 位数，不包括中继前缀 0。

手机和座机都遵循相同的格式:

*   0AAA-XXX-XXX

![](img/14709ab16be7538e84e5dcd36d249b04.png)

国家代码是+40，放在号码的前面。

## 俄罗斯

![](img/6fcfe7f6b71b066c3b19bb4ed39e8de8.png)

正如我们之前所见，一些欧洲国家通常采用开放的编号方案。

在俄罗斯，号码是 10 位数，中继前缀是 8。

区号因地区而异，但都是 3-5 位数，用户号码用破折号分隔。

常见的格式是

*   8 (AAA) NNN-NN-XX

![](img/766d7ef1d7d59103a806dcbf16361f78.png)

对于国际电话，国家代码是+7

## 西班牙

![](img/1836a4793418cdcd78fb94b8c0a58726.png)

号码是 9 位数，座机以 9 或 8 开头，手机以 6 或 7 开头。

数字可以写成

*   AA NNN XX NN or
*   AAA NNN XXX 取决于区号的长度。

![](img/7406d70ce8f40ac151aa0b9f4588862f.png)

国家代码是+34，放在号码的前面。

## 斯洛文尼亚

![](img/b58dac2b80c8bbd524b68e2cbee1b413.png)

所有号码都是 9 位数，包括中继前缀 0。

国家代码是+386，代替了 0。

区号可以是 1-3 位数字，可能的格式为:

*   NNN XX XX 或
*   (0AA) NNN XXX 或
*   (0AAA) NN XXX

![](img/f236def39f4545314adfdd1475f58845.png)

## 瑞典

![](img/8a9c12f1510d7a29311a70df6a2fd909.png)

瑞典有一个开放的编号方案，因此号码格式因长度而异，但中继前缀为 0。国家代码是+46，拨打国际电话时代替 0。

**10 位数**

*   NNN XXX XX 或
*   0AA-NNN XX XX 或
*   0AAA-NN XX XX

![](img/e3e1d8b8fdab578c6fa7cccf404ea79b.png)

**9 位数字**

*   NNN XX XX 或
*   0AA-NNN XX XX 或
*   0AAA-NNN XX

![](img/ce189aedc79c03ed71824c6fd19945bc.png)

**8 位数字**

*   0A-NN XX XX 或
*   0AA-NNN XX

![](img/e72d623aae4dcad628184b567591a69a.png)

**移动**

数字是 10 位数，并且总是以 7 开头。比如:

*   07A-NNN XX XX 或
*   07AA-NN XX XX

![](img/e47e0daaed3826e17e72b8ebff4d249d.png)

## 联合王国

![](img/e6c3004f6c324c3589231bb15efc1090.png)

由于有一个开放的编号方案，编号的长度范围从 7 到 10 位。

中继前缀是 0，国家代码是+44，当从英国以外的地方打电话时，它代替 0

常见的格式是:

*   NNN·XXXX

![](img/154c790030c4eba302aa204e7c7fb624.png)

**移动**

手机以 7 开头，因手机供应商而异，但通用格式是:

*   07aa NNN XXX

![](img/82cc89edf5041b5a670aae9d32eeb221.png)

# 结束语

你可以使用这个指南来实现从你的数据库、编程语言或电子表格中清除电话号码。

如果您需要清理电子表格中的号码，您可以使用我们的工具[清理电子表格](http://www.cleanspreadsheets.com/)来自动清理和转换电子表格中的任何电话号码。

如果你想要一个定制的应用程序，数据清理，或使用电子表格建立项目，你可以在这里查看我们的咨询服务:[https://www.lovespreadsheets.com](https://www.lovespreadsheets.com/)！

数据清理快乐！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰更多内容请查看[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)