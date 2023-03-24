# 我去 JQ

> 原文：<https://levelup.gitconnected.com/my-go-to-jq-ca8102c8560a>

![](img/5bc636bb9ef45008f1cfdb24da41b739.png)

作者图片

## JSON，数据，终端，编程

## 信息的力量已经从拥有它的人转移到能够使用它的人。

[JQ](https://stedolan.github.io/jq/) 是一个强大的命令行 JSON 处理器。以下是我作为一名软件工程师在日常工作中一直依赖的一些 JQ 食谱。有了这些，我极大地改进了脚本并解决了生产问题。

[下载](https://stedolan.github.io/jq/) JQ 跟随。

# 方法

## 循环的狂欢

**场景** 您想用 bash 遍历一个 JSON 元素数组，并为每个元素执行一些逻辑。

**示例** 给定下面的 JSON 动物数组(存储在 *animals.json* 中)，循环遍历它们并让每个条目自我介绍。

**实现** 你可以循环遍历 animal 数组，并结合 bash、JQ 和原生 base64 函数对每个元素进行操作。

**Output** 我们在这里只打印简单的消息，但是你可以看到这个模式是如何给你机会对数组的每个元素执行复杂的操作。

```
Hi, I am a Fin Whale and I belong to the Mammal class
Hi, I am a Cuttlefish and I belong to the Cephalopoda class
Hi, I am a Opossum and I belong to the Marsupials class
```

## 串联数组

**场景** 你有多个 JSON 数组，你想把它们组合成一个数组。

**示例** 让我们将 For 循环示例中的每个动物类分离到它自己的数组中，然后将它们连接在一起。

哺乳动物系列:

头足类阵列:

有袋动物阵列:

**实现** 假设这些数组中的每一个都保存在你的工作目录下，扩展名为`*-array.json`。

```
jq -s '.[0]=([.[]]|flatten)|.[0]' *-array.json
```

**输出** 这个数组的输出和我们原来的 animals 数组匹配。

```
[
  {
    "class": "Mammal",
    "name": "Fin Whale",
    "interestingFacts": [
      {
        "fact": "I swim faster than all other whales."
      }
    ]
  },
  {
    "class": "Cephalopoda",
    "name": "Cuttlefish",
    "interestingFacts": [
      {
        "fact": "I have three hearts."
      }
    ]
  },
  {
    "class": "Marsupials",
    "name": "Opossum",
    "interestingFacts": [
      {
        "fact": "I bet you didn't know that I was a marsupial."
      }
    ]
  }
]
```

## 引用父元素

**场景** 你正在遍历一个 JSON 对象，你需要“向上延伸”并使用一个父字段。当您想要枚举子数组并将一些父值应用到子数组的每个元素时，这通常是必需的。

**示例** 你想给每一个有趣的事实添加一个动物的名字和类别。

**实现** 在迭代嵌套的`interestingFacts`数组之前，将数组的 animal 元素保存为`$parent`变量。从`interestingFacts`数组中引用`$parent`变量来完成枚举。

```
jq '[.[] | . as $parent | .interestingFacts[] | { "fact": .fact, "name": $parent.name, "class": $parent.class }]' animals.json
```

**输出** 输出是对有趣事实的动物类和名称的枚举。

```
[
  {
    "fact": "I swim faster than all other whales.",
    "name": "Fin Whale",
    "class": "Mammal"
  },
  {
    "fact": "I have three hearts",
    "name": "Cuttlefish",
    "class": "Cephalopoda"
  },
  {
    "fact": "I bet you didn't know that I was a marsupial.",
    "name": "Opossum",
    "class": "Marsupials"
  }
]
```

## 到简单字符串的转换

**场景** 你需要输出一个基于某个 JSON 对象的值的简单字符串，而不是 JSON 对象本身。

**示例** 你想在一行中输出一个动物的名字和类别。

**实现** 将每个 JSON 元素转换成一个字符串。

```
jq -r '.[] | "\(.name): \(.class)"' animals.json
```

**Output** 将元素转换成简单的字符串通常很有帮助，因为它可以创建一个清晰的、非技术性的报告。

```
Fin Whale: Mammal
Cuttlefish: Cephalopoda
Opossum: Marsupials
```

# 谢谢你

感谢您抽出时间阅读本文。我希望你发现它是有帮助的，并且你在你自己的工作中结合一些这些食谱！

如您所知，我们这些参与中型合作伙伴计划的人拥有不到 100 名关注者(就是我！)可能会在新的一年(2022 年)从节目中移除。我天生就不喜欢张扬，但在这种情况下，我需要你的帮助。如果你喜欢我在这篇文章或我的任何其他文章中所说的，请给我一个关注！

乔娜，节日快乐