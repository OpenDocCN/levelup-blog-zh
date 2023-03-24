# 科特林:喜欢清晰胜过简洁

> 原文：<https://levelup.gitconnected.com/kotlin-prefers-clarity-over-concise-a5a2088536b6>

![](img/c9ed592e325b961d3ba202f0ba7862b5.png)

图片由[元素 5 数码](https://unsplash.com/@element5digital)在 [Unsplash](https://unsplash.com/photos/OyCl7Y4y0Bk) 上显示

K otlin 为我们提供了很多便利的工具，比如`takeIf`等。它可以帮助使代码更加简洁，删除临时变量等。

[](https://medium.com/@elye.project/using-kotlin-takeif-or-takeunless-c9eeb7099c22) [## 使用 Kotlin takeIf(或 takeUnless)

### 在科特林的标准函数中，有两个函数，即 takeIf 和 takeUnless，乍一看，这是什么…

medium.com](https://medium.com/@elye.project/using-kotlin-takeif-or-takeunless-c9eeb7099c22) 

然而，如果我们对此投入过多，就会使代码的可读性变得复杂。

以下面的为例。

```
savedStateHandle.get<String>(KEY).*takeIf* **{** !**it**.*isNullOrEmpty*()
**}**?.*let* **{** setValue(**it**) **}** ?: removeValue()
```

这样做可能感觉很棒，因为它:

*   消除 if-else
*   将代码链接在一起
*   消除临时变量

然而，这种代码的缺点是:

1.  理解代码的流程需要一些时间。它掩盖了代码背后的简单逻辑。
2.  如果我们反编译代码，它会生成大量的临时变量和逻辑，如下所示。

```
Object var2 = this.savedStateHandle.get("Key");
boolean var3 = false;
boolean var4 = false;
String it = (String)var2;
int var6 = false;
CharSequence var7 = (CharSequence)it;
boolean var8 = false;
boolean var9 = false;
String var10000 = (String)(var7 != null && var7.length() != 0 ? var2 : null);
if (var10000 != null) {
   String var10 = var10000;
   var3 = false;
   var4 = false;
   var6 = false;
   Intrinsics.*checkExpressionValueIsNotNull*(var10, "it");
   this.setValue(var10);
} else {
   this.removeValue();
}
```

## 简化版

让我们回到旧的 Java 写作方式，简单的 IF-ELSE。看起来没那么优雅，但是一看，一切都明白了。

```
val savedMessage = savedStateHandle.get<String>(KEY)
if (savedMessage.*isNullOrBlank*()) {
    removeValue()
} else {
    setValue(savedMessage)
}
```

此外，如果我们反编译，它看起来也很整洁，只有很少的生成变量。

```
String savedMessage = (String)this.savedStateHandle.get("Key");
CharSequence var3 = (CharSequence)savedMessage;
boolean var4 = false;
boolean var5 = false;
if (var3 == null || StringsKt.*isBlank*(var3)) {
   this.removeValue();
} else {
   this.setValue(savedMessage);
}
```

**使用**

当然，如果我们愿意，我们可以用`when`对它进行一点改进，与 IF-ELSE 相比，缩短一行

```
val savedMessage = savedStateHandle.get<String>(KEY)
when {
    savedMessage.*isNullOrBlank*() -> removeValue()
    else -> setValue(savedMessage)
}
```

反编译的代码看起来也不错。

```
String savedMessage = (String)this.savedStateHandle.get("Key");
CharSequence var3 = (CharSequence)savedMessage;
boolean var4 = false;
boolean var5 = false;
if (var3 == null || StringsKt.*isBlank*(var3)) {
   this.removeValue();
} else {
   this.setValue(savedMessage);
}
```

**使用**

但是如果我们需要对临时变量`savedMessage`进行编码，我们可以使用`run`(或者其他作用域函数)来消除它。像下面这样…

```
savedStateHandle.get<String?>(KEY).*run* **{** when {
        this.*isNullOrBlank*() -> removeValue()
        else -> setValue(this)
    }
**}**
```

反编译后的代码如下所示。没那么好，但还是比第一部好。

```
Object var2 = this.savedStateHandle.get("Key");
boolean var3 = false;
boolean var4 = false;
String $this$run = (String)var2;
int var6 = false;
CharSequence var7 = (CharSequence)$this$run;
boolean var8 = false;
boolean var9 = false;
if (var7 == null || StringsKt.*isBlank*(var7)) {
   this.removeValue();
} else {
   this.setValue($this$run);
}
```

总之，在这些例子中，我最喜欢这个:

```
val savedMessage = savedStateHandle.get<String>(KEY)
when {
    savedMessage.*isNullOrBlank*() -> removeValue()
    else -> setValue(savedMessage)
}
```

即使它不是最简洁的，并且需要一个临时变量，它仍然是最容易理解的。反编译的代码也很简洁。

所以我的代码审查规则是:**比简洁更喜欢清晰**

感谢阅读。你可以在这里查看我的其他话题[。](https://medium.com/@elye.project/)

关注我的[](https://medium.com/@elye.project)**[*Twitter*](https://twitter.com/elye_project)*[*脸书*](https://www.facebook.com/elyeproj/) 或 [*Reddit*](https://www.reddit.com/user/elyeproj/) 获取移动开发等相关话题的小技巧和学习。~Elye~***