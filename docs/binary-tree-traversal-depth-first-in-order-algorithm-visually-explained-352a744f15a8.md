# 二叉树遍历:深度优先—有序算法直观解释

> 原文：<https://levelup.gitconnected.com/binary-tree-traversal-depth-first-in-order-algorithm-visually-explained-352a744f15a8>

![](img/10449c928321d79c63cddf57375acae4.png)

深度优先二叉树遍历有三种类型:

*   预购:
*   依次:
*   后置顺序:

我们将关注有序二叉树的遍历。我们可以看到，从根节点开始，我们将访问左侧子树中的所有节点，返回到根节点，然后访问右侧子树中的所有顶点。

![](img/4a1b6ffb64e6533d396b763e453aee17.png)

在每个节点的旁边，我们会为自己放置几个提醒:

*   左子树
*   访问/打印节点
*   右子树

![](img/e001ef1ad2a268e383f8c7e24172aa9d.png)

我们将从根节点开始。

![](img/fcdcdb5f3857eb8501977f2183e81a5d.png)

此时，我们可以划掉左子树标签，开始向下移动左子树。

![](img/4c47f674535e028bc13d9d708dcd271b.png)

由于顶点 B 也有一个左子树，根据有序遍历( <left><root><right>)所概述的规则，接下来我们必须访问它的左子树。所以，我们划掉顶点 B 上的左子树标签，移到顶点 d。</right></root></left>

![](img/c05f1c4555b0b1f769af7469afccbaea.png)

顶点 D 也有一个左子树，所以我们接下来访问它的左子树。我们划掉顶点 D 上的左子树标签，移到顶点 h。

![](img/5a897eadfc32d604e8a7dfcfa9fd047f.png)

我们可以很快看到，顶点 H 没有任何其他左子树。我们可以安全地把它划掉。

![](img/d11b0a839048d1ac3445b0f0ec7b5667.png)

我们将返回访问/打印节点 h。

![](img/5907f30b6a130d84fb41e313d6505a5f.png)

试图访问节点 H 的右子树，但是顶点 H 没有右子树。右边的子树标签被划掉，我们返回到节点 D。在访问了它的左子树之后，我们现在正在访问节点 D。我们可以将节点 D 添加到输出中。

![](img/0094f094e4e990aa3e5755c7503ef701.png)

我们移动到节点 D 的右子树。节点 D 的右边子树中只有一个节点，即节点 I。

![](img/a4bdeb7f90423c1d3f3708701bc43a78.png)

我们可以很快看到，顶点 I 没有任何其他左子树。我们可以安全地把它划掉。

![](img/f5431902fe6b6fd80d2411ee8b8f5160.png)

然后我们返回到访问/打印节点 I。

![](img/a3ef0f924a25f5b1408a1306d95626aa.png)

试图访问节点 I 的右子树，但是顶点 I 没有右子树。右边的子树标签被划掉，我们返回到节点 d。

![](img/ea7801c8c60b1ba349543d72a8cad99b.png)

我们完成了节点 D 的右子树，所以我们返回到 B。在访问了它的左子树之后，我们现在正在访问节点 B。我们可以将节点 B 添加到输出中。

![](img/f5da4490b911571e2d2f9ac6a341c207.png)

我们移动到节点 B 的右子树。节点 B 在其右子树中只有一个节点，即节点 e。

![](img/6943afb57323e925f44c335cabd8a049.png)

我们试图访问节点 E 的左子树，但是它没有左子树。一旦我们划掉左边的子树标签，我们就访问顶点 E 并打印出它的值。

![](img/ae6165dcd0428055d19c41f269ee09d2.png)

我们试图访问节点 E 的右子树，但是它没有右子树。我们回到顶点 b。

![](img/0732ea9eace3a32506c84e5cdf540335.png)

我们完成了 B 的右子树，所以我们返回到节点 A。由于我们在访问了节点 A 的左子树之后又访问了节点 A，所以我们将把节点 A 添加到输出中。

![](img/ce2515a4448aa4d25d88337141bf9c53.png)

我们接下来访问节点 A 的右子树。

![](img/873c353205c14ffea13335954e0cb85e.png)

根据算法，我们必须先访问节点 C 的左子树。

![](img/755ff59d0a6d456e66c21156fe02970f.png)

由于节点 F 没有左子树，我们返回到节点 F 并打印出它的值。

![](img/ee602386c9887c37e976074ecc6d1bd4.png)

节点 F 没有右边的子树，所以我们返回到节点 C。因为我们在访问了节点 C 左边的子树之后又返回到节点 C，所以我们将节点 C 添加到输出中。

![](img/7c1efc4cdea86f54b665f466a9de7b65.png)

该算法要求我们接下来访问节点 C 的右子树。

![](img/a0910d319dee1f931b7b493c0da5ec20.png)

试图访问节点 G 的左子树。由于节点 G 没有左子树，我们返回到节点 G 并打印出来。

![](img/730a3dc177b057a04270f73a8419b1d8.png)

我们试图访问 G 的右子树，但是它没有，所以我们返回到节点 c。

![](img/90a722488999c9caca415ba91e28e8ca.png)

我们完成了节点 C 的右子树，所以我们返回到节点 a。

![](img/93cca846a6caf80c95beb3de8f80300a.png)

既然我们在访问了它的右子树之后已经回到了根节点，那么算法就结束了。前序和后序遍历遵循类似的准则。前序二叉树遍历首先显示节点，然后访问左边的子树，接着是右边的子树。后序二叉树遍历访问左右子树，然后输出节点。

如果你喜欢你所读的，我的书，[](https://www.amazon.com/Illustrative-Introduction-Algorithms-Dino-Cajic-ebook-dp-B07WG48NV7/dp/B07WG48NV7/ref=mt_kindle?_encoding=UTF8&me=&qid=1586643862)**【算法的说明性介绍】，涵盖了这个算法和更多。**

*![](img/cc9789c47529ad8dd22ee833f64f0c82.png)*

*Dino Cajic 目前是 [LSBio(寿命生物科学公司)](https://www.lsbio.com/)、[绝对抗体](https://absoluteantibody.com/)、 [Kerafast](https://www.kerafast.com/) 、 [Everest BioTech](https://everestbiotech.com/) 、 [Nordic MUbio](https://www.nordicmubio.com/) 和 [Exalpha](https://www.exalpha.com/) 的 IT 负责人。他还是我的自动系统公司的首席执行官。他有十多年的软件工程经验。他拥有计算机科学学士学位，辅修生物学。他的背景包括创建企业级电子商务应用程序、执行基于研究的软件开发，以及通过写作促进知识的传播。*

*你可以在 [LinkedIn](https://www.linkedin.com/in/dinocajic/) 上联系他，在 [Instagram](https://instagram.com/think.dino) 上关注他，或者[订阅他的媒体出版物](https://dinocajic.medium.com/subscribe)。*

*阅读 Dino Cajic(以及 Medium 上成千上万的其他作家)的每一个故事。你的会员费直接支持迪诺·卡吉克和你阅读的其他作家。你也可以在媒体上看到所有的故事。*