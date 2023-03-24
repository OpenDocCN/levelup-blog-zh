# JavaScript 中位运算的 7 个实际例子

> 原文：<https://levelup.gitconnected.com/7-practical-examples-of-bitwise-operation-in-javascript-342216522674>

itwise 操作对每个程序员来说都是一个强大的工具，因为它们允许你操作一个值中的单个位。这在各种情况下都很有用。例如，操作通常更快，但它也带来了难以理解的代价。所以，了解 JavaScript 中的位运算是如何工作的很重要。也知道按位运算应该属于每个软件开发人员的技能。

通常情况下，这些例子是相当“学术”的，像掩盖一点，设置，或清除一点。这些例子太抽象了，至少在我们看来是这样。尽管我们认识到首先理解位运算的基础很重要。因此，我们在前面的中发表了一篇关于位运算的[基础的文章。然而，在这篇文章中，我们希望收集实际的例子，使按位更容易理解。](https://medium.com/gitconnected/basics-of-bitwise-operations-in-javascript-with-practical-examples-6197b4d391b3)

这只是众多关于 JavaScript 和软件开发的文章中的一篇。欢迎关注我们，获取更多关于 JavaScript 和软件开发的精彩内容。我们尝试一周发布多次。请确保不要错过我们的任何精彩内容。

![](img/f4e50e99e9a01458656f77db63dbfed5.png)

马特·阿特兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

通过利用按位运算对数字的二进制表示进行操作，可以在 JavaScript 中以意想不到的方式使用按位运算。下面是一些在实际应用中使用按位运算的例子:

## 实现“穷人的”模运算符

您可能已经使用了模运算符。使用按位 AND 运算符(&)，您可以实现一个适用于 2 的幂除数的“穷人”模运算符。例如，要使用按位 AND 运算符计算 x % 8，可以执行以下操作:

```
let result = x & 7;
```

这比使用%运算符更快，尤其是对于大数，因为它避免了除法运算。但是，它仅适用于 2 的幂的约数，并且结果可能与对其他约数使用%运算符不同。

## 不使用临时变量交换值

您可以使用按位 XOR 运算符(^)来交换两个变量 x 和 y 的值，而无需使用临时变量。例如:

```
let x = 42;
let y = 13;

x ^= y;
y ^= x;
x ^= y;
console.log(x); // Output: 13
console.log(y); // Output: 42
```

## 寻找两个值的最小值或最大值

您可以使用按位 OR (|)和按位 AND (&)来查找两个值的最小值或最大值，而无需使用比较运算符。这是可行的，因为对两个值进行“或”运算将导致在每个比特位置具有 1 的值，其中至少一个原始值具有 1，而对两个值进行“与”运算将导致在每个比特位置具有 1 的值，其中两个原始值都具有 1。这里有一个例子:

```
function min(x, y) {
 return y & ((x - y) >> 31) | x & (~(x - y) >> 31);
}
function max(x, y) {
 return x & ((x - y) >> 31) | y & (~(x - y) >> 31);
}
console.log(min(10, 20)); // Output: 10
console.log(max(10, 20)); // Output: 20
```

## 确定一个数是奇数还是偶数

要在 JavaScript 中使用按位运算来确定一个数字是奇数还是偶数，可以使用按位 AND 运算符(&)来检查数字的最低有效位(LSB)的值。LSB 是数字的二进制表示中最右边的位，它代表数字的模 2 值。例如:

```
function isOdd(num) {
  return (num & 1) === 1;
}

function isEven(num) {
  return (num & 1) === 0;
}

console.log(isOdd(5));  // Output: true
console.log(isEven(5)); // Output: false
console.log(isOdd(6));  // Output: false
console.log(isEven(6)); // Output: true
```

在此示例中，isOdd 函数使用按位 AND 运算符检查数字的最低有效位是否为 1。如果是，这个数是奇数。isEven 函数做同样的事情，但是检查最低有效位是否为 0。

值得注意的是，这种方法只适用于整数。如果您需要检查浮点数是奇数还是偶数，您将需要使用不同的方法。

## 检查一个数是否是 2 的幂

您可以使用按位 AND 运算符(&)来检查数字 n 是否是 2 的幂。如果 n 是 2 的幂，那么 n &(n-1)将是 0。例如:

```
if ((n & (n - 1)) === 0) {
 console.log(`${n} is a power of 2`);
} else {
 console.log(`${n} is not a power of 2`);
}
```

我希望这些例子能给你一些在 JavaScript 中以意想不到的方式使用位运算的想法。关于这个话题你还有其他问题吗？

## 计算一个数 x 是否能被数 y 整除

一种方法是使用按位 AND 运算符(&)和移位来检查 x 除以 y 的余数是否为 0。

例如，假设您想检查 x 是否能被 4 整除。您可以执行以下操作:

```
if ((x & 3) === 0) {
 console.log(`${x} is divisible by 4`);
} else {
 console.log(`${x} is not divisible by 4`);
}
```

表达式(x & 3)将计算 x 除以 4 的余数，因为 4 等于 2，而 2 — 1 = 3。如果余数是 0，那么 x 可以被 4 整除。

这种方法只适用于 2 的幂的除数，因为按位 AND 运算符只适用于数字的二进制表示。对于其他除数，您需要使用不同的方法，例如使用模运算符(%)或整数除法(/)。

下面是一个更通用的实现，您可以用它来通过位运算检查数字 x 是否能被数字 y 整除:

```
function isDivisible(x, y) {
 let t = y;
 let s = 0;
// Calculate t = y * 2^s such that t is a power of 2
 while ((t & (t - 1)) !== 0) {
 t &= (t - 1);
 s++;
 }
// Check if x is divisible by t
 return (x & (t - 1)) === 0;
}
```

该函数的工作方式是首先找到大于或等于 y 的 2 的最小幂，然后使用按位 and 运算符检查 x 是否能被 2 的幂整除。

例如，要检查 x 是否能被 3 整除，可以调用 isDivisible(x，3)。如果 x 能被 3 整除，则返回 true，否则返回 false。

## 实现布隆过滤器

我们之前发表了一篇关于 bloom filter 的[文章。布隆过滤器是用于确定元素是否是集合成员的概率数据结构。它要么返回一个元素肯定不在集合中(意味着没有假阴性)，要么可能在一个集合中(可能是假阳性)。它可能会返回误报，这使得它成为一个概率数据结构。](https://medium.com/@pandaquests/bloom-filter-in-javascript-24c64b48f8d2)

```
class BloomFilter {
 constructor(size, hashFunctions) {
   this.size = size;
   this.hashFunctions = hashFunctions;
   this.bits = new Array(size).fill(0);
 }
 add(item) {
  for (let i = 0; i < this.hashFunctions.length; i++) {
   const hashValue = this.hashFunctions[i](item);
   const index = hashValue % this.size;
   this.bits[index] |= 1 << hashValue;
  }
 }
 contains(item) {
  for (let i = 0; i < this.hashFunctions.length; i++) {
   const hashValue = this.hashFunctions[i](item);
   const index = hashValue % this.size;
   if ((this.bits[index] & 1 << hashValue) === 0) {
    return false;
   }
  }
  return true;
 }
}
```

它使用不同位运算的组合，即位运算或和左移。基本上，它通过应用| (OR)操作符来计算散列值并设置位。钻头的位置由<< (left shift) operator and the value of the hash .

It has many applications, e.g. network traffic filtering, spell checking, cache invalidation, password checker, and so on. [设定，更多细节参见本文](https://pandaquests.medium.com/bloom-filter-in-javascript-24c64b48f8d2)。我们还[在这里实现了一个密码检查器](https://github.com/pandaquests/passwordChecker/blob/main/passwordChecker.js)来举例说明布隆过滤器。

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

你知道更多使用位运算的实际例子吗？我们一直在寻找更实用的按位运算的例子，因为我们认为这是一个有趣的话题，将提高你作为软件开发人员的技能。请随时联系我们。我们喜欢收到你的来信。

如果你喜欢这个内容，那么别忘了关注我们。我们每周发表多篇文章。所以，不要错过任何一个。再见