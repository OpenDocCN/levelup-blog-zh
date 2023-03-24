# 如何获取智能合同创建块编号

> 原文：<https://levelup.gitconnected.com/how-to-get-smart-contract-creation-block-number-7f22f8952be0>

## 使用二分搜索法算法获得智能合同创建块

![](img/9ef9e07687c0a08628ec5f43a1d99a18.png)

照片由[思想目录](https://unsplash.com/@thoughtcatalog?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在 StackOverflow 上有很多关于如何获得智能合约的创建块的讨论。

*   [如何获得一份合同的“出生块”？](https://ethereum.stackexchange.com/questions/36173/how-to-get-the-birth-block-of-a-contract)
*   [如何获取 erc20 合同部署区块号？](https://ethereum.stackexchange.com/questions/53322/how-to-get-erc20-contract-deploy-time-block-number)
*   [部署合同时如何找到区块](https://ethereum.stackexchange.com/questions/45300/how-to-find-the-block-when-contract-was-deployed)
*   [如何使用 web3.py 找到给定合同地址的创建块号](https://stackoverflow.com/questions/72489211/how-to-find-the-creation-block-number-for-a-given-contract-address-using-web3-py?rq=1)

然而，它们中的大多数都没有提供详细的解决方案。在本文中，我将向您展示如何使用 Go 和以太坊获得智能合约的创建块。

这里是完整的[代码](https://gist.github.com/jerryan999/56ffce6571cfd8ef06e96d580b252b32)

# 怎么

没有直接获得智能合同创建块的方法。我们需要从头到尾扫描每个块，以找到创建契约的块。

但是这种线性扫描太慢，更可行的方法是使用**二分搜索法**找到创建合同的块号。

下一个问题是如何确定一个区块是合同的创建区块。

我们可以使用合同代码的长度来帮助。如果代码长度大于 2，则合同已经创建。否则，它还没有被创建。

现在是代码时间！

# 什么

## 实例化

下面是`ContractFinder`的核心**结构**以及如何实例化它:

```
type ContractFinder struct {
    client      *ethclient.Client
    latestBlock int64
}

func NewContractFinder(provider string) (*ContractFinder, error) {
    conn, err := ethclient.Dial(provider)
    if err != nil {
        return nil, err
     }

    // get latest block number for reuse later
    latestBlock, err := conn.BlockByNumber(context.Background(), nil)
    if err != nil {
        return nil, err
     }

    return &ContractFinder{
        client:      conn,
        latestBlock: latestBlock.Number().Int64(),
        }, nil
}
```

我们需要知道最新的块号才能使用二分搜索法。可以通过调用客户端的`BlockNumber`方法从提供者处获得。

另外，请注意，我们将这个数字存储在`ContractFinder`结构中，以便在某个时间段内不需要进一步的请求。

## 履行

```
func (c *ContractFinder) codeLen(contractAddr string, blockNumber int64) int {
    ctx := context.Background()
    data, err := c.client.CodeAt(ctx, common.HexToAddress(contractAddr), big.NewInt(blockNumber))
    if err != nil {
        log.Fatal("Whoops something went wrong!", err)
     }

    return len(data)
}

func (c *ContractFinder) GetContractCreationBlock(contractAddr string) int64 {
     return c.getCreationBlock(contractAddr, 0, c.latestBlock)
}

// binary search
func (c *ContractFinder) getCreationBlock(contractAddr string, startBlock int64, endBlock int64) int64 {
    if startBlock == endBlock {
       return startBlock
     }

    midBlock := (startBlock + endBlock) / 2
    codeLen := c.codeLen(contractAddr, midBlock)

    if codeLen > 2 {
       return c.getCreationBlock(contractAddr, startBlock, midBlock)
    } else {
       return c.getCreationBlock(contractAddr, midBlock+1, endBlock)
    }
}
```

`GetContractCreationBlock`是界面！

## 客户端部分

下面是如何使用上面的代码。

```
func main() {

   cttFinder, err := NewContractFinder("https://mainnet.infura.io/v3/[yourkey]")
   if err != nil {
      log.Fatal("Whoops something went wrong!", err)
   }

   contracts := []string{
    "0x3a4f40631a4f906c2bad353ed06de7a5d3fcb430",
    "0xa4991609c508b6d4fb7156426db0bd49fe298bd8",
   }

   for _, contract := range contracts {
      creationBlock := cttFinder.GetContractCreationBlock(contract)
      log.Println(contract, creationBlock)
   }
}
```

# 其他提示

## 限速

有时，您的节点 RPC 提供程序有一个速率限制策略。你需要添加一个限速器组件来避免被禁止。

```
type ContractCreationFinder struct {
     client *ethclient.Client
     rl     ratelimit.Limiter   // rate limit component
}

func (c *ContractCreationFinder) GetCodeLen(contractAddr string, blockNumber int64) int {
     // ...

     c.rl.Take()
     //...

}
```

## 同时找到创建块

如果我们想找到 1000 个契约的创建块，也许我们必须同时进行。

请随时查看 [Github](https://gist.github.com/jerryan999/56ffce6571cfd8ef06e96d580b252b32) 上的完整代码

我希望你喜欢读这篇文章😄。如果你想支持我☕作为一个作家，考虑注册[成为一个媒体成员](https://jerryan.medium.com/membership)。你还可以无限制地访问媒体上的每个故事。