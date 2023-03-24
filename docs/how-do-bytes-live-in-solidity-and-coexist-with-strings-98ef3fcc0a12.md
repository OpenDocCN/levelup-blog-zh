# 字节如何在坚固中生存并与字符串共存

> 原文：<https://levelup.gitconnected.com/how-do-bytes-live-in-solidity-and-coexist-with-strings-98ef3fcc0a12>

![](img/fe6badabb987f8e71ed36e40d6fd4ca9.png)

照片由 [amirali mirhashemian](https://unsplash.com/@amir_v_ali?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

字节和字符串在 Solidity 编程语言中有着特殊的地位。这两种类型的工作方式非常相似，但又有所不同。偶数字节有两种方式——固定大小或动态大小。根据我们的需要，字节和字符串可以相互转换。让我们在这篇博文中更深入地了解一下。

# 固定大小的字节数组

如果我们知道想要存储的字节大小，最好的方法是使用固定大小的字节数组类型。Solidity 语言允许在一个序列中存储 1 到 32 个字节。就汽油费而言，使用已知的前期大小来存储字节的成本要便宜得多。

为了初始化一个固定大小的字节数组，我们需要指定我们想要存储多少字节的大小。

```
bytes1 b1 = hex"41";
```

字节可以用十六进制字符串`hex"41"`或十六进制值`0X41`初始化，根据 ASCII，该值是字母`A`。

另一个重要的注意事项是，在 Solidity 编程语言中，固定大小的字节可以在智能契约之间传递。

# 动态大小字节数组

动态大小字节数组可以保存可变的字节数。

有可能得到字节的索引，但字符串类型不是这样。

```
function dynamicBytesMemoryNew() external view returns(bytes memory) {
  bytes memory _dynamicBytesMemoryNew = new bytes(200);
  _dynamicBytesMemoryNew[0] = hex"41";
  console.log(string(_dynamicBytesMemoryNew));
  return _dynamicBytesMemoryNew;
}
```

Solidity 语言在内存中分配字节有几种方法。

# 存储中的动态大小字节数组

当在存储器上分配动态字节时，我们需要提供我们想要的最大容量。

```
bytes _dynamicBytesStorage = new bytes(200);
```

如果我们在存储器中分配一个动态大小的字节数组，我们可以使用`pop`和`push`方法。请记住，这样做的代价可能会很高。

```
function dynamicBytesStorage() external returns(bytes memory) {
  _dynamicBytesStorage.push(hex"41");
  console.log(string(_dynamicBytesStorage));
  return _dynamicBytesStorage;
}
```

# 内存中动态大小字节数组

当在内存中分配动态字节数组时，我们不需要指定大小。

```
function dynamicBytesMemory() external view returns(bytes memory) {
  bytes memory _dynamicBytesMemory = hex"41";

  console.log(string(_dynamicBytesMemory));
  return _dynamicBytesMemory;
}
```

# 字符串是字节(几乎)

Solidity 中的字符串类型几乎就像字节的动态数组。它可以保存任意长度的字符数据。这一次我们不会更深入地讨论这种类型，而是从字节的角度来看它。

我们可以将任何字符串转换成字节。这允许我们在字节级别上修改和读取字符。这意味着我们需要处理 ASCII 级别的字符。

一个关键的注意事项是字符串不能在智能合约之间传递。为此，我们需要深入到字节级别。但是在这些类型之间进行转换是可行的。

# 转换为字节

将字符串转换成字节是一项简单的任务。我们需要初始化传入字符串类型的字节。作为回报，我们得到一个动态字节数组。

```
bytes memory stringBytes = bytes("This is string");
```

如果我们想转换成`bytes32`类型，我们需要进入汇编级，将字符串写入内存。

```
bytes32 result;

assembly {
  result := mload(add("This is string", 32))
}
```

请记住，我们最多只能写入 32 个字节。

# 从字节转换

在 Solidity 语言中，我们可以将字符串值转换回动态大小的字节数组。我们无法转换为固定字符串字节，因为字符串类型的大小未知。

```
bytes memory bytesData = hex"41";
string memory stringData = string(bytesData);
```

# TL；速度三角形定位法(dead reckoning)

字节在 Solidity 编程语言中起着至关重要的作用。它可以在固定或动态大小的字节数组中保存任何数据。例如，与字节非常相似但功能有限的字符串。幸运的是，字符串和字节之间的转换是可行的，但有一些小的注意事项。

# 链接

*   [样本代码](https://gist.github.com/fassko/548c83158f4006c38e70e5dceed2a8cb)
*   [固定长度字节数组](https://solang.readthedocs.io/en/latest/language/types.html#fixed-length-byte-arrays)
*   [动态长度字节](https://solang.readthedocs.io/en/latest/language/types.html#dynamic-length-bytes)
*   [坚实度教程:关于字节的一切](https://jeancvllr.medium.com/solidity-tutorial-all-about-bytes-9d88fdb22676)