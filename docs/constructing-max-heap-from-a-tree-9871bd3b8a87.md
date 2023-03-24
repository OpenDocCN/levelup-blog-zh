# 从树中构造最大堆

> 原文：<https://levelup.gitconnected.com/constructing-max-heap-from-a-tree-9871bd3b8a87>

最大堆的根节点包含最大值。每个孩子都比父母小。我们将从之前几篇[文章](/creating-a-heap-from-an-array-75830508b034)中的相同数组开始。

![](img/a0f8440afac67e157df0ae9746137eb8.png)

我们将首先构造堆。

![](img/60d30ab897280c046f31ff311038f252.png)

接下来，我们将开始构建最大堆。就像我们在构建[最小堆](/constructing-min-heap-from-a-tree-ebe20eceb867)时一样，我们将从第三个节点开始:

*楼层(6/2) =楼层(3) = 3*

![](img/ee1e3473ad3deb0654457bf5a185d99a.png)

因为 9 只有一个子节点，所以 9 将与 3 进行比较。如果父节点小于子节点，节点将被交换。因为 9 大于 3，所以节点不交换。我们移动到第二个节点。

![](img/1bbc5863f991fb3b9550e9223f16b6e3.png)

第二个节点有两个子节点。两者中较大的是 5，所以 5 与 2 相比较。因为 5 大于 2，所以两个值互换。

![](img/19e2823f25a3413ba8ee22a5eca5c81c.png)

比较节点递减，我们从第一个节点开始比较。

![](img/cdc0379821103623327a57c0fd68f521.png)

第一个节点有两个子节点。孩子们被比较。因为 9 大于 5，所以 9 与 7 相比较。数字 9 大于 7，因此两个节点被交换。

![](img/0aa7548081e09767ee31201a26884c5a.png)

我们将再次遍历它，以确保达到 Max-Heap。我们将从第三个节点开始。因为父节点 7 比它的子节点大，所以节点保持不变。

![](img/70eff2bd8ad818f2a08d9ecc60a0953a.png)

比较节点递减，第二个节点与其子节点进行比较。因为父节点比它的两个子节点都大，所以节点保持在当前位置。

![](img/425bbf16e682136e54173352355a5335.png)

比较节点递减，第一个节点与其子节点进行比较。因为父节点比它的两个子节点都大，所以节点保持在当前位置。

![](img/62e8bbce2029275f42a6b5bbabb82dbc.png)

由于树没有变化，我们可以得出结论，最大堆树已经创建。

*如果你喜欢你所读的，我的书，*[](https://www.amazon.com/Illustrative-Introduction-Algorithms-Dino-Cajic-ebook-dp-B07WG48NV7/dp/B07WG48NV7/ref=mt_kindle?_encoding=UTF8&me=&qid=1586643862)**算法的说明性介绍，涵盖了这种数据结构和更多内容。**

*![](img/5280ce1e0fa9b63203cd08af64867922.png)*

*迪诺·卡伊奇目前是 [LSBio(生命周期生物科学公司)](https://www.lsbio.com/)、[绝对抗体](https://absoluteantibody.com/)、 [Kerafast](https://www.kerafast.com/) 、[珠穆朗玛生物](https://everestbiotech.com/)、[北欧 MUbio](https://www.nordicmubio.com/) 和 [Exalpha](https://www.exalpha.com/) 的 IT 主管。他还担任我的自动系统的首席执行官。他有十多年的软件工程经验。他拥有计算机科学学士学位，辅修生物学。他的背景包括创建企业级电子商务应用程序、执行基于研究的软件开发，以及通过写作促进知识的传播。*

*你可以在 [LinkedIn](https://www.linkedin.com/in/dinocajic/) 上联系他，在 [Instagram](https://instagram.com/think.dino) 上关注他，或者[订阅他的媒体出版物](https://dinocajic.medium.com/subscribe)。*

*[*阅读迪诺·卡吉克(以及媒体上成千上万其他作家)的每一个故事。你的会员费直接支持迪诺·卡吉克和你阅读的其他作家。你也可以在媒体上看到所有的故事。*](https://dinocajic.medium.com/membership)*