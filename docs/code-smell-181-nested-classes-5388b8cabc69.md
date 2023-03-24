# 代码味道 181 —嵌套类

> 原文：<https://levelup.gitconnected.com/code-smell-181-nested-classes-5388b8cabc69>

## 嵌套类或伪私有类似乎非常适合隐藏实现细节。

![](img/186fe91d35c1c5a7caec7371a3e17e60.png)

> *TL；DR:不要使用嵌套类*

# 问题

*   [双射](https://codeburst.io/the-one-and-only-software-design-principle-5328420712af)对现实世界概念的断层。
*   缺乏可测试性
*   缺乏重用
*   范围和[名称空间复杂性](https://stackoverflow.com/questions/47452783/code-style-and-smells-nested-classes)

# 解决方法

1.  公开该类
2.  将公共类放在您自己的名称空间/模块下。
3.  用一个面向外部世界的门面来隐藏它。

# 语境

一些语言允许我们创造只存在于更有意义的想法中的私人概念。

这些类更难测试、调试和重用。

# 示例代码

## 错误的

```
class Address {
  String description = "Address: ";

  public class City {
    String name = "Doha";
  }
}
public class Main {
  public static void main(String[] args) {
    Address homeAddress = new Address();
    Address.City homeCity = homeAddress.new City();
    System.out.println(homeAddress.description + homeCity.name);
  }
}
// The output is "Adress: Doha"
//
// If we change privacy to 'private class City' 
//
// We get an error " Address.City has private access in Address"
```

## 对吧

```
class Address {
  String description = "Address: ";
}

class City {
    String name = "Doha";
  }
public class Main {
  public static void main(String[] args) {
    Address homeAddress = new Address();
    City homeCity = new City();
    System.out.println(homeAddress.description + homeCity.name);
  }
}
// The output is "Adress: Doha"
//
// Now we can reuse and test City concept
```

# 侦查

[X]自动

既然这是一种语言特性，我们就可以检测到它，避免它的使用。

# 标签

*   等级制度

# 结论

许多功能因功能复杂而臃肿。

我们很少需要这些新的流行文化特征。

我们需要保持一个最小的概念集。

# 更多信息

*   [W3 学校](https://www.w3schools.com/java/java_inner_classes.asp)

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由 [Dana Ward](https://unsplash.com/@danaward) 在 [Unsplash](https://unsplash.com/s/photos/spiral) 上拍摄

> 开发人员被复杂性所吸引，就像飞蛾扑火一样，结果往往是一样的。

尼尔·福特

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)