# 代码气味 134 —专业商务系列

> 原文：<https://levelup.gitconnected.com/code-smell-134-specialized-business-collections-ce63d851792b>

## 如果它走路像鸭子，叫声像鸭子，那它一定是鸭子

![](img/286e6350602245c4f894976fe141e3aa.png)

> *TL；DR:不要创造不必要的抽象概念*

# 问题

*   过度设计
*   不需要的类

# 解决方法

1.  使用标准类

# 语境

在[映射器](https://medium.com/@mcsee/what-is-software-9a78c1172cf9)上发现抽象是一项艰巨的任务。

提炼之后，我们应该删除不必要的抽象。

# 示例代码

## 错误的

```
<?phpNamespace Spelling;final class Dictionary { private $words;
    function __construct(array $words) {
        $this->words = $words;
    } function wordsCount(): int {
        return count($this->words);
    } function includesWord(string $subjectToSearch): bool {
        return in_array($subjectToSearch, $this->words);
    }
}//This has protocol similar to an abstract datatype dictionary
//And the testsuse PHPUnit\Framework\TestCase;final class DictionaryTest extends TestCase {
    public function test01EmptyDictionaryHasNoWords() {
        $dictionary = new Dictionary([]);
        $this->assertEquals(0, $dictionary->wordsCount());
    } public function test02SingleDictionaryReturns1AsCount() {        
        $dictionary = new Dictionary(['happy']);
        $this->assertEquals(1, $dictionary->wordsCount());
    } public function test03DictionaryDoesNotIncludeWord() {
        $dictionary = new Dictionary(['happy']);
        $this->assertFalse($dictionary->includesWord('sadly'));
    } public function test04DictionaryIncludesWord() {
        $dictionary = new Dictionary(['happy']);
        $this->assertTrue($dictionary->includesWord('happy'));
    }
}
```

## 对吧

```
<?phpNamespace Spelling;// final class Dictionary is no longer needed//The tests use a standard class 
//In PHP we use associative arrays
//Java an other languages have HashTables, Dictionaries etc. etc.use PHPUnit\Framework\TestCase;final class DictionaryTest extends TestCase {
    public function test01EmptyDictionaryHasNoWords() {
        $dictionary = [];
        $this->assertEquals(0, count($dictionary));
    } public function test02SingleDictionaryReturns1AsCount() {
        $dictionary = ['happy']; 
        $this->assertEquals(1, count($dictionary));
    } public function test03DictionaryDoesNotIncludeWord() {
        $dictionary = ['happy']; 
        $this->assertFalse(in_array('sadly', $dictionary));
    } public function test04DictionaryIncludesWord() {
        $dictionary = ['happy'];  
        $this->assertTrue(in_array('happy', $dictionary));
    }
}
```

# 侦查

[X]半自动

基于协议，我们应该删除一些不必要的类

# 标签

*   协议

# 例外

如果我们有足够有力的证据，有时我们需要出于性能原因优化集合。

# 结论

我们需要不时地清理代码。

专门化的收藏是一个很好的起点。

# 关系

[](https://blog.devgenius.io/code-smell-111-modifying-collections-while-traversing-b453783ab983) [## 代码味道 111 —遍历时修改集合

### 在遍历时更改集合可能会导致意外错误

blog.devgenius.io](https://blog.devgenius.io/code-smell-111-modifying-collections-while-traversing-b453783ab983) 

# 更多信息

*   [鸭子打字](https://en.wikipedia.org/wiki/Duck_typing)

# 信用

照片由 [Pisit Heng](https://unsplash.com/@pisitheng) 在 Unsplash 上拍摄

> *软件行业的大部分工作都是维护已经存在的代码。*

*Wietse Venema*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)