# Fe 是下一个智能合同语言吗？

> 原文：<https://levelup.gitconnected.com/is-fe-the-next-smart-contract-language-c9aae3c3ec18>

![](img/61de4dc6c7abfaff8b5e8714aa7d1c12.png)

当我开始编写智能合约时，我总是想知道是否有更好的方式来编写它们，可以避免类似 C++的代码结构。谈到可靠性，你可以很容易地写出基本的合同。在我们进入 *Fe* 到底是什么之前，让我们写一个基本的 HelloWorld 契约，看看它是什么样子的。

这是一个基本的 hello world solidity 合同:

```
// SPDX-License-Identifier: MITpragma solidity ^0.7.0;// Defines a contract named `HelloWorld`.
contract HelloWorld {
   string public message;
   constructor(string memory initMessage) {
      message = initMessage;
   }
   function update(string memory newMessage) public {
      message = newMessage;
   }
   function viewMessage() public view returns(string memory) {
      return message;
   }
}
```

如果你想部署这个契约，你可以在这里点击: [Remix HelloWorld](https://remix.ethereum.org/#version=soljson-v0.7.6+commit.7338295f.js&optimize=false&runs=200&gist=95aa04cfa9faa9589b79acca0a494ad0)

很简单对吧。我不会深入这里，因为有很多教程解释同样的问题。虽然，我想通过结构。喜欢 Python 胜过其他语言的人知道，在编写代码时添加一个丢失的分号或一个大括号是多么令人沮丧。为了用一个更受 python-rust 启发的界面使这变得简单和容易，以太坊的人们开发了 Fe。

Fe 是一种新的编译器，写在铁锈上，元素周期表中表示铁元素的名称描述了耐久性。此外，铁锈会在铁上形成(干得好，Fe 团队)。

别浪费时间了，让我们研究一下 Fe。让我们把 HelloWorld 契约翻译成 Fe。

为了编译 Fe 代码，我们首先需要获取编译器二进制文件。让我们从这里的存储库版本中得到答案: [Fe 版本](https://github.com/ethereum/fe/releases)。您可以在发行版的下面的资源中找到二进制文件。我使用的是 0.10.0 alpha，这是我写这篇文章时的最新版本。

一旦你有了那个，让我们现在打合同。该生锈了。

这是 Fe 中的代码。你可以看到现在比早先是多么容易、简单和简洁。让我们在这里过一遍线。

```
# The `contract` keyword defines a new contract type
contract HelloWorld:
    message: String<100>
    pub fn __init__(self, _message: String<100>):
        self.message = _message
    pub fn update(self, newMessage: String<100>):
        self.message = newMessage
    pub fn getMessage(self) -> String<100>:
        return self.message.to_mem()
```

Github 要点在这里: [Helloworld.fe](https://gist.github.com/adityak74/722b9a7de697962c3d3c25fa94e5463c)

1.  contract 关键字是 Fe 中的一个类型，它指定了我们在这里创建的对象的类型。我们正在使用关键字`contract`初始化一个合同模块。
2.  `message: String<100>`在这里实际上是一个声明，将全局变量消息声明为字符串类型，最大长度限制为 100 个字符。我们键入变量名，后跟一个冒号，指定变量的类型。
3.  init 方法基本上是我们在 python 中看到的构造函数。我们使用相同的符号风格`variable: type`作为参数。注意每个方法中的 python style `self`关键字。
4.  函数`getMessage`实际上返回一个字符串。为了做到这一点，我们使用了箭头关键字，你可以注意到以上。

让我们关注代码的`to_mem()`部分。这对于将被引用的类型值复制到内存中并由协定返回给调用方非常重要。这类似于`string memory`在《坚固性宣言》。这将值从存储器移动到内存。

我们减少到了 8 行代码，并且没有任何麻烦。现在我们有了代码，让我们编译它。

我使用的是 mac，所以`fe_mac`是我将用来编译这个 fe 文件的二进制文件的名称。我们开始吧:

`$ ./fe_mac helloworld.fe`

这会生成一个名为`output`的文件夹。输出文件夹将包含二进制文件和 abi 文件。让我们看看它生成的 abi 文件:

```
[
  {
    "name": "update",
    "type": "function",
    "inputs": [
      {
        "name": "newMessage",
        "type": "string"
      }
    ],
    "outputs": []
  },
  {
    "name": "getMessage",
    "type": "function",
    "inputs": [],
    "outputs": [
      {
        "name": "",
        "type": "string"
      }
    ]
  },
  {
    "name": "",
    "type": "constructor",
    "inputs": [
      {
        "name": "_message",
        "type": "string"
      }
    ],
    "outputs": []
  }
]
```

这是我们的 abi 的混音可靠性代码:

```
[
    {
        "inputs": [
            {
                "internalType": "string",
                "name": "initMessage",
                "type": "string"
            }
        ],
        "stateMutability": "nonpayable",
        "type": "constructor"
    },
    {
        "inputs": [],
        "name": "message",
        "outputs": [
            {
                "internalType": "string",
                "name": "",
                "type": "string"
            }
        ],
        "stateMutability": "view",
        "type": "function"
    },
    {
        "inputs": [
            {
                "internalType": "string",
                "name": "newMessage",
                "type": "string"
            }
        ],
        "name": "update",
        "outputs": [],
        "stateMutability": "nonpayable",
        "type": "function"
    },
    {
        "inputs": [],
        "name": "viewMessage",
        "outputs": [
            {
                "internalType": "string",
                "name": "",
                "type": "string"
            }
        ],
        "stateMutability": "view",
        "type": "function"
    }
]
```

在接下来的文章中，让我们仔细看看如何在以太坊上部署这些 Fe 契约。

如果您对加密货币感兴趣，并且希望将历史数据用于 ML 或任何其他项目，请查看我在这里开发的 API:

*   获取硬币数据:[cryptoviz.adityakarnam.me/coins](https://cryptoviz.adityakarnam.me/coins)
*   获取硬币价格数据(每 5 分钟跟踪一次):[cryptoviz.adityakarnam.me/coinsData](https://cryptoviz.adityakarnam.me/coinsData)

如果你对这个项目感兴趣，你可以在评论中联系我。链接到 CryptoViz API 项目:[github.com/adityak74/cryptoviz](https://github.com/adityak74/cryptoviz)